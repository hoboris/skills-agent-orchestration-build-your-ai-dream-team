# Project Pulse Dashboard — Implementation Plan

## Summary

Build Mona’s Project Pulse as a lightweight, dependency-free static dashboard for contributors. The app will present multiple projects in polished, responsive cards showing ownership, status, recent activity, priority/risk, and a short summary. The repository currently has no application implementation or frontend dependency manifest, so use browser-native HTML, CSS, JavaScript, and JSON patterns.

The dashboard must be runnable through the VS Code launch configuration named **Run Project Pulse Dashboard**, serving `app/` with `python3 -m http.server 5500` and opening `index.html` rather than a directory listing.

## Ordered Implementation Steps

1. **Confirm the UI and data contract**
   - **Designer** defines the information hierarchy, responsive behavior, accessibility treatment, color/status semantics, and the shared markup hooks required by styling.
   - Establish the required hooks before implementation: `.dashboard`, `.project-card`, status badge classes/attributes, priority treatment, loading/error area, and responsive card layout.
   - **Files:** No file changes required for this handoff; it is the implementation contract for subsequent work.
   - **Depends on:** Project brief and existing agent conventions.

2. **Create the project data source**
   - **Coder** creates `app/project-data.json` as valid JSON with a top-level `projects` array.
   - Add multiple realistic project records. Every record must include `name`, `owner`, `status`, `recentActivity`, and `priority`; include a short `summary` field to satisfy the contributor-friendly summary requirement.
   - Keep values suitable for display, with consistent status and priority terminology.
   - **Files:** `app/project-data.json`
   - **Depends on:** The data contract from step 1.

3. **Implement the dashboard document and data rendering**
   - **Coder** creates `app/index.html` with the exact visible title **Project Pulse**, semantic page structure, and a link to `styles.css`.
   - Load `project-data.json` in browser JavaScript, render one visible `.project-card` per project, and display name, owner, status, recent activity, priority, and summary.
   - Add accessible status/priority text rather than conveying meaning only through color. Include clear loading, empty-data, and data-load failure states.
   - **Files:** `app/index.html`
   - **Depends on:** Step 2’s final JSON schema and step 1’s agreed markup hooks.

4. **Implement polished responsive styling**
   - **Designer** creates `app/styles.css` using the agreed structure and class hooks.
   - Provide a polished card-based dashboard with `.dashboard` and `.project-card` selectors, readable typography and spacing, status badges, distinct priority/risk treatment, `border-radius`, `box-shadow`, keyboard-focus styling, sufficient contrast, and a responsive layout that remains usable on narrow screens.
   - **Files:** `app/styles.css`
   - **Depends on:** Step 1’s hook contract; should be reviewed against the completed markup in step 3 before integration sign-off.

5. **Add the deterministic VS Code launch configuration**
   - **Coder** creates `.vscode/launch.json` as strict JSON with no comments.
   - Add a configuration named **Run Project Pulse Dashboard** that runs `python3 -m http.server 5500` with `cwd` set to `${workspaceFolder}/app`.
   - Configure `serverReadyAction` to open `http://localhost:%s/index.html`, ensuring the browser opens the dashboard frontend rather than the server’s directory root.
   - **Files:** `.vscode/launch.json`
   - **Depends on:** The required app location and launch behavior from the project brief; it does not depend on the finished UI.

6. **Integrate and validate**
   - **Coder** verifies the data-driven page, error states, JSON validity, and launch configuration.
   - **Designer** performs a visual/accessibility review of the integrated dashboard at desktop and narrow viewport sizes.
   - **Files reviewed:** `app/index.html`, `app/styles.css`, `app/project-data.json`, `.vscode/launch.json`
   - **Depends on:** Steps 2–5 being complete.

## File Assignments

| File | Owner | Responsibility |
| --- | --- | --- |
| `app/index.html` | Coder | Semantic dashboard structure, stylesheet reference, JSON loading, dynamic project-card rendering, and explicit loading/empty/error states. |
| `app/styles.css` | Designer | Responsive visual system, dashboard/card layout, status and priority presentation, accessibility-focused typography, focus, spacing, contrast, rounded styling, and shadows. |
| `app/project-data.json` | Coder | Valid project dataset with top-level `projects` and required project fields, plus contributor-friendly summaries. |
| `.vscode/launch.json` | Coder | Strict JSON launch configuration named **Run Project Pulse Dashboard** that serves `app/` and opens `index.html`. |

