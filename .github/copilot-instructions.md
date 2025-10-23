<!-- Auto-generated helper for AI coding agents. Keep concise and actionable. -->
# Copilot / AI agent instructions for this repository

Purpose: Help an AI contributor become productive quickly in this static site + light PHP contact handler project.

- Big picture
  - This repository is a static website (HTML/CSS/JS) with assets grouped under `css/`, `js/`, `img/`, and `fonts/`.
  - A simple PHP contact endpoint lives at `mail/mail.php` which uses PHP's mail() and redirects to `mail-success.html`.
  - A `Dockerfile` builds an Nginx container that serves the repo root (no build step required).
  - CI: the workflow `.github/workflows/cicd.yml` contains a commented-out ECR `build-push` job and an active `deploy` job that SSHes to a remote host using the `SSH_KEY` secret.

- How to run & validate locally (examples)
  - Quick static server (any OS):
    - python: `python -m http.server 8000` (then open `http://localhost:8000`)
  - Run with Docker (matches production container):
    - `docker build -t mediplus:local .`
    - `docker run --rm -p 8080:80 mediplus:local` (visit `http://localhost:8080`)
  - Test PHP contact handler locally (if editing `mail/mail.php`):
    - `php -S localhost:8000 -t .` then visit `contact.html` and submit — note `mail()` requires a configured MTA on the host.

- CI/CD & deployment notes (important)
  - File: `.github/workflows/cicd.yml`
    - The `build-push` job that logs into AWS ECR is commented out. To enable container publish, uncomment and ensure `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` exist in repository secrets.
    - The `deploy` job expects `secrets.SSH_KEY`. It writes this secret to `Dockerhost.pem` and SSHes to `ubuntu@3.94.110.160` to run `docker ps` and `hostnamectl`.
    - Security note: do NOT print private keys in logs. Add the host to `~/.ssh/known_hosts` (via `ssh-keyscan`) and avoid exposing secret contents in workflow logs.

- Project conventions & patterns
  - Vendor libraries are committed under `js/` (minified vendor code such as `jquery.min.js`, `popper.min.js`). Avoid editing vendor/minified files; prefer editing `main.js`, `map-active.js`, or adding new files.
  - Styles: base and vendor CSS in `css/`. Site-level rules live in `style.css` at project root.
  - Page files are top-level HTML (e.g., `index.html`, `contact.html`) — editing these is the standard change surface.
  - Contact handler: `mail/mail.php` expects POST fields `name`, `email`, `phone`, `subject`, `message` and sends email to the placeholder `name@youremail.com`. Replace with configured address or swap to SMTP/Mailer library as needed.

- Integration points to be aware of
  - Google Maps: see `js/map-active.js` — the script uses GMaps and targets the DOM element with id myMap (i.e. id="myMap"). Ensure any Google API keys are injected in templates or replaced where they exist (no key present in repo). Missing API key will break maps on the site.
  - Deployment target: the workflow SSHs to a specific IP (3.94.110.160). Treat this host as production and coordinate with repository owners before making changes to the deploy steps.

- Examples of common edits
  - Change homepage copy: edit `index.html` and corresponding text blocks in `css/` selectors found in `style.css`.
  - Update contact recipient: edit `mail/mail.php` recipient address and optionally replace `mail()` with PHPMailer/SMTP.
  - Add a new asset: place images in `img/` and reference from HTML with relative paths (e.g., `img/your.png`).

- Quick debugging tips
  - If JS features fail: open devtools console in browser; confirm vendor scripts (`jquery.min.js`) load before `main.js`.
  - If contact form doesn't send: test `mail/mail.php` with `php -S` locally and check server mail logs or replace with SMTP to debug.
  - If maps don't render: check browser console for Google Maps API key errors.

- What to avoid and permissions
  - Do not commit secrets or private keys. CI uses `secrets.SSH_KEY`; keep that only in repository secrets.
  - Avoid editing minified vendor files; add or replace non-vendor JS/CSS instead.

If anything here is unclear or you'd like more examples (e.g., standard commit messages, branching rules, or a deploy checklist), tell me which area to expand and I'll update this file.
