You are helping me modify a Python CLI project. First, read and understand the codebase, then implement a UX change.

## Goal
The current script asks the user to paste a **Microsoft Learn course URL**. I want to remove that requirement.

Instead:
1. Fetch all available courses from the Microsoft Learn Catalog API.
2. Present those courses as a numbered list (or searchable selection) to the user.
3. Let the user select one course by number.
4. Use the selected course URL/UID to continue the existing flow (fetch learning paths, modules, units, generate HTML/PDF output).

## Requirements
- Keep existing behavior and architecture as intact as possible.
- Reuse existing services/classes where possible; avoid large rewrites.
- Add clear error handling for:
  - catalog fetch failure
  - empty course list
  - invalid user selection
- If useful, show both course title and URL in the selection list.
- Preserve the default output generation flow once a course is selected.
- Keep code style consistent with the repository.

## Implementation expectations
- Inspect current classes and identify where user input currently happens.
- Add a method to retrieve available courses from the catalog (title + URL/uid).
- Replace the current `input(course_url)` prompt with a `select course` prompt.
- Ensure selected course can still be passed into `process_course(...)` without breaking downstream logic.
- Update README usage instructions to reflect the new selection flow.

## Deliverables
1. Code changes.
2. A concise explanation of what changed and why.
3. Any assumptions you made.
4. Commands run to validate (e.g., lint/tests/manual run).
