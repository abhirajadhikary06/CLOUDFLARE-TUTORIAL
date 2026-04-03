# OpenCalendar Deployment Guide (Cloudflare)

## Cloudflare Pages vs Wrangler Static Assets Setup

**Cloudflare Pages** is Cloudflare’s official platform for hosting static websites, single-page applications (SPAs), and Jamstack projects. It was built to provide a seamless developer experience with the following key benefits:

- **Git integration**: Automatic deployments on every push to GitHub, GitLab, or Bitbucket.
- **Preview deployments**: Unique preview URLs for every branch and pull request.
- **Dashboard UI**: Easy configuration of build commands, environment variables, and custom domains directly from the Cloudflare dashboard.
- **Global CDN + Edge Functions**: Fast performance with automatic caching and the ability to add dynamic behavior via Cloudflare Functions.
- **Analytics, Forms, and more**: Built-in observability, form handling, and additional features without extra configuration.

Pages is ideal when your project grows, involves a team, or when you want automated CI/CD workflows tied to your repository.

This repository, however, uses **Wrangler with static assets** (Cloudflare Workers static asset mode) instead of the Cloudflare Pages dashboard build pipeline. This setup was chosen for:
- Direct CLI-based deployment control (`wrangler deploy`)
- Simpler configuration for purely static sites without needing a build step
- Immediate control over the Workers runtime and compatibility flags
- Deployment to a custom `workers.dev` subdomain

**When to use which:**
- Use **Cloudflare Pages** for Git-connected auto-deploys and preview environments.
- Use the current **Wrangler + assets** setup for lightweight, CLI-driven deployments and fine-grained Workers control.

You can migrate to Cloudflare Pages later if your needs change (see Section 10 for comparison).

## Important Context
This project is currently deployed using Wrangler with static assets (Cloudflare Workers static asset mode), not the Cloudflare Pages dashboard build pipeline.

That means:
1. Files are served from the `calendar` folder.
2. Deployment is done with `wrangler deploy`.
3. The app is available on a `workers.dev` URL.
## 1) Prerequisites

Install and configure these first:

1. Cloudflare account (required for deployment)
2. Node.js 18+ (runtime for Wrangler CLI)
3. npm (package manager, bundled with Node.js)
4. Wrangler CLI (deployment tool)

Check your environment:

```bash
node -v
npm -v
npx wrangler -v
```

## 2) Project Components

Current structure:

```text
pages/
  calendar/
	 index.html
	 users.html
  wrangler.jsonc
  README.md
```

What each component does and why it is needed:

1. `calendar/index.html`
	Main Calendar UI (app landing page).
	Requirement: required.

2. `calendar/users.html`
	Users/participants view.
	Requirement: optional feature page, but required if navbar links to it.

3. `wrangler.jsonc`
	Cloudflare deployment configuration.
	Requirement: required for `wrangler deploy`.

4. `README.md`
	Setup and maintenance documentation.
	Requirement: optional but recommended for contributors.

## 3) wrangler.jsonc Breakdown

Current config:

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

Field-by-field meaning and requirement:

1. `$schema`
	Enables editor validation and autocomplete for the config.
	Requirement: optional (recommended).

2. `name`
	The deployed project/service name.
	Requirement: required.

3. `compatibility_date`
	Locks Cloudflare runtime behavior to a known date.
	Requirement: required.

4. `observability.enabled`
	Enables observability features for logs and insights.
	Requirement: optional.

5. `assets.directory`
	Defines which directory is uploaded as static assets.
	Requirement: required in this setup.

6. `compatibility_flags`
	Enables runtime feature flags such as `nodejs_compat`.
	Requirement: optional, only if your code needs that compatibility.

## 4) First-Time Setup

Run from repository root:

```bash
cd pages
npm init -y
npm install -D wrangler
npx wrangler login
```

Why each step matters:

1. `npm init -y`
	Initializes a Node project in `pages`.

2. `npm install -D wrangler`
	Installs Wrangler locally for consistent team tooling.

3. `npx wrangler login`
	Authenticates the local machine with Cloudflare.

## 5) Local Development

Start local dev server:

```bash
cd pages
npx wrangler dev
```

Requirement:

1. Keep file paths and `assets.directory` aligned.
2. Update links if files are renamed or moved.

## 6) Deployment

Deploy from `pages`:

```bash
npx wrangler deploy
```

On success, Cloudflare returns a `workers.dev` URL.

## 7) Routing Rules for This Repo

With current file placement:

1. Calendar page route: `/`
2. Users page route: `/calendar/users.html`

If you move files again, update navbar links in both pages before deploying.

## 8) Common Issues

1. Users page returns 404
	Cause: old URL in navbar after file move.
	Fix: update links to current route, then redeploy.

2. Deployment auth failure
	Cause: missing or expired login session.
	Fix: run `npx wrangler login` again.

3. Files not showing in production
	Cause: wrong assets directory.
	Fix: make sure `assets.directory` points to `calendar`.

## 9) Cloudflare Pages vs This Setup

Use current setup (Wrangler + assets) when:

1. You want direct CLI deployment.
2. You are fine with a `workers.dev` host.

Use Cloudflare Pages dashboard when:

1. You want Git-connected auto-deploy previews per commit/branch.
2. You want Pages-managed build settings in the UI.

Both are valid. This repository is currently configured for Wrangler static-asset deployment.
