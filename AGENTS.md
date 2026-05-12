# AGENTS.md

This fork is a reference project for building an agent-oriented Z-Library CLI.

The existing project is a human-facing terminal client. It contains useful
reverse-engineering work for login, search, metadata parsing, download URL
resolution, quota/history pages, proxy support, and challenge handling. The
interactive UI, themes, Kindle support, and presentation-oriented output are
mostly out of scope for the intended agent workflow.

## Useful Code To Study Or Reuse

The root package is the most valuable part of this repository.

- `client.go`
  - Login request construction.
  - Cookie jar usage.
  - Domain/proxy-aware HTTP client setup.
  - Browser-like request headers.
  - Basic challenge retry flow.

- `urls.go`
  - Domain normalization.
  - Search, full-text search, book detail, downloads, and history URL builders.

- `search.go`
  - Search option shape.
  - Search and full-text search flow.
  - Page/count normalization.

- `book.go`
  - Fetching book details by ID.
  - Concurrent detail fetching pattern for history enrichment.

- `profile.go`
  - Quota lookup.
  - Download history fallback across multiple page shapes.

- `parser.go`
  - `goquery`-based extraction for search results, book detail pages, download
    history, and quota data.
  - Selector fallbacks are useful examples for handling unstable HTML.

- `download.go`
  - Streaming download with progress callback.
  - Context cancellation.
  - Incomplete file cleanup on read errors.
  - Filename cleanup.

- `challenge.go`
  - Current proof-of-work challenge solver.
  - This is site-shape dependent and should be isolated behind a small
    interface in a new project.

- `internal/config/session.go`
  - Session file layout.
  - `0600` file permissions for stored cookies.

## Code To Avoid Carrying Forward

These pieces are useful for the original user-facing CLI but should not drive
the agent tool design.

- `internal/cli/*`
  - Cobra command definitions can be referenced, but the current commands are
    optimized for humans, not agents.
  - Bubble Tea and Huh interactive flows should not be part of the agent CLI.
  - Colorized tables and progress bars should be replaced with JSON and quiet
    stderr diagnostics.

- `kindle.go` and `internal/cli/kindle.go`
  - Kindle delivery is not needed for the intended workflow.
  - Removing it reduces dependencies, configuration surface, and maintenance.

- `internal/cli/theme.go`, `internal/cli/colors.go`, demo GIFs, and terminal UI
  support files.
  - These are presentation features, not agent features.

- `internal/tableprinter`
  - Useful as a small example of display-width testing, but not needed if the
    future CLI outputs JSON.

## Useful error codes:

- `NOT_LOGGED_IN`
- `EMPTY_QUERY`
- `NO_RESULTS`
- `BOOK_UNAVAILABLE`
- `QUOTA_EXCEEDED`
- `NETWORK_TIMEOUT`
- `NETWORK_FAILED`
- `PARSE_FAILED`
- `POLICY_BLOCKED`
- `FILE_TOO_LARGE`
- `UNSUPPORTED_FORMAT`
- `DOWNLOAD_FAILED`
