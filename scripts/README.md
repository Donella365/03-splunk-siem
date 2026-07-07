# Lab 3 Scripts

Infrastructure-as-Code for the Splunk SIEM lab. Run in order:

| Script | Run where | Purpose |
|---|---|---|
| `01-provision-infra.sh` | Local (Linux Mint) | Creates resource group, VNet, bidirectional peering with Lab 1, NSG rules, and the Splunk VM |
| `02-install-splunk.sh` | On the Splunk VM (via SSH) | Installs Splunk Enterprise, configures receiving on port 9997, creates the `windows_logs` index — all via CLI instead of clicking through the web UI |
| `03-configure-forwarder.ps1` | On `vm-actived` (via Remmina/RDP, PowerShell as Administrator) | Silently installs the Universal Forwarder, writes `inputs.conf`, restarts the service |
| `04-lockdown-rdp.sh` | Local (Linux Mint) | Remediation script — restricts `vm-actived`'s RDP rule to a single trusted IP. This is the actual script run to close the real RDP exposure found during this lab (see NOTES.md for the full incident) |

## What's scripted vs. what's manual (actual, not theoretical)

Everything above (Sections 2–5 of the SOP) was run via these scripts/CLI. **Sections 6–8 were done manually through the Splunk Web UI**, not scripted:

- **Section 6 (SPL searches)** — run directly in the Splunk search bar, not scriptable as "infrastructure" in the same sense
- **Section 7 (Windows Security Overview dashboard)** — built manually via Dashboards → Create New Dashboard → Add Panel, one panel at a time. See SOP Section 7 for the exact 4 panel searches and `screenshots/13-security-dashboard-top.png` / `14-security-dashboard-bottom.png` for the finished result.
- **Section 8 (brute-force alert)** — built manually via Save As → Alert on the search bar. See SOP Section 8 and `screenshots/15-alert-config-saved.png`.

Dashboards and alerts *can* be defined as code (Splunk's `savedsearches.conf` / `.conf` files, or the REST API), but that was out of scope for this pass — noted here as a possible future improvement rather than pretending it was scripted when it wasn't.

## Manual steps that can't be scripted

- **Splunk Enterprise / Universal Forwarder downloads** — both require registering on splunk.com (temp-mail address is fine — see SOP Section 3). No API/CLI path around this.
- **Splunk admin password on first login** — `02-install-splunk.sh` seeds a placeholder password (`ChangeMeImmediately123!`); change it immediately after first login.

## Prerequisites

- Azure CLI installed and authenticated (`az login`)
- Lab 1 (`vm-actived`, `RG-ACTIVED`, `vnet-centralindia-1`) already deployed
- Run `01-provision-infra.sh` from Linux Mint, `02` on the Splunk VM over SSH, `03` on `vm-actived` over RDP, `04` back on Linux Mint
