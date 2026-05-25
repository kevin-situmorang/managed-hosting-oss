# Hutabyte Antigravity — age Key Management Procedure

Status: DRAFT — both custodians must be named and sign off before first client goes live
Version: 1.0 (2026-05-25)
Tool: age (https://github.com/FiloSottile/age)

---

## Why This Document Matters

Hutabyte stores each client's ERP credentials (hostname, database name, API keys) in
age-encrypted YAML files. These files are committed to the repo. Without the age
private key, all client credentials are permanently unreadable — complete service
outage across all clients. This procedure defines how the key is protected, who can
access it, and what to do when things go wrong.

**Losing the age key = losing every client's credentials. There is no recovery path
without a backup.**

---

## Custodians

Two named people hold copies of the age private key. Both must be designated before
the first client is onboarded. The second custodian is not optional.

| Role | Name | Contact | Key Storage Method |
|---|---|---|---|
| **Custodian 1 (Primary)** | **[FILL IN — must be named before first client]** | [Email / phone] | 1Password / Bitwarden personal vault |
| **Custodian 2 (Backup)** | **[FILL IN — must be named before first client]** | [Email / phone] | 1Password / Bitwarden personal vault |

**Rules:**
- Custodians must be reachable within 4 hours during business hours
- If a custodian leaves Hutabyte, key rotation must happen within 30 days (see Rotation below)
- Neither custodian may share the key with any third party without written approval from the other custodian

---

## Key Generation

Run once. Do not re-run unless rotating the key (which requires re-encrypting all client configs).

```bash
# Generate a new age key pair
age-keygen -o ~/.age/hutabyte-prod.key

# The output file contains:
# - Public key (first line, starts with "age1...")
# - Private key (secret, keep safe)

# View the public key only (safe to share/commit)
age-keygen -y ~/.age/hutabyte-prod.key
```

The **public key** goes into the repo (in each per-client config encrypt command and in
this document). The **private key** is NEVER committed to any repository.

**Current production public key:**
```
[FILL IN after first key generation — e.g., age1abc123...]
```

---

## Encrypting a New Client Config

When onboarding a new client, create and encrypt their credential file:

```bash
# 1. Create plaintext client config (NEVER commit the plaintext)
cat > /tmp/newclient.yaml << EOF
erp_type: odoo          # or erpnext
host: newclient.hutabyte.id
database: newclient_db
api_key: <odoo-api-key>
api_secret: <odoo-api-secret>
erp_type_version: "17.0"
monitoring_id: uptimerobot-monitor-id
EOF

# 2. Encrypt with the production public key
age -r age1abc123... -o configs/clients/newclient.enc.yaml /tmp/newclient.yaml

# 3. Verify encryption worked (should output the YAML)
age -d -i ~/.age/hutabyte-prod.key configs/clients/newclient.enc.yaml

# 4. Commit the encrypted file only
git add configs/clients/newclient.enc.yaml
git commit -m "chore: add encrypted config for newclient"

# 5. Securely delete the plaintext
shred -u /tmp/newclient.yaml
```

---

## Decrypting a Client Config (Normal Operations)

The application decrypts configs at runtime using the private key. For manual access:

```bash
# Decrypt a specific client config
age -d -i ~/.age/hutabyte-prod.key configs/clients/newclient.enc.yaml

# Or pipe directly into a script
age -d -i ~/.age/hutabyte-prod.key configs/clients/newclient.enc.yaml | python3 -c "import yaml, sys; print(yaml.safe_load(sys.stdin))"
```

In production, the private key is loaded by the application from the path defined in
the environment variable `AGE_KEY_PATH` (default: `~/.age/hutabyte-prod.key`).
This path must exist on the central Hutabyte server and be readable only by the
service user (`chmod 600`).

---

## Encrypted Backup of the Private Key

The private key itself must be backed up in an encrypted form. Two copies:

### Backup 1 — Private GitHub Repository

```bash
# Encrypt the private key with a strong passphrase (different from the key itself)
# Store passphrase in each custodian's password manager
gpg --symmetric --cipher-algo AES256 ~/.age/hutabyte-prod.key

# This produces hutabyte-prod.key.gpg
# Commit ONLY the .gpg file to the private key backup repo:
# github.com/hutabyte/key-backup (private repo, restricted access)
```

### Backup 2 — Offline (Recommended)

Each custodian keeps a copy of the private key in their personal password manager
(1Password or Bitwarden) under the vault item named `hutabyte-age-prod-key`.

**Both backup copies must be updated every time the key is rotated.**

---

## Key Rotation Procedure

Rotate the key when:
- A custodian leaves Hutabyte
- Key is suspected to be compromised
- Annual rotation (recommended, not required)

**Rotation takes all client configs offline temporarily. Plan for a maintenance window.**

```bash
# Step 1: Generate new key pair
age-keygen -o ~/.age/hutabyte-prod-new.key
NEW_PUBKEY=$(age-keygen -y ~/.age/hutabyte-prod-new.key)

# Step 2: Re-encrypt all client configs with new public key
for f in configs/clients/*.enc.yaml; do
  # Decrypt with old key
  age -d -i ~/.age/hutabyte-prod.key "$f" > /tmp/plaintext.yaml
  # Re-encrypt with new key
  age -r "$NEW_PUBKEY" -o "$f" /tmp/plaintext.yaml
  shred -u /tmp/plaintext.yaml
  echo "Rotated: $f"
done

# Step 3: Replace old key file with new key
mv ~/.age/hutabyte-prod-new.key ~/.age/hutabyte-prod.key
chmod 600 ~/.age/hutabyte-prod.key

# Step 4: Update production public key in this document (line above)
# Step 5: Update encrypted backup in GitHub private repo
# Step 6: Update both custodians' password manager entries
# Step 7: Commit re-encrypted configs
git add configs/clients/*.enc.yaml
git commit -m "chore: rotate age key — all client configs re-encrypted"
```

---

## Emergency Recovery Procedure

### Scenario A — Primary custodian unavailable, key needed

1. Contact Custodian 2 immediately.
2. Custodian 2 retrieves key from their password manager vault.
3. Key is loaded onto the server via SSH with the minimum necessary access.
4. After incident resolves, assess whether key rotation is needed.

### Scenario B — Both custodians simultaneously unavailable

This should not happen if custodians are chosen carefully (different locations, different
employers, different personal circumstances). If it does:

1. Retrieve the GPG-encrypted backup from `github.com/hutabyte/key-backup`.
2. Decrypt using the backup passphrase (stored separately in the company password
   manager, accessible to the Hutabyte lead).
3. **Immediate action after recovery:** rotate the key and update all backups.

### Scenario C — Key confirmed compromised

1. Take all client-facing services offline immediately (revoke API keys at Odoo/ERPNext level).
2. Rotate the age key (procedure above).
3. Generate new Odoo/ERPNext API keys for all clients.
4. Re-encrypt all client configs with the new age key AND new ERP API keys.
5. Notify affected clients of the incident.

---

## Checklist Before First Client Goes Live

- [ ] Custodian 1 named: ___________________________
- [ ] Custodian 2 named: ___________________________
- [ ] Production key generated
- [ ] Production public key recorded in this document
- [ ] Private key backup in GitHub private repo (GPG-encrypted)
- [ ] Both custodians have key copy in personal password manager
- [ ] `AGE_KEY_PATH` environment variable set on production server
- [ ] Key file permissions: `chmod 600 ~/.age/hutabyte-prod.key`
- [ ] Decryption tested: at least one test config successfully decrypted
