# Contribution 1: Semantic versioning

**Contribution Number:** 1
**Student:** Divine Doamekpor
**Issue:** https://github.com/codeforpdx/tenantfirstaid/issues/175
**Status:** Phase I In Progress

---

## Why I Chose This Issue

I chose this issue because it seems like a good first contribution that will help me understand both the frontend and backend of the project. The issue is focused on adding a single version number for the whole application, which is useful because it helps users and developers know exactly what version of the website they are using.

This issue also matches my learning goals because I want to get more comfortable working in a real open-source codebase. Since this task may involve reading from the backend version, creating an endpoint, and displaying the result on the webpage, it gives me practice with full-stack development in a manageable way.

---

## Understanding the Issue

### Problem Description

The project needs one clear version number that represents the deployed app as a whole. Right now, the backend has a version listed in `pyproject.toml`, but that version has not been updated in a while. There is also no visible way for users to see what version of the website they are using.

### Expected Behavior

The application should have a single source of truth for the version number. The backend should provide an endpoint that returns the current app version, and the frontend should display that version somewhere on the webpage.

### Current Behavior

Currently, the version exists in the backend, but it may be outdated and is not clearly connected to the deployed app as a whole. Users cannot easily see the version of the website they are interacting with.

### Affected Components

The affected components will likely include:

* Backend version configuration, possibly `pyproject.toml`
* A backend API endpoint for getting the version
* Frontend code that fetches and displays the version
* Possibly tests for the new endpoint or frontend display

---

## Reproduction Process

### Environment Setup

I will start by forking the repository, cloning it locally, and following the project setup instructions. I will make sure the backend and frontend can both run locally before making changes.

### Steps to Reproduce

1. Clone and run the project locally.
2. Look for any existing version information in the backend, especially in `pyproject.toml`.
3. Check the frontend webpage to see whether the version is currently displayed.
4. Confirm that there is no endpoint currently exposing the app version.

### Reproduction Evidence

* **Commit showing reproduction:** [Link to commit in your fork]
* **Screenshots/logs:** I will add screenshots or terminal output showing the app running without a visible version.
* **My findings:** The app needs a clear version endpoint and a visible version display on the webpage.

---

## Solution Approach

### Analysis

The root issue is that the app does not currently have a clear, visible version system for users. Even though the backend has a version in `pyproject.toml`, it is not being used as a single source of truth across the deployed app.

### Proposed Solution

I plan to use the backend version as the single source of truth, expose it through a backend endpoint, and then update the frontend to fetch and display that version on the webpage.

### Implementation Plan

Using UMPIRE framework:

**Understand:**
The project needs one version number that represents the entire deployed app and can be shown to users.

**Match:**
I will look through the backend routes to find how existing API endpoints are created. I will also look through the frontend to find a good location to display small site information, such as a footer or about section.

**Plan:**

1. Find where the backend version is stored in `pyproject.toml`.
2. Add a backend endpoint that returns the current version.
3. Add or update frontend code to call the version endpoint.
4. Display the version somewhere simple on the webpage.
5. Add or update tests if the project has testing patterns for endpoints or frontend components.

**Implement:**
[Link to your branch/commits as you work]

**Review:**
I will check that the code follows the project’s style, does not hard-code the version in multiple places, and keeps the backend version as the single source of truth.

**Evaluate:**
I will run the project locally and confirm that the version endpoint works and that the version appears correctly on the webpage.

---

## Testing Strategy

### Unit Tests

* [ ] Test that the version endpoint returns a successful response.
* [ ] Test that the endpoint returns the expected version format.
* [ ] Test that the frontend can display the version without breaking the page.

### Integration Tests

* [ ] Start the backend and confirm the frontend can fetch the version.
* [ ] Confirm the displayed version matches the backend version source.

### Manual Testing

I will manually run the app locally, open the webpage, and check that the version is visible. I will also use the browser or a tool like `curl` to confirm that the version endpoint returns the correct response.

---

## Implementation Notes

### Week 1 Progress

I selected the issue, reviewed the issue description, and started identifying the backend and frontend areas that may need to be changed.

### Week 2 Progress

[Continue documenting as you work]

### Code Changes

* **Files modified:** [List files after implementation]
* **Key commits:** [Add commit links]
* **Approach decisions:** I plan to avoid hard-coding the version in multiple places so the project has one clear source of truth.

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:**
This PR adds support for exposing the application version through a backend endpoint and displaying the version on the frontend. The goal is to reduce ambiguity about which version of the website a user is interacting with.

**Maintainer Feedback:**

* [Date]: [Summary of feedback received]
* [Date]: [How you addressed it]

**Status:** Awaiting implementation

---

## Learnings & Reflections

### Technical Skills Gained

I expect to gain practice reading an unfamiliar open-source codebase, working with backend routes, connecting frontend code to an API endpoint, and following project contribution standards.

### Challenges Overcome

One challenge may be figuring out the best way to read the version from `pyproject.toml` and finding the best place in the frontend to display it.

### What I'd Do Differently Next Time

After completing this issue, I would try to inspect the project structure faster and identify existing patterns before writing new code.

---

## Resources Used

* GitHub issue: https://github.com/codeforpdx/tenantfirstaid/issues/175
* Project README: [Add link]
* Backend documentation or framework docs: [Add link]
* Frontend documentation or framework docs: [Add link]