## Dependencies

- The Designer’s component/hook contract must be agreed before Coder finalizes HTML class names and accessible labels.
- `app/project-data.json` must be defined before `app/index.html` finalizes its rendering and validation logic.
- `app/index.html` and `app/styles.css` must use the same agreed selectors, especially `.dashboard` and `.project-card`.
- `.vscode/launch.json` is independent of UI/data implementation but must use the fixed `app/` directory, port `5500`, and `index.html` URL required by the brief.
- Integration validation must occur after all four required files exist.

## Parallel and Sequential Work

### Can run in parallel

After step 1 establishes the shared UI contract:

- **Coder:** Create `app/project-data.json`.
- **Coder:** Create `.vscode/launch.json`.
- **Designer:** Begin `app/styles.css` using the agreed component hooks and responsive design contract.

These files have separate ownership and do not require concurrent edits.

### Must run sequentially

1. Designer’s UI/hook contract must precede final HTML/CSS implementation decisions.
2. `app/project-data.json` must precede final data rendering in `app/index.html`.
3. `app/index.html` structure must be available for Designer’s final CSS integration review.
4. All implementation work must precede end-to-end browser and launch validation.

## Edge Cases and Risks to Handle

- The JSON request may fail when the app is opened directly with `file://`; the dashboard should direct users to use the provided launch configuration/server.
- The JSON may be malformed, missing `projects`, contain an empty array, or omit a display field; show a concise visible error/empty state instead of failing silently.
- Project values may be unexpectedly long; card layout should wrap text without overflow.
- Unknown statuses or priorities should remain readable through text and use a neutral visual fallback.
- Color alone must not communicate project status or priority.
- The launch server’s readiness pattern must match Python’s actual startup output; otherwise VS Code may not open the browser automatically.
- Port `5500` may already be occupied; document this as a local runtime limitation if it occurs during validation.

## Validation Expectations

1. **File and syntax checks**
   - Verify all required files exist:
     - `app/index.html`
     - `app/styles.css`
     - `app/project-data.json`
     - `.vscode/launch.json`
   - Run `python3 -m json.tool app/project-data.json`.
   - Run `python3 -m json.tool .vscode/launch.json`.
   - Confirm `launch.json` is strict JSON with no comments.

2. **Static content checks**
   - Confirm `app/index.html` contains the exact title `Project Pulse`.
   - Confirm it references `styles.css` and `project-data.json`.
   - Confirm rendered project cards use the `project-card` class.
   - Confirm the page displays status, `recentActivity`, and priority for each project.
   - Confirm `app/styles.css` contains `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.
   - Confirm project data has a top-level `projects` array and every project includes `name`, `owner`, `status`, `recentActivity`, and `priority`.

3. **Runtime checks**
   - Start **Run Project Pulse Dashboard** from VS Code Run and Debug.
   - Verify it serves from `app/` using `python3 -m http.server 5500`.
   - Verify the browser opens `http://localhost:5500/index.html` (via the required URL template) and displays the dashboard rather than a directory listing.
   - Verify multiple project cards render from the JSON data.
   - Verify loading, empty, and failed-data paths behave visibly and do not leave a blank page.
   - Stop the preview server after validation.

4. **Visual and accessibility checks**
   - Review desktop and narrow-width layouts for readable spacing, no clipped content, and usable card layout.
   - Confirm status badges and priority indicators include readable text.
   - Check that focus indicators are visible and text/background contrast is legible.

## Open Questions

- No existing app-specific visual system or frontend library is present; the implementation should remain native HTML/CSS/JavaScript unless the Orchestrator explicitly approves a new dependency.
- The brief requires priority “or risk level” but does not prescribe a taxonomy; use a small, consistent set of text values and ensure unknown values have a neutral fallback.
- The brief does not specify whether project data must be editable at runtime; this plan treats `project-data.json` as the static source of truth.
