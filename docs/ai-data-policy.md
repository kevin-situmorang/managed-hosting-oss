# Hutabyte Antigravity — AI Data Minimization Policy

Status: DRAFT — must be reviewed by Hutabyte lead before LOI presentation
Version: 1.0 (2026-05-25)
Legal basis: UU PDP 2022 (Undang-Undang No. 27 Tahun 2022 tentang Pelindungan Data Pribadi)

---

## Purpose

This policy defines which data Hutabyte's AI systems may and may not access or transmit.
All AI components (support bot, deployment automation, AIOps classifier) interact with
client ERP systems. This policy is the binding constraint on what data crosses the
boundary to the Claude API (Anthropic, US-based).

The principle: **operational and configuration data only**. Personal data of any kind
— employees, customers, donors, vendors, fund recipients — never enters the AI pipeline.

---

## Scope

Applies to all three AI components:
- Phase 1: Telegram Support Bot (Claude API via support-bot/)
- Phase 2: Deployment Automation (Claude API via deploy/playbook_generator.py)
- Phase 3: AIOps Alert Classifier (Claude API via aiops/classifier.py)

---

## Odoo 17 CE — Allowed Fields (Whitelist)

Only these fields may be queried from the Odoo XML-RPC API and included in Claude API payloads.

| Model | Field | Purpose |
|---|---|---|
| ir.module.module | name | Module name |
| ir.module.module | state | installed / uninstalled / to install |
| ir.module.module | installed_version | Current version |
| ir.module.module | latest_version | Available version (for upgrade check) |
| ir.module.module | shortdesc | Human-readable module description |
| ir.logging | level | Log level (INFO / WARNING / ERROR) |
| ir.logging | message | Last 100 lines — redacted of amounts and names |
| ir.logging | func | Function that generated the log |
| ir.logging | path | File path |
| ir.logging | line | Line number |
| res.lang | name | Installed language packs |
| base.automation | name | Automation rule names (not record content) |
| ir.cron | name | Scheduled job names |
| ir.cron | nextcall | Next scheduled execution time |
| ir.cron | active | Whether cron is active |

**Performance metrics (collected via VPS-level monitoring, not Odoo API):**
- HTTP response time (ms) to Odoo web endpoint
- PostgreSQL query count (pg_stat_activity, query text excluded)
- Docker container CPU % and memory MB

---

## ERPNext — Allowed Fields (Whitelist)

Only these REST API resources and fields may be queried and included in Claude API payloads.

| Resource (REST endpoint) | Field | Purpose |
|---|---|---|
| /api/method/frappe.utils.get_installed_apps | app_name | Installed app list |
| /api/method/frappe.utils.get_installed_apps | version | App version |
| /api/resource/Installed Applications | installed | Installation status |
| /api/method/frappe.client.get_count (DocType: Error Log) | count | Error log count |
| /api/resource/Error Log | method | Function that raised error |
| /api/resource/Error Log | error (last 100 lines) | Error trace — redacted |
| /api/resource/Scheduler Log | method | Scheduled job name |
| /api/resource/Scheduler Log | status | success / failed |
| /api/resource/Scheduler Log | scheduled_time | When job ran |

**Performance metrics (VPS-level, not ERPNext API):**
- HTTP response time (ms) to ERPNext web endpoint
- MariaDB/PostgreSQL active connections (query text excluded)
- Docker container CPU % and memory MB
- frappe-bench worker queue depth (integer count only)

---

## Forbidden Data — Never Transmitted

The following data categories are permanently excluded from all AI pipelines.
Inclusion of any field from these categories is a PII violation and must cause an
immediate `PIIViolationError` with deployment blocked until resolved.

### Odoo — Forbidden Models

| Model | Reason |
|---|---|
| res.partner | Customer, vendor, donor records — contains name, email, phone, address |
| hr.employee | Employee records — NIK, salary, personal details |
| hr.payslip | Payroll data |
| account.move | Journal entries — financial transaction amounts |
| account.move.line | Individual debit/credit lines |
| project.task | Task content — assignee names, descriptions may contain PII |
| project.project | Project descriptions may reference clients or donors |
| sale.order | Sales orders — customer data |
| purchase.order | Purchase orders — vendor and amount data |
| account.payment | Payment records |
| res.users (fields) | email, login — user identity |

### ERPNext — Forbidden DocTypes

| DocType | Reason |
|---|---|
| Customer | Customer records — name, contact, tax ID |
| Supplier | Supplier records |
| Employee | Employee personal data — NIK, salary, contact |
| Salary Slip | Payroll data |
| GL Entry | General ledger — financial amounts |
| Sales Invoice | Customer and amount data |
| Purchase Invoice | Supplier and amount data |
| Payment Entry | Payment data |
| Stock Entry | May reference internal transfer descriptions |
| User | User identity — email, full name |

**Any field containing these values is forbidden regardless of model:**
- name, full_name, display_name (when referring to a person)
- email, phone, mobile, fax
- nik (Nomor Induk Kependudukan)
- address, street, city (when tied to a person or company record)
- amount, total, balance, debit, credit (financial figures)
- salary, wage, allowance, deduction

---

## Enforcement Layers

### Layer 1 — ERP Client Query Scope
`common/odoo_client.py` and `common/erpnext_client.py` only request fields from
the allowed whitelist above. Forbidden models are never called.

### Layer 2 — PII Firewall (Pre-Payload Check)
`common/pii_firewall.py` validates every dict before it is passed to the Claude API.
Any key not in the ALLOWED_FIELDS set raises `PIIViolationError` immediately.
The call to Claude API is aborted. The error is logged with the violating field name
(not value) for audit.

### Layer 3 — CI Gate
`.github/workflows/pii-firewall.yml` runs `pytest tests/test_pii_firewall.py` on
every pull request. Merge is blocked if any PII test fails.

### Layer 4 — Service Agreement Disclosure
The client service agreement (see service-agreement-template.md) must include a clause
disclosing the AI processing scope: what data is processed, for what purpose, and that
personal data is never transmitted to third-party AI services without explicit consent.

---

## Cross-Border Data Transfer

Anthropic's Claude API processes data in the United States (or the region specified in
Anthropic's DPA). Under UU PDP 2022 Pasal 56, cross-border personal data transfer
requires:

1. The destination country has equivalent data protection standards, OR
2. Explicit written consent from the data subject, OR
3. A data processing agreement (DPA) with the receiving party

**Hutabyte's position:** Only operational/configuration data (non-personal) is transmitted.
This does not constitute a personal data transfer under UU PDP 2022 and does not
require cross-border transfer consent. The PII firewall (Layer 2 above) is the
technical guarantee of this position.

If a client requests that the support bot access any personal data (e.g., "look up
employee X's payslip"), the bot must refuse and escalate to a human operator.

---

## Review and Updates

This policy must be reviewed:
- Before each new ERP platform is added to Hutabyte's supported stack
- When Odoo or ERPNext adds new API endpoints or models used by Hutabyte
- Annually, or when UU PDP 2022 implementing regulations are updated

Owner: Hutabyte lead engineer
Last reviewed: 2026-05-25
