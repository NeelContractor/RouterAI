# RouterAI

An open-source AI chat application that routes your prompts to multiple LLM providers — all in one place.

## What is RouterAI?

RouterAI is a fast and sleek AI chat application that lets you talk to multiple AI models from different providers (OpenAI, Google, Anthropic) through a single, unified interface.

## Why RouterAI?

It supports different LLMs, responds fast, is user friendly, has customization, and is affordable.
  - ✅ **Open-Source** – No hidden agendas, fully transparent.
  - 🚀 **Developer-Friendly** – Built with extensibility and integrations in mind.

## Tech Stack

RouterAI is built with modern and reliable technologies:

- **Frontend**: Next.js, TypeScript, TailwindCSS, Shadcn UI
- **Backend**: tRPC, Prisma ORM
- **Database**: PostgreSQL
- **Authentication**: Google OAuth
<!-- - **Testing**: Jest, React Testing Library -->

## Getting Started

### Prerequisites

**Required Versions:**

- Node.js >= 18.0.0
- bun >= 1.0.0
- Docker >= 20.10.0

Before running the application, you'll need to set up several services and environment variables:

For more in-depth information on environment variables, please refer to the [Environment Variables](#environment-variables) section.


1. **Setup Local**

   - Make sure you have [Docker](https://docs.docker.com/get-docker/), [NodeJS](https://nodejs.org/en/download/), and [bun](https://bun.sh/docs/installation) installed.
   - Open codebase as a container in [VSCode](https://code.visualstudio.com/) or your favorite VSCode fork.
   - Run the following commands in order to populate your dependencies and setup docker

     ```
     bun install
     ```

   - Run the following commands if you are unable to start any of the services

     ```
     rm -rf node_modules
     ```

2. **Next Auth Setup**

   - Open the `.env` file and change the AUTH_SECRET to string given below.

     ```env
     AUTH_SECRET= 'authjs.session-token'
     ```

3. **Google OAuth Setup**

   - Go to [Google Cloud Console](https://console.cloud.google.com)
   - Create a new project
   - Create OAuth 2.0 credentials (Web application type)
   - Add authorized redirect URIs:
     - Development:
       - `http://localhost:3000`
       - `http://localhost:3000/api/auth/callback/google`
     - Production:
       - `https://your-production-url`
       - `https://your-production-url/api/auth/callback/google`
   - Add to `.env`:

     ```env
     GOOGLE_CLIENT_ID=your_client_id
     GOOGLE_CLIENT_SECRET=your_client_secret
     ```
### Environment Variables

Copy `.env.example` to `.env` and configure the following variables:

```env
# Auth
AUTH_TRUST_HOST=true
AUTH_SECRET=            # Required: Secret key for authentication (generate with `openssl rand -base64 32`)
SECRURE_AUTH_SECRET=    # Optional: Cookie name for the secure auth session
NEXT_PUBLIC_APP_URL=    # App URL, e.g. http://localhost:3000
NEXTAUTH_URL=           # Auth URL, e.g. http://localhost:3000

# Google OAuth ( Required )
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=

# Cloudflare Turnstile ( Required )
TURNSTILE_SITE_KEY=  # https://dash.cloudflare.com/?to=%2F%3Aaccount%2Fturnstile
NEXT_PUBLIC_TURNSTILE_SITE_KEY=
TURNSTILE_SITE_SECRET=

# Stripe ( Optional )
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=
STRIPE_SECRET_KEY=
STRIPE_WEBHOOK_SECRET=

# Upstash Redis ( Optional - used for rate limiting )
UPSTASH_REDIS_REST_URL=
UPSTASH_REDIS_REST_TOKEN=

# Mail ( Optional )
MAIL_APP_USER=
MAIL_APP_PASSWORD=

# Database ( Required )
DATABASE_URL=

# OpenRouter ( Required - powers the free-tier models)
OPENROUTER_API_KEY=

# Discord webhook ( Optional - feedback notifications)
WEBHOOK_URL=
```

All variables marked **Optional** can be left empty — the app will run without them.

Run the database migration with `bunx prisma migrate dev` and generate the Prisma client with `bunx prisma generate`.


### Running Locally

Run the development server:

```bash
bun dev
```
Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

### Running with Docker (Recommended)

The whole app (Next.js + PostgreSQL) is containerized. A single command builds the image, starts the database, applies migrations, and serves the site.

**First run / after code or `.env` changes:**

```bash
docker compose up --build
```

**Subsequent runs (no code changes):**

```bash
docker compose up
```

Then open [http://localhost:3000](http://localhost:3000).

| Action | Command |
|---|---|
| Run in background | `docker compose up -d` |
| Stop everything | `docker compose down` |
| Stop and delete the database | `docker compose down -v` |
| View app logs | `docker compose logs -f app` |
| Rebuild after changes | `docker compose up --build` |
| Full reset (fresh DB) | `docker compose down -v && docker compose up --build` |

**How it works:**

- The `app` service builds the Next.js image, waits for PostgreSQL to be healthy, applies migrations (`prisma migrate deploy`), then starts the server on port `3000`.
- The `db` service runs PostgreSQL on port `5432` with a persistent volume.
- Environment variables are loaded from your local `.env` file at both build and runtime:
  - **`NEXT_PUBLIC_*` variables** are inlined into the client bundle during the Docker build — edit `.env` and rebuild (`docker compose up --build`) for changes to take effect.
  - **Server-side variables** are read at runtime from `.env` — only the app container needs to restart.
- Empty/unset variables fall back to safe placeholders during the build, so you don't need every key configured to build or start the app.

**Important:** `.env` is gitignored and excluded from the Docker build context — never commit it. It holds your real secrets (Google, Stripe, OpenRouter, etc.).

## Contribute

Contributions are welcome!