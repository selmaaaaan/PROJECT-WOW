# UNSEEN Discover

A dedicated student skills and opportunity application. The general UNSEEN website and resource tools are no longer part of this app.

## Run

Requires Node.js 24 with built-in SQLite. No package installation is needed.

```powershell
node server.mjs
```

Open http://127.0.0.1:4173. Use **Set up institution** to create your first management account. Management receives the student join code and can generate email-bound, single-use invitations for teachers and other managers. No test accounts are seeded into the real app.

## Working flows

- Student registration, login, skill selection, optional directory visibility, profile editing, and guided skill exploration.
- Teachers and management publish webinars, contests, projects, and hiring opportunities.
- Publishing generates one in-app suggestion for each student whose selected skills overlap. All students can browse and apply regardless of matching.
- Teachers search opted-in student profiles, invite students to their opportunities, review applications, and mark a student selected. Selection is a human decision, not an AI ranking or an employment contract.
- Student inbox shows matching opportunities, invitations, and application status updates.
- Management creates staff invitation codes and rotates the student join code.
- Exactly one primary navigation bar, at the bottom, with role-specific tabs. No top hamburger or old Tools tab.

## Backend

`backend.mjs` provides the HTTP API and persists data to `data/discover.sqlite`. Passwords use salted scrypt hashes. Session tokens are random, hashed in SQLite, and sent in HttpOnly SameSite cookies. Staff permissions and opportunity ownership are checked server-side. Cross-origin mutations are rejected. Authentication and AI endpoints have rate limits. The application is one institution per deployment.

The previous browser-local prototype data is not silently imported. Accounts start fresh in the server database. Keep the data directory private and back it up. Never commit it or API secrets.

## Optional AI

Copy `.env.example` to `.env` and configure your own `OPENAI_API_KEY` and `OPENAI_MODEL`, then start:

```powershell
node --env-file=.env server.mjs
```

The server uses the OpenAI Responses API: https://developers.openai.com/api/reference/resources/responses/methods/create

AI is opt-in per reflection. Only that reflection is sent, with `store: false`; profile and account details are not added to the request. Model output is restricted to the supported skill catalog and does not select applicants. Without a key/model, guided keyword suggestions and all core workflows remain available. No API key is included or supplied by the assistant. Live AI calls have not been tested because no key was provided.

## Validation

Run `node test-api.mjs`. The integration suite uses a separate test database and port 4174. It verifies registration, staff invitations, authorization, ownership, matching and deduplication, application decisions, directory opt-out, AI fallback, cross-origin rejection, logout, restart persistence, and SPA direct routes.

## Deployment limitations

The app is running locally. The old static Sites manifest cannot host the new Node/SQLite backend; do not publish `dist` alone. Production needs a persistent Node host or a port to a Worker-compatible database/runtime. Complete initial management setup before exposing a server. For production, configure HTTPS, `COOKIE_SECURE=true`, a fixed `APP_ORIGIN`, persistent storage, backups, and institution policies. Password reset/email verification and email/SMS delivery are not implemented. Notifications currently work inside the app only. Staff invitation codes must be shared by management; the app does not send them by email.
