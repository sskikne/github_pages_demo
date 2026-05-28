# GitHub Pages Deployment Tutorial — Design Spec

## Overview

A single-page tutorial (`index.html`) that teaches non-technical vibe coders how to deploy their Claude Code projects to GitHub Pages. The page is itself deployed to GitHub Pages, serving as both documentation and a working example.

## Audience

- Non-technical users who build apps by prompting Claude Code
- They may have never used GitHub before
- They have built (or are building) tools on top of the Learning Commons Knowledge Graph
- They are working with Next.js or Vite/React projects scaffolded by Claude Code

## Design Principles

- **Battle-tested**: Every step in the tutorial is verified by the actual deployment of this page
- **One prompt, zero decisions**: The sample prompt handles framework detection (Next.js vs Vite) internally — the user copies one block and pastes it
- **Clean and professional**: Minimalist UI, no fluff, no celebration text, no emoji

## Page Structure

### 1. Hero
- Title: "Deploy Your App to GitHub Pages"
- Subtitle: A one-line description — something like "A step-by-step guide for turning your Claude Code project into a live website."

### 2. What is GitHub Pages?
- Two sentences max. GitHub hosts your static files as a website for free. No servers to manage.

### 3. What Won't Work on GitHub Pages
Clear, direct list of things that require a server and therefore won't work:
- **Search endpoints** — If your app calls a search API you built, that won't run on GitHub Pages. (The Learning Commons Knowledge Graph search endpoint is an example of this.)
- **Databases** — No PostgreSQL, SQLite, or any database. All data must be pre-cached as static files.
- **User authentication** — No login/signup flows that require server-side session management.
- **Server-side API routes** — Next.js API routes (`/api/*`), Express endpoints, etc.
- **Real-time features** — WebSockets, server-sent events, anything requiring a persistent connection.
- **Server-side rendering** — `getServerSideProps`, dynamic server rendering. (Static generation / `getStaticProps` is fine.)

Frame this as: "If your app needs any of these, GitHub Pages isn't the right fit. Consider Vercel, Netlify, or Railway instead."

### 4. Setting Up GitHub
For users who have never used GitHub:
- Link to https://github.com/signup with brief guidance
- Installing GitHub CLI (`gh`) — link to https://cli.github.com with install commands for macOS/Windows/Linux
- Signing into GitHub CLI: `gh auth login` — walk through what to expect (browser opens, authorize, done)

### 5. Make Your App Deployable — The Prompt
A single copyable prompt block. When pasted into Claude Code, it:
1. Detects whether the project is Next.js or Vite/React
2. **For Next.js**: Sets `output: 'export'` in `next.config.js`, removes API routes, replaces `getServerSideProps` with static data, configures `basePath` and `assetPrefix` for GitHub Pages
3. **For Vite**: Configures `base` in `vite.config.js` for GitHub Pages
4. **For both**: Fetches data from `https://api.learningcommons.org/knowledge-graph/v0` (referencing docs at `https://docs.learningcommons.org/knowledge-graph/getting-started/quickstart`), saves it as a local JSON cache file, and updates the app to read from the cache instead of making live API calls
5. Removes any backend dependencies (Express, database clients, etc.)
6. Ensures the build output is a folder of static HTML/CSS/JS

### 6. Deploy to GitHub Pages
Step-by-step using `gh` CLI and git commands. These steps will be finalized based on actual deployment experience, but the expected flow:
1. Create a GitHub repository: `gh repo create`
2. Add remote and push code
3. Set up GitHub Pages (either via `gh` CLI or GitHub Actions workflow)
4. Verify the site is live

Any steps that require the user to visit GitHub.com in a browser (e.g., enabling Pages in repo settings) will be clearly noted with screenshots or exact navigation paths.

### 7. Troubleshooting
Common issues discovered during actual deployment, plus known issues:
- Blank page (usually `basePath` / `base` not set correctly)
- 404 on refresh (GitHub Pages doesn't support client-side routing without a 404.html workaround)
- Missing assets (relative vs absolute paths)
- Build failures

## Visual Design

- **Layout**: Single column, max-width ~700px, centered
- **Colors**: White background (#fff or very light gray), dark text (#1a1a1a), one accent color for links and callouts
- **Typography**: System font stack (sans-serif), comfortable line height (~1.6), clear heading hierarchy
- **Code blocks**: Light gray background, monospace font, horizontal scroll for long lines
- **Spacing**: Generous whitespace between sections
- **No JavaScript** (the tutorial page itself is pure HTML + CSS)
- **No images, icons, or decorative elements** unless they serve a clear instructional purpose
- **Responsive**: Reads well on mobile (simple single-column layout handles this naturally)

## Technical Approach

- Single `index.html` file at the repo root
- Inline `<style>` block — no external CSS files
- Deployed via GitHub Pages from the `main` branch root
- The repo itself demonstrates the simplest possible GitHub Pages deployment

## Deployment Strategy

The deployment will be done live during implementation. Steps:
1. Create the `index.html`
2. Create the GitHub repo via `gh repo create`
3. Push to `main`
4. Enable GitHub Pages
5. Verify the live URL
6. Update the tutorial with the exact commands and any corrections based on what actually happened

## Sample Prompt (Draft)

The prompt will be refined during implementation, but the core instructions to Claude Code will be:

```
I want to deploy this project to GitHub Pages. Please make it fully static and deployable:

1. Detect whether this is a Next.js or Vite/React project.

2. If Next.js:
   - Set `output: 'export'` in next.config.js
   - Remove any API routes (pages/api/ or app/api/)
   - Replace getServerSideProps with getStaticProps or static data
   - Add `basePath` and `assetPrefix` set to '/<repo-name>' in next.config.js
   - Add a .nojekyll file to the output directory

3. If Vite/React:
   - Set `base: '/<repo-name>'` in vite.config.js

4. For data from the Learning Commons Knowledge Graph:
   - Fetch data from https://api.learningcommons.org/knowledge-graph/v0
     (API docs: https://docs.learningcommons.org/knowledge-graph/getting-started/quickstart)
   - Save the response as a local JSON file (e.g., public/data/knowledge-graph.json)
   - Update all API calls in the app to read from this local JSON file instead
   - Remove any search functionality that depends on a server-side search endpoint

5. Remove any backend dependencies (express, database clients, server-only packages)

6. Build the project and verify the output is a folder of static HTML/CSS/JS files

7. Create a GitHub Actions workflow (.github/workflows/deploy.yml) that:
   - Builds the project on push to main
   - Deploys the build output to GitHub Pages
```

## Out of Scope

- Multiple pages / routing within the tutorial itself
- Interactive elements or JavaScript on the tutorial page
- Video or animated content
- Coverage of deployment platforms other than GitHub Pages (mentioned only as alternatives)
