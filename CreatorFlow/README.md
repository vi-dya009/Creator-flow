# CreatorFlow

CreatorFlow is a portfolio-ready content planning demo for individual creators. Collect ideas, draft content, plan posts on a calendar, and see simple account-level counts. **Scheduling is a planning demo only:** nothing is published to social platforms and analytics sample values are labelled as demo data.

## Features

- Responsive landing page, sign-up/login, protected workspace, and profile settings.
- Secure password hashing, server-side SQLite sessions, session rotation on sign-in, CSRF checks, and per-account content ownership.
- Create, view, edit, and delete content with platform, status, caption, and optional scheduled time.
- Calendar month view, dashboard counts derived from SQLite, analytics sample chart labelled “Demo data.”
- Content search and filters, CSV export, keyboard shortcuts (`Ctrl/Cmd+K` to search, `N` to create), accessible focus states, and mobile navigation.
- Automated API tests for authentication, validation, password hashing, CRUD, dashboard counts, and account data isolation.

## Requirements

- Node.js 22.12+ (up to Node.js 24; Vite 8 requires at least Node.js 20.19 or 22.12)
- npm

## Run locally

```bash
npm install
Copy-Item .env.example .env
```

Set `SESSION_SECRET` in `.env` to a unique random value of at least 32 characters before deploying. The development value is intentionally not a real secret. Start both the API and frontend:

```bash
npm run dev
```

Open the Vite URL shown in the terminal (usually `http://localhost:5173`). The Vite development server proxies `/api` requests to Express on port 3000. SQLite creates `server/data/creatorflow.sqlite` automatically. Set `DATABASE_PATH` to change its location.

Create an account from the landing page. Use synthetic names and test credentials only.

## Publish the interactive preview with GitHub Pages

GitHub Pages publishes the frontend as a **read-only portfolio preview** with synthetic sample content. Its static hosting cannot run the Express API or SQLite database, so account registration, login, real data persistence, server-backed content editing, and real publishing are not available on the Pages site. The regular, full-stack app continues to run locally using `npm run dev`.

1. Push the complete project to the GitHub repository named `Creator-flow`. Keep `.github/workflows/deploy-pages.yml` in the repository. The Vite Pages base path is configured for this repository name.
2. In GitHub, open **Settings → Pages**.
3. Under **Build and deployment → Source**, select **GitHub Actions**.
4. Open the repository's **Actions** tab and run **Deploy CreatorFlow portfolio preview**, or push a commit to `main`.
5. Wait for the workflow to complete successfully. Open `https://vi-dya009.github.io/Creator-flow/`.
6. Select **Explore the demo** to view the sample dashboard, content library, calendar, and demo analytics.

If the repository has a different owner or name, update `base` in `vite.config.js` and the public link above. Every push to `main` also runs `.github/workflows/ci.yml` to test and lint the full-stack app; the Pages deployment workflow checks those same requirements before publishing the static preview.

To run the full API, authentication, and database locally, follow the local steps above. GitHub Pages is not a backend host; do not enter real passwords into the static demo.

## Quality checks

```bash
npm test
npm run lint
npm run build
```

## API overview

| Method | Endpoint | Purpose |
| --- | --- | --- |
| `GET` | `/api/health` | Health check |
| `GET` | `/api/auth/csrf` | Initialize a CSRF token |
| `POST` | `/api/auth/register` | Register and sign in |
| `POST` | `/api/auth/login` | Sign in |
| `POST` | `/api/auth/logout` | Sign out |
| `GET` | `/api/auth/me` | Read the current session |
| `GET`, `POST` | `/api/posts` | List or create the signed-in user's content |
| `PUT`, `DELETE` | `/api/posts/:id` | Update or delete owned content |
| `GET` | `/api/dashboard` | Database-derived content counts |
| `GET`, `PUT` | `/api/profile` | Read or update profile details |

All state-changing API requests require the current token in the `X-CSRF-Token` header. Content queries are always scoped by the authenticated user ID.

## Scope notes

This is a demonstration planner, not a social publishing service. It does not request social-account credentials, connect platform APIs, publish posts, or present sample analytics as real performance. Team collaboration, billing, AI generation, and real analytics integrations are intentionally outside the MVP.
