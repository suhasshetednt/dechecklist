# DoD Rollout — Jira Admin Handoff Checklist

**Project:** ADL · **Site:** aslairlines.atlassian.net · **Time:** ~45 min
**Full details for any step:** see `ADMIN-SETUP-GUIDE.md`. **Checklist app lives at:** https://suhasshetednt.github.io/dechecklist/

---

### Jira (site admin)
- [ ] **1. Group** — create group `data-engineering`; add Suhas Shete, Dhruv Mevada (`dhruv.m@dntinfotech.com`), Vansh Nautiyal (`vansh.n@dntinfotech.com`).
- [ ] **2. App** — install **Smart Checklist for Jira** (Marketplace).
- [ ] **3. Custom fields** (Settings → Issues → Custom fields), add to ADL screens:
  - [ ] `Tech Components Touched` — Checkboxes: Talend, Python, SQL, Dremio PDS/VDS, Reflection, CDC, API/Xero, S3, Azure Data Lake
  - [ ] `Affects Reports / Semantic Layer?` — Radio: Yes, No
  - [ ] `Unit Tests` — Select: Not Run, Passed, Failed
  - [ ] `Test Evidence` — URL
  - [ ] `QA Sign-off` — Select: Pending, Done
- [ ] **4. Field IDs** — note the `customfield_XXXXX` ids for Unit Tests / Test Evidence / QA Sign-off → give to GitHub admin (run `deploy/list_jira_fields.py` or read from each field's URL).
- [ ] **5. Templates** — in Smart Checklist, create templates from `smart-checklist-templates.md` (DE-CORE, DE-UNIT-TESTING, DE-PROMOTION, DE-SQL, DE-TALEND, DE-PYTHON, DE-DREMIO, DE-REFLECTION, DE-CDC, DE-API, DE-STORAGE).
- [ ] **6. Link auth** — generate an API token for the **existing automation user**; store `DOD_LINK_BASIC_AUTH` = base64(`email:token`) as an Automation **secret**; set link rules' Actor = that user.
- [ ] **7. Automation rules** — build the 7 rules from `jira-automation-rules.json` (Project settings → Automation). Every rule scoped with: `project = ADL AND assignee in membersOf("data-engineering")`. Includes: inject checklist, block Done until mandatory done, require green CI, auto-create BI ticket, optional-item warning, **web link on create**, **web link sprint-sweep** (link URL already = the GitHub Pages page above).
- [ ] **8. (Optional) Workflow** — add gate statuses: Unit Testing → Ready for Review → Merged to TEST → QA/Test → UAT → Ready for PROD.

### GitHub (repo admin)
- [ ] **9. Secrets** — add Actions secrets from `deploy/dod.env.example` (incl. the field IDs from step 4; `JIRA_PROJECT_KEYS=ADL`).
- [ ] **10. Workflow + scripts** — copy `ci-github-actions.yml` → `.github/workflows/dod.yml`; copy `deploy/` and `dq_harness/`.
- [ ] **11. GitHub for Jira** — install the app, link the repo (commits/PRs/builds show on tickets; needed for the CI-status gate).
- [ ] **12. Branch protection** — create rulesets per `github-branch-protection.md` for main / test / uat / prod (enforces feature→main→test→uat→prod).
- [ ] **13. Commit hook** — each dev runs `git config core.hooksPath .githooks` (rejects commits without `ADL-123`).

### Verify on ONE ticket (do before team rollout)
- [ ] **14.** Create an ADL ticket assigned to a DE member, set Tech = Python+SQL → checklist auto-fills Core+UnitTesting+Promotion+Python+SQL; **Web Links** shows "📋 Definition of Done (DE)".
- [ ] **15.** Try to move it to **Done** with items unchecked → blocked. Push `feature/ADL-<n>-x` + PR → `merge-guard`/`unit-tests` run; `Unit Tests` flips to Passed, `Test Evidence` link appears. Tick items → Done allowed. ✅ Roll out.

---
**Owner:** Suhas Shete · **Questions:** see `JIRA-Integration-Design.md` for the rationale behind each gate.
