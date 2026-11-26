# Going live on Oracle Cloud (T6.1)

Everything after "Create the server" is automated. Expect about 30 minutes in total, most of it
waiting for Oracle and the first build.

## 1. Account (once)
1. Sign up at https://signup.cloud.oracle.com. Home region: **India South (Hyderabad)** or
   **India West (Mumbai)** — it cannot be changed later.
2. A card is needed for the identity check; the Always Free tier is not charged.
3. Optional but recommended: **Billing → Upgrade to Pay As You Go**. Free resources stay free, Oracle
   gives such accounts better access to free capacity and does not reclaim idle free servers. Add a
   **Budget** of ₹100 with an email alert.

## 2. Network (once)
**Networking → Virtual cloud networks → Start VCN Wizard → Create VCN with Internet Connectivity**,
name `siliconics-vcn`. Then open its **public subnet → Default Security List → Add Ingress Rules**:
TCP port **80** and TCP port **443**, source `0.0.0.0/0`.

## 3. Create the server
**Compute → Instances → Create instance**
- Image: **Canonical Ubuntu 24.04** (aarch64). Shape: **Ampere VM.Standard.A1.Flex**, 4 OCPU / 24 GB
  (2 / 12 is enough if 4 is out of capacity).
- Networking: `siliconics-vcn`, its **public** subnet, **Assign a public IPv4 address** ticked.
- SSH key: paste the contents of `~/.ssh/siliconics_oracle.pub` from the Mac.
- "Out of capacity": retry later (early morning or late night), try 2 OCPU, or upgrade to Pay As You Go.

## 4. Domain
At https://www.duckdns.org sign in, add a subdomain (e.g. `siliconics`) and set its IP to the server's
public IP. Keep the DuckDNS token private.

## 5. Set up the server (one command)
From the Mac:
```bash
ssh -i ~/.ssh/siliconics_oracle ubuntu@<public-ip>
```
On the server (replace the name; the token keeps the DuckDNS address pointing at the server):
```bash
curl -fsSL https://raw.githubusercontent.com/raghavkhandelwal11/Siliconia_Backend/main/deploy/vm/bootstrap.sh | sudo DUCKDNS_TOKEN=<your-token> bash -s -- <name>.duckdns.org
```
It installs Docker, opens the firewall, downloads both repos to `/opt/siliconics`, writes a production
`.env` with fresh random secrets (only on the server), installs a nightly database backup, builds
and starts everything, and waits until `https://<name>.duckdns.org` is healthy.

Then, still on the server:
```bash
cd /opt/siliconics/siliconia_backend
make demo                                         # demo store, all 12 categories, a scheduled launch
make admin PHONE=<10 digits> NAME="<your name>"   # first admin; set up the authenticator at first sign-in
grep TEST_INBOX_PASSWORD deploy/compose/.env      # password to share with testers
```

## 6. Automatic deploys
In **both** GitHub repositories (Settings → Secrets and variables → Actions):
- Secrets: `VM_HOST` = public IP, `VM_USER` = `ubuntu`, `VM_SSH_KEY` = contents of `~/.ssh/siliconics_oracle`
  (the private key).
- Variable: `DEPLOY_ENABLED` = `true`.

From then on every merge to `main` that passes CI runs `deploy/vm/deploy.sh` on the server: it pulls
both repos, rebuilds, checks health and **rolls back automatically** if the new version is not healthy
within 3 minutes.

## Day to day

On the server, start and update with `deploy.sh`, **not** `make up` (that is the local setup: no web
app, ports 8080/8443). `make demo` and `make admin` are fine.

| Task | On the server |
|---|---|
| Deploy now | `bash /opt/siliconics/siliconia_backend/deploy/vm/deploy.sh` |
| Logs | `cd /opt/siliconics/siliconia_backend && docker compose -f deploy/compose/docker-compose.yml -f deploy/compose/docker-compose.prod.yml logs -f --tail=100` |
| Backups | `/opt/siliconics/backups` (last 7 nights, 02:45 IST); log in `/var/log/siliconics-backup.log` |
| Restore a backup | `gunzip -c <file> \| docker compose -f deploy/compose/docker-compose.yml exec -T postgres psql -U siliconia siliconia` |
