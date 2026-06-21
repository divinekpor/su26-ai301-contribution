# Contribution 1: Semantic Versioning

**Contribution Number:** 1
**Student:** Divine Doamekpor
**Issue:** https://github.com/codeforpdx/tenantfirstaid/issues/175
**Status:** Phase II Complete

---

## Why I Chose This Issue

I chose this issue because it is a focused first contribution that still touches both sides of the application. The issue asks for semantic versioning support so the app has one clear version number. That matters because users, contributors, and maintainers should be able to tell which version of Tenant First Aid is deployed or running locally.

This issue also fits my learning goals because it requires reading a real open-source codebase, finding existing backend routes, finding where the frontend displays shared app information, and planning a small full-stack change without changing unrelated behavior.

---

## Understanding the Issue

### Problem Description

Tenant First Aid currently has more than one version value in the codebase. The backend project version is defined in `backend/pyproject.toml`, but the frontend has a separate package version in `frontend/package.json`. The visible footer currently uses the frontend package version, so it does not represent one shared application version.

### Expected Behavior

The application should have one source of truth for its version. The backend version should be exposed through an API endpoint, and the frontend should display that backend-provided version in the existing footer.

### Current Behavior

The backend version is defined at `backend/pyproject.toml:4`:

```toml
version = "0.5.0"
```

The frontend has a separate version at `frontend/package.json:4`:

```json
"version": "0.2.0"
```

The frontend injects that frontend package version in `frontend/vite.config.ts:6` and `frontend/vite.config.ts:13`:

```ts
import { version } from "./package.json";
__APP_VERSION__: JSON.stringify(version)
```

The footer displays that value in `frontend/src/App.tsx:46`:

```tsx
UI Version {__APP_VERSION__}
```

The backend currently registers `/api/query` at `backend/tenantfirstaid/app.py:55` and `/api/feedback` at `backend/tenantfirstaid/app.py:63`, but I did not find an existing `/api/version` route.

### Affected Components

* `backend/pyproject.toml`
* `backend/tenantfirstaid/app.py`
* `backend/tests/test_app.py`
* `frontend/package.json`
* `frontend/vite.config.ts`
* `frontend/vitest.config.ts`
* `frontend/src/vite-env.d.ts`
* `frontend/src/App.tsx`

---

## Reproduction Process

### Environment Setup

I forked the repository and cloned my fork locally:

```sh
git clone https://github.com/divinekpor/tenantfirstaid.git
cd tenantfirstaid
```

The project does not appear to use a dev container. I used the setup instructions in the root `README.md`.

Backend setup path from the README:

```sh
cd backend
uv sync
uv run python -m tenantfirstaid.app
```

Frontend setup path from the README:

```sh
cd frontend
npm install
npm run generate-types
npm run dev
```

Local frontend URL:

```text
http://localhost:5173
```

Important setup notes:

* Backend requires Python `>=3.12,<3.14`.
* Backend dependencies use `uv`.
* Frontend dependencies use `npm`.
* Running the full backend locally may require Google Cloud application default credentials and a LangSmith API key.
* The repository has `backend/.env.example` and root `.env.example` files for environment setup.

Working branch: https://github.com/divinekpor/tenantfirstaid/tree/fix-issue-175

### Steps to Reproduce

1. Open the cloned `tenantfirstaid` repository.
2. Inspect `backend/pyproject.toml:4`.
3. **Expected:** This backend version should be the app-level version source.
4. **Actual:** The backend version is `0.5.0`, but it is not exposed by a backend route.
5. Inspect `backend/tenantfirstaid/app.py:55` and `backend/tenantfirstaid/app.py:63`.
6. **Expected:** There should be an endpoint such as `GET /api/version`.
7. **Actual:** The file only registers `/api/query` and `/api/feedback`; no version endpoint exists.
8. Inspect `frontend/package.json:4`.
9. **Expected:** The frontend should not be the separate source of truth for the deployed app version.
10. **Actual:** The frontend has its own version, `0.2.0`.
11. Inspect `frontend/vite.config.ts:6` and `frontend/vite.config.ts:13`.
12. **Expected:** The frontend should get the displayed app version from the backend.
13. **Actual:** `__APP_VERSION__` is defined from the frontend package version.
14. Inspect `frontend/src/App.tsx:46`.
15. **Expected:** The footer should display the shared app version from the backend.
16. **Actual:** The footer displays `UI Version {__APP_VERSION__}`, which comes from the frontend package version.

