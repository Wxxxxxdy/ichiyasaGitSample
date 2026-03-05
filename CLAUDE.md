# CLAUDE.md

This file provides guidance for AI assistants working in this repository.

## Project Overview

**ichiyasaGitSample** is a static HTML landing page for the "Ichiyasa Git User Group" (いちばんやさしいGit&GitHubの教本のサンプルプロジェクト) — a sample project from a Japanese Git/GitHub tutorial book. It serves as a community event website for Git study meetings/workshops.

- **Type**: Static HTML5 website (no build step, no package manager)
- **Template**: "Read Only" by HTML5 UP (CCA 3.0 license)
- **Language**: Japanese content, English code

## Repository Structure

```
ichiyasaGitSample/
├── .github/
│   └── workflows/
│       ├── claude.yml              # Claude Code assistant (triggered by @claude mentions)
│       └── claude-code-review.yml  # Automatic PR code review via Claude
├── assets/
│   ├── css/
│   │   ├── main.css               # Main stylesheet (3094 lines, includes grid system)
│   │   └── font-awesome.min.css   # FontAwesome 4.6.3 icon library
│   ├── fonts/                     # FontAwesome webfont files
│   └── js/
│       ├── jquery.min.js          # jQuery (minified, ~94KB)
│       ├── jquery.scrolly.min.js  # Smooth scroll plugin
│       ├── jquery.scrollzer.min.js # Nav active-state plugin
│       ├── skel.min.js            # Deprecated responsive layout framework
│       ├── util.js                # jQuery plugin definitions (navList, panel, scrolly, scrollzer)
│       └── main.js                # Application entry point (112 lines)
├── images/
│   ├── avatar.png                 # Community avatar (383x383, 2.3KB)
│   └── banner.png                 # Header banner (1911x439, 793KB — oversized)
├── index.html                     # Single-page entry point (170 lines)
├── .gitignore                     # Minimal — only excludes .DS_Store
└── README.md                      # Japanese improvement notes
```

## Key Files

### `index.html`
The sole HTML file. It contains three sections:
- `#one` — Community description (Ichiyasa Git User Group)
- `#two` — Upcoming event announcement (第２回Git勉強会)
- `#three` — Past events with embedded YouTube iframes

Scripts are loaded at the bottom of `<body>` in this order:
1. `jquery.min.js`
2. `jquery.scrollzer.min.js`
3. `jquery.scrolly.min.js`
4. `skel.min.js`
5. `util.js`
6. `main.js`

### `assets/js/main.js`
Initializes the page using the skel framework and jQuery:
- Sets responsive breakpoints via `skel.breakpoints()`
- Activates smooth scrolling with `$.scrolly()`
- Activates nav active-state tracking with `$.scrollzer()`
- Creates a mobile title bar with a toggle for off-canvas navigation

### `assets/css/main.css`
3094-line stylesheet — the majority is unused template boilerplate. Google Fonts (Lato, Source Code Pro) are loaded via `@import` inside this file.

## Development Workflow

### No Build Step Required
This is a static site. To view it locally, simply open `index.html` in a browser or serve with any static file server:
```bash
python3 -m http.server 8080
# or
npx serve .
```

### Making Changes
1. Edit `index.html` for content changes
2. Edit `assets/css/main.css` for style changes
3. Edit `assets/js/main.js` for behavior changes
4. Refresh the browser to preview

### Branching Convention
- `master` — stable main branch
- `claude/<description>-<id>` — Claude-generated feature branches
- Feature branches are merged via pull requests

### Git Workflow
```bash
git checkout -b <branch-name>
# make changes
git add <specific-files>
git commit -m "descriptive message"
git push -u origin <branch-name>
# then open a pull request
```

## CI/CD: GitHub Actions

### `claude.yml` — Claude Code Assistant
Triggers when any issue or PR comment contains `@claude`. Claude can:
- Answer questions about the codebase
- Implement changes and push commits
- Read CI results on PRs

Required secret: `CLAUDE_CODE_OAUTH_TOKEN`

### `claude-code-review.yml` — Automatic Code Review
Triggers on every PR event (opened, synchronize, ready_for_review, reopened). Runs the `code-review` Claude Code plugin automatically.

Required secret: `CLAUDE_CODE_OAUTH_TOKEN`

## Known Issues (from README)

These are documented improvement areas — do not treat them as bugs to fix without being asked:

**Content**
- Speaker/session info is placeholder (`未定` = TBD)
- No event registration link (connpass / Doorkeeper)
- Event year missing (only "3月23日" shown)
- Copyright footer still says "© Untitled"
- Nav label ("Japan Git User Group") doesn't match section heading ("Ichiyasa Git User Group")

**Missing Files**
- `images/speaker1.png` and `images/speaker2.png` are referenced in HTML but do not exist

**Performance**
- `banner.png` is 793KB — should be compressed/converted to WebP
- Google Fonts loaded via `@import` in CSS (render-blocking)
- `font-awesome.min.css` loaded but FontAwesome icons are not used
- jQuery (94KB) used only for smooth scroll and nav highlighting

**Code Quality**
- `skel.min.js` is an unmaintained legacy framework
- No `defer`/`async` on script tags
- Large amount of unused CSS in `main.css`
- `.gitignore` is minimal

**SEO / Accessibility**
- No `<meta name="description">`
- All images have empty `alt=""` attributes
- No OGP tags for social sharing

## Conventions for AI Assistants

- **Edit content** only in `index.html` — all visible page text and structure lives there
- **Edit styles** only in `assets/css/main.css` — do not add inline styles
- **Edit behavior** only in `assets/js/main.js` — do not add `<script>` blocks to HTML
- **Do not add new dependencies** without discussing first — this is intentionally dependency-free
- **Do not add files** for one-time fixes; prefer editing existing files
- **Commit specific files** by name; do not use `git add -A` or `git add .`
- **Use descriptive commit messages** in English or Japanese consistent with existing history
- **Open pull requests** for all changes; do not push directly to `master`
- **Speaker images** (`speaker1.png`, `speaker2.png`) are missing — note this when touching the speakers section
