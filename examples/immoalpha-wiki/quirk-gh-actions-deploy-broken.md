---
title: "Quirk: GH-Actions auto-deploy broken — deploy via script"
type: quirk
status: open
updated: 2026-07-16
sources: [memory hosting]
---

# Quirk: deploys run via `deploy-vps.sh`, GH Actions is dead

The **only working deploy path** is `./deploy/deploy-vps.sh` (SSH as root to the Hostinger VPS, Docker build there). The GitHub Actions auto-deploy has been **broken since May** (secrets) and was never repaired.

Consequences:
- Merging to main deploys NOTHING automatically — pushing is not deploying.
- Green/red Actions runs say nothing about the prod state.
- **TODO:** fix Actions or remove the workflow (source of confusion).

## Related
[[architecture-overview]] · [[decision-direct-to-main-prod]]