### Reproduction Evidence

* **Commit showing reproduction:** [Link to commit in your fork]
* **Screenshots/logs:** [If applicable]
* **My findings:**

  * `backend/pyproject.toml:4` has backend version `0.5.0`.
  * `frontend/package.json:4` has frontend version `0.2.0`.
  * `frontend/vite.config.ts:13` defines `__APP_VERSION__` from the frontend package version.
  * `frontend/src/App.tsx:46` displays `UI Version {__APP_VERSION__}`.
  * `backend/tenantfirstaid/app.py:55` and `backend/tenantfirstaid/app.py:63` show the current API route pattern, but there is no `/api/version` route.

---

## Solution Approach

### Analysis

The root cause is that the frontend is using its own package version as the displayed UI version, while the backend version is stored separately and is not available through the API. This creates two version values and prevents the backend version from acting as the single source of truth.

### Proposed Solution

The backend version should be exposed through an API endpoint, and the frontend should display that backend-provided version in the existing footer.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** Tenant First Aid needs one semantic version number for the deployed application. Right now, the backend version is `0.5.0` in `backend/pyproject.toml:4`, but the frontend footer displays `0.2.0` from `frontend/package.json:4`.

**Match:** Backend routes are registered in `backend/tenantfirstaid/app.py` with `app.add_url_rule`, such as `/api/query` at line 55 and `/api/feedback` at line 63. Backend route tests are grouped in `backend/tests/test_app.py`, including route behavior tests like `test_unknown_route_returns_404` at line 74. The frontend already has a shared footer in `frontend/src/App.tsx:42-48`, so that is the right place to display the app version.

**Plan:**

1. In `backend/tenantfirstaid/app.py`, add a small version route function near the existing route definitions.
2. Read the backend package version from Python package metadata for `tenant-first-aid`, so the endpoint stays tied to `backend/pyproject.toml`.
3. Register a `GET /api/version` route with `app.add_url_rule`.
4. Return JSON in this shape:

```json
{ "version": "0.5.0" }
```

5. In `backend/tests/test_app.py`, add a test for `GET /api/version`.
6. Test that the endpoint returns `200`, JSON content, and a `version` value matching the backend package version.
7. In `frontend/src/App.tsx`, replace the current `UI Version {__APP_VERSION__}` footer display with state that fetches `/api/version`.
8. Keep a fallback display so the footer does not break if the version request fails.
9. Remove unused frontend-only version plumbing from `frontend/vite.config.ts`, `frontend/vitest.config.ts`, and `frontend/src/vite-env.d.ts` if nothing else uses `__APP_VERSION__`.
10. Add or update a frontend test if the existing test setup has coverage for `App.tsx` or footer behavior.

**Implement:** https://github.com/divinekpor/tenantfirstaid/tree/fix-issue-175

**Review:** I will review the code against the project README and `.github/pull_request_template.md`. The PR template asks contributors to identify the PR type, link the related issue, describe the changes, confirm tests, and note whether architecture documentation needs updates.

**Evaluate:** Manual verification should show that `GET /api/version` returns the backend version and that the footer displays the same version. Automated verification should include backend route tests and the relevant frontend checks.

---

## Testing Strategy

### Unit Tests

* [ ] Test case 1: [Description]
* [ ] Test case 2: [Description]
* [ ] Test case 3: [Description]

### Integration Tests

* [ ] Integration scenario 1
* [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

* **Files modified:** [List]
* **Key commits:** [Links to important commits]
* **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**

* [Date]: [Summary of feedback received]
* [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

* [Link to helpful documentation]
* [Tutorial or Stack Overflow post that helped]
* [GitHub issues or discussions that helped]
