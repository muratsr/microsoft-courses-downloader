# Code Documentation

## Overview
This project downloads Microsoft Learn training content and compiles it into module-level HTML and PDF files.

At runtime it:
1. Fetches the Microsoft Learn catalog.
2. Optionally asks for a topic keyword (for example, `AI`) to filter courses.
3. Shows matching courses and asks the user to choose one.
4. Resolves learning paths for that course.
5. Resolves modules for each learning path.
6. Fetches unit pages for each module and merges them into one HTML file.
7. Converts each generated HTML file into a PDF.

---

## Core Components

### `HttpClient`
- Centralizes request headers and timeout handling.
- Used by service classes for all HTTP requests.

### `CatalogService`
- Fetches and caches `https://learn.microsoft.com/api/catalog/`.
- Provides:
  - `get_available_courses()` for interactive course selection.
  - `get_course_learning_paths(course_url)` for path discovery.
  - `get_learning_path_modules(path_url)` for module discovery.

### `ContentService`
- Fetches page HTML.
- Extracts page title and main content block.
- Cleans navigation/UI-only HTML elements.
- Normalizes relative image URLs.
- Discovers unit links under a module page.

### `HtmlGenerator`
- Builds one combined HTML document per module from all unit pages.
- Applies shared CSS formatting.
- Sanitizes generated filenames.

### `PdfGenerator`
- Uses Playwright Chromium to convert generated HTML into PDF.
- Handles conversion failures gracefully.

### `CourseProcessor`
- Orchestrates the full course-to-files pipeline.
- Creates output directories for course and learning path structure.
- Invokes module processing and artifact generation.

---

## Runtime Flow (`main.py`)
1. Initialize `CourseProcessor`.
2. Call `get_course_url_from_user(processor.catalog_service)`.
3. Optionally filter courses by topic using case-insensitive title matching.
4. Validate numeric selection and map it to a course URL.
5. Pass selected URL into `processor.process_course(...)`.
6. Generate HTML/PDF output in `output/<course>/<learning-path>/...`.

---

## How To Change This For Future Ideas

## 1) Add search/filter for course selection
- Current selection prints every course.
- You can add keyword filtering before listing options.
- Suggested place: `get_course_url_from_user`.

## 2) Allow selecting multiple courses
- Change input parser to accept comma-separated indices.
- Loop over selected course URLs and call `process_course` for each.

## 3) Add non-interactive CLI flags
- Introduce `argparse` in `main()`.
- Example flags:
  - `--course-url <url>` bypasses interactive selection.
  - `--course-index <n>` selects from fetched list.
  - `--html-only` skips PDF generation.

## 4) Improve robustness/retries
- Add retry/backoff logic inside `HttpClient.get`.
- Handle transient API failures more gracefully.

## 5) Parallelize page fetching
- Module unit page fetches are currently sequential.
- Convert fetching in `HtmlGenerator._build_html` to async/concurrent if speed is a concern.

## 6) Change output formats
- Add Markdown/EPUB/docx generation by introducing another generator class similar to `PdfGenerator`.

## 7) Persist catalog cache
- Save the catalog JSON to disk with a TTL to reduce repeated API calls across runs.

---

## Maintenance Notes
- Keep URL cleanup logic centralized (`_clean_url`).
- Reuse `CatalogService` and `ContentService` instead of duplicating fetch logic.
- Preserve error messages that explain whether failure happened at catalog fetch, selection, module discovery, or conversion stage.
