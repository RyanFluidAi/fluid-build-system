---
name: deploy-app
description: Deploy the application to staging or production. Use when deploying code, preparing a release, or troubleshooting environment drift.
disable-model-invocation: true
---

# Deploy App

## Non-negotiables

- Never deploy without required approvals (define your policy in `CLAUDE.md`).
- Prefer deterministic checks before deployment (tests/build/lint).
- Do not paste secrets into docs; use environment variables or secret managers.

## Quick reference

### Do

- Run fast checks before deploying.
- Verify the deployment succeeded (health checks + key user flows).
- Record what changed in the active sprint doc and/or release notes.

### Don't

- Don't deploy "blind" (no tests, no validation).
- Don't treat UI-only changes as authoritative if server-side contracts exist.

## Workflow

- [ ] Confirm target environment (staging vs production)
- [ ] Run deterministic checks (tests/build/lint)
- [ ] Deploy using your project's deployment mechanism (CI, scripts, platform)
- [ ] Verify health + main flows
- [ ] Update `docs/sprints/CURRENT_STATUS.md` if priorities changed
- [ ] Run a mini audit if canonical surfaces/contracts were affected

## Scripts

- `scripts/deploy.sh <environment>` (create this for your project)
- `scripts/verify.sh <environment>` (create this for your project)

## References

See [references/](references/) for deployment-specific documentation for your project.
