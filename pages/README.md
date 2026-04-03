# OpenCalendar Deployment Guide (Cloudflare)

This folder contains the Cloudflare deployment setup for OpenCalendar.

Important note:
The current project is deployed using Wrangler with static assets from the `calendar` folder, which serves your site on a `*.workers.dev` URL.

## 1) Requirements

You need these before deployment:

1. Cloudflare account
2. Node.js (LTS recommended, 18+)
3. npm (comes with Node.js)
4. Wrangler CLI (installed globally or used with `npx`)

Check versions:

```bash
node -v
npm -v
npx wrangler -v
```

## 2) Project Structure (What each part does)

Current structure in this folder:

```text
pages/
  calendar/
	 index.html
	 users.html
  wrangler.jsonc
  README.md
```

What each component means:

1. `calendar/index.html`
	Main Calendar page (home page of your app).

2. `calendar/users.html`
	Users page, currently routed as `/calendar/users.html`.

3. `wrangler.jsonc`
	Deployment configuration file that tells Cloudflare what to deploy and how.

4. `README.md`
	Documentation for setup, deployment, and maintenance.

## 3) `wrangler.jsonc` Explained

Your current config:

```jsonc
{
  "$schema": "node_modules/wrangler/config-schema.json",
  "name": "opencalendar",
  "compatibility_date": "2026-04-03",
  "observability": {
	 "enabled": true
  },
  "assets": {
	 "directory": "calendar"
  },
  "compatibility_flags": [
	 "nodejs_compat"
  ]
}
```

Meaning and requirement of each field:

1. `$schema`
	Enables editor validation/autocomplete for Wrangler config.
	Requirement: optional but strongly recommended.

2. `name`
	Project/service name used by Cloudflare deployment.
	Requirement: required.

3. `compatibility_date`
	Locks runtime behavior to a known Cloudflare Workers API date.
	Requirement: required.

4. `observability.enabled`
	Enables Cloudflare observability features (logs/insights support).
	Requirement: optional.

5. `assets.directory`
	Tells Wrangler to upload static files from `calendar/`.
	Requirement: required for static-asset deployment.

6. `compatibility_flags: ["nodejs_compat"]`
	Enables Node.js compatibility behavior where needed.
	Requirement: optional (only needed if your runtime code depends on it).

## 4) First-Time Setup

From repository root:

```bash
cd pages
npm init -y
npm install -D wrangler
npx wrangler login
```

Why these are needed:

1. `npm init -y`
	Creates local Node project metadata.

2. `npm install -D wrangler`
	Installs Wrangler locally so everyone uses a consistent version.

3. `npx wrangler login`
	Authenticates your machine with your Cloudflare account.

## 5) Deploy

Deploy from the `pages` folder:

```bash
npx wrangler deploy
```

After deployment, Cloudflare returns a `workers.dev` URL for your project.

## 6) Routing Notes for This Project

Because static files are served from `calendar/`:

1. `index.html` is the entry page.
2. `users.html` should be linked with a valid deployed path.
3. Use root-based links (`/` and `/calendar/users.html`) consistently with your current structure.

## 7) Local Development

Run locally before deployment:

```bash
npx wrangler dev
```

Requirement:

1. Keep `wrangler.jsonc` and file paths in sync.
2. If you rename/move HTML files, update navbar links accordingly.

## 8) Common Errors and Fixes

1. 404 on Users page
	Cause: old path still used in navbar.
	Fix: update links to current file location.

2. Auth error during deploy
	Cause: not logged in or expired login.
	Fix: run `npx wrangler login` again.

3. Missing files in deployment
	Cause: wrong `assets.directory`.
	Fix: ensure it points to the folder that contains your HTML (`calendar`).

## 9) When to Use Cloudflare Pages vs Workers

1. Use this current setup (Wrangler + assets) when:
	You want a simple static deployment and a `workers.dev` URL.

2. Use Cloudflare Pages when:
	You want Git-integrated automatic builds/previews from the Cloudflare Pages dashboard.

Both are valid. Your current project is already working with the Wrangler static-asset path.
