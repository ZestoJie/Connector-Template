# Connection Starter

A deployable full-stack integration starter for hackathon projects. It provides a React frontend, Express API, Gemini-powered claim checker, and optional Brevo and Supabase connectivity checks, with automated CI/CD and deployment configurations for Vercel and Railway.

Use this template to establish, configure, and verify communication between your application and external services before developing your actual product.

> **Project Scope**
>
> This repository is an integration tester and development foundation, not a finished social networking or participant-matching application. It does not include participant accounts, profiles, discovery, or connection requests. These features should be implemented according to the requirements of the project using this starter as the foundation.

---

## Table of Contents

1. [Features](#features)
2. [Technology Stack](#technology-stack)
3. [Architecture Overview](#architecture-overview)
4. [Prerequisites](#prerequisites)
5. [Local Development Setup](#local-development-setup)
6. [Environment Configuration](#environment-configuration)
7. [Running the Application](#running-the-application)
8. [Integration Testing](#integration-testing)
9. [CI/CD Pipeline](#cicd-pipeline)
10. [Automated Tests and Smoke Checks](#automated-tests-and-smoke-checks)
11. [Deployment Guide](#deployment-guide)
12. [Monitoring and Recovery](#monitoring-and-recovery)
13. [Using This as a GitHub Template](#using-this-as-a-github-template)
14. [Suggested Hackathon Development Workflow](#suggested-hackathon-development-workflow)
15. [Available Commands](#available-commands)
16. [Security Considerations](#security-considerations)
17. [Troubleshooting](#troubleshooting)

---

## Features

### Frontend
- React 19 with TypeScript.
- Vite development server and production build.
- API connectivity testing.
- Gemini-powered factual claim checking.
- Operator interface for email and database integration tests.

### Backend
- Express 5 with TypeScript.
- Health-check endpoint.
- Security headers.
- Configurable CORS allow-list.
- API rate limiting.
- Protected integration-testing endpoints.
- Server-side provider credential management.

### AI Integration
- Gemini API for factual claim assessment.
- Tavily API for retrieving web evidence.
- Grounded assessment with source links.
- Server-side API key handling.

### External Services
- Brevo API for test email delivery.
- Supabase PostgreSQL for database connectivity checks.

### DevOps and Deployment
- GitHub Actions automated CI.
- Backend build and automated tests.
- Frontend linting and production build.
- Railway backend deployment.
- Vercel frontend deployment.
- CI-gated production deployments.
- Deployment health checks.
- Monitoring and manual rollback procedures.

---

## Technology Stack

| Category | Technology | Purpose |
|---|---|---|
| Frontend | React 19 | User interface |
| Frontend Language | TypeScript | Type-safe frontend development |
| Frontend Tooling | Vite | Development server and build tool |
| Backend | Express 5 | REST API and server-side logic |
| Backend Language | TypeScript | Type-safe backend development |
| Runtime | Node.js 22.12+ | JavaScript execution environment |
| Package Manager | npm | Dependency management |
| AI Model | Google Gemini | Factual claim assessment |
| Web Research | Tavily | Web evidence retrieval |
| Email Service | Brevo | Transactional test email delivery |
| Database | Supabase PostgreSQL | Database connectivity and table checks |
| Frontend Hosting | Vercel | Frontend deployment |
| Backend Hosting | Railway | Backend deployment |
| CI/CD | GitHub Actions | Automated integration and build checks |
| Version Control | Git and GitHub | Source control and collaboration |

---

## Architecture Overview

The application separates frontend, backend, external services, and deployment automation.

### Application Architecture

```text
                    USER / OPERATOR
                           |
                           v
                  REACT + VITE FRONTEND
                       (Vercel)
                           |
                           | HTTPS API Requests
                           v
                    EXPRESS REST API
                       (Railway)
                           |
          +----------------+----------------+
          |                |                |
          v                v                v
      GEMINI API       TAVILY API       BREVO API
      Claim Check      Web Evidence     Test Email
          |
          |
          +----------------+
                           |
                           v
                    SUPABASE POSTGRES
                    Database Checks
```

### CI/CD Architecture

```text
                DEVELOPER
                    |
                    v
               GIT PUSH
                    |
                    v
              GITHUB REPOSITORY
                    |
                    v
             GITHUB ACTIONS CI
                    |
          +---------+----------+
          |                    |
          v                    v
     BACKEND CHECKS       FRONTEND CHECKS
     - npm ci             - npm ci
     - Build              - Lint
     - Tests              - Build
          |                    |
          +---------+----------+
                    |
                    v
               CI PASSED
                    |
          +---------+----------+
          |                    |
          v                    v
       RAILWAY               VERCEL
       Wait for CI           Deployment Checks
          |                    |
          v                    v
     BACKEND DEPLOY       FRONTEND DEPLOY
```

### How the Components Communicate

1. The frontend sends HTTPS requests to the Railway backend.
2. The backend validates requests and handles application logic.
3. The backend communicates with Gemini and Tavily for claim checking.
4. The backend communicates with Brevo for email testing.
5. The backend connects to Supabase PostgreSQL for database testing.
6. GitHub Actions validates code changes before production deployment.
7. Railway and Vercel deploy their respective services after the configured checks pass.

**Credential handling:** Provider credentials remain on the backend. The browser receives the public API URL and, when an operator performs protected integration checks, a tester token entered by that operator.

---

## Prerequisites

Install the following tools before setting up the project.

| Requirement | Version / Description |
|---|---|
| Node.js | 22.12 or newer |
| npm | Included with Node.js |
| Git | Latest stable version |
| Code Editor | VS Code or equivalent |
| GitHub Account | Repository and CI/CD configuration |
| Vercel Account | Frontend hosting |
| Railway Account | Backend hosting |

### Optional External Services

The following services are only required if you intend to test their corresponding integrations.

| Service | Purpose |
|---|---|
| Google AI Studio | Obtain a Gemini API key |
| Tavily | Obtain a web research API key |
| Brevo | Configure test email delivery |
| Supabase | Configure PostgreSQL connectivity |

---

## Local Development Setup

Follow these instructions to download, configure, and run the application locally.

### 1. Clone the Repository

```bash
git clone <YOUR_REPOSITORY_URL>
```

Navigate into the project directory:

```bash
cd <PROJECT_DIRECTORY>
```

### 2. Install Backend Dependencies

Open a terminal:

```powershell
cd backend
npm ci
```

Create the backend environment file:

```powershell
Copy-Item .env.example .env
```

### 3. Install Frontend Dependencies

Open a second terminal from the project root:

```powershell
cd frontend
npm ci
```

Create the frontend environment file:

```powershell
Copy-Item .env.example .env
```

### 4. Configure Environment Variables

Configure the backend and frontend environment files before starting the application.

Refer to [Environment Configuration](#environment-configuration) for the complete variable reference.

For local development, configure:

**Frontend (`frontend/.env`)**

```env
VITE_API_URL=http://localhost:3000
```

**Backend (`backend/.env`)**

```env
TESTER_TOKEN=<GENERATED_RANDOM_TOKEN>
```

Generate a secure tester token using:

```bash
node -e "console.log(require('node:crypto').randomBytes(32).toString('hex'))"
```

Copy the generated value into the backend environment file.

### 5. Configure External Services

If you intend to test the external integrations, configure the corresponding backend credentials:

- Gemini API key.
- Tavily API key.
- Brevo API key and verified sender email.
- Supabase PostgreSQL connection URL.

Do not place provider credentials in the frontend environment file.

---

## Environment Configuration

The project uses separate environment files for frontend and backend configuration.

Create local `.env` files using the committed `.env.example` files.

### Frontend Environment Variables

File: `frontend/.env`

| Variable | Description | Required |
|---|---|---|
| `VITE_API_URL` | Public API origin used by the frontend | Yes |

Example:

```env
VITE_API_URL=http://localhost:3000
```

For production, replace the local URL with the public Railway API URL.

### Backend Environment Variables

File: `backend/.env`

| Variable | Description | Required |
|---|---|---|
| `NODE_ENV` | Application environment (`development` or `production`) | Yes |
| `PORT` | Backend listening port; Railway supplies this in deployment | Platform-dependent |
| `FRONTEND_URL` | Allowed frontend origin for CORS; multiple comma-separated origins are supported | Yes |
| `TESTER_TOKEN` | Protects integration-testing endpoints | For protected checks |
| `RATE_LIMIT_MAX` | Maximum API requests allowed within the configured window | Optional |
| `RATE_LIMIT_WINDOW_SEC` | Rate-limit window duration in seconds | Optional |
| `GEMINI_API_KEY` | Server-only Gemini API key | For claim checking |
| `GEMINI_MODEL` | Gemini model override; defaults to `gemini-3.5-flash-lite` | Optional |
| `TAVILY_API_KEY` | Server-only Tavily API key for web evidence retrieval | For claim checking |
| `BREVO_API_KEY` | Brevo v3 API key used with the `api-key` header | For email testing |
| `BREVO_SENDER_EMAIL` | Verified sender email address in Brevo | For email testing |
| `SUPABASE_DB_URL` | Supabase PostgreSQL connection URL | For database testing |

Example:

```env
NODE_ENV=development
PORT=3000
FRONTEND_URL=http://localhost:5173

TESTER_TOKEN=<GENERATED_RANDOM_TOKEN>

RATE_LIMIT_MAX=100
RATE_LIMIT_WINDOW_SEC=60

GEMINI_API_KEY=<YOUR_GEMINI_API_KEY>
GEMINI_MODEL=gemini-3.5-flash-lite

TAVILY_API_KEY=<YOUR_TAVILY_API_KEY>

BREVO_API_KEY=<YOUR_BREVO_API_KEY>
BREVO_SENDER_EMAIL=<YOUR_VERIFIED_SENDER_EMAIL>

SUPABASE_DB_URL=<YOUR_SUPABASE_POSTGRES_CONNECTION_URL>
```

Only configure the integrations your project needs.

### Important Configuration Notes

**Brevo**
- Use a Brevo v3 API key.
- Do not use an SMTP key for the current API integration.
- Verify your sender email address in Brevo.

**Supabase**
- The current PostgreSQL smoke test uses `SUPABASE_DB_URL`.
- The Supabase client SDK variables `SUPABASE_URL` and `SUPABASE_SERVICE_ROLE_KEY` are not required for these checks.

**Gemini and Tavily**
- Both API keys must remain on the backend.
- Never expose these credentials through `VITE_` environment variables.

**Vite Environment Variables**

Vite embeds `VITE_` variables into the frontend bundle during the build process. Treat all frontend environment variables as publicly accessible.

---

## Running the Application

Start the frontend and backend independently during local development.

### 1. Start the Backend

Open a terminal:

```powershell
cd backend
npm run dev
```

The API should be available at:

```text
http://localhost:3000
```

### 2. Start the Frontend

Open another terminal:

```powershell
cd frontend
npm run dev
```

Vite will display the local development URL.

Default:

```text
http://localhost:5173
```

### 3. Verify the API Connection

Open the frontend URL in your browser.

Select **Check API connection** to verify that the frontend can communicate with the backend.

If the connection succeeds, the frontend-to-backend communication and CORS configuration are working.

### 4. Test External Integrations

Once the API is reachable, use the operator interface to test the configured services.

Refer to [Integration Testing](#integration-testing).

---

## Integration Testing

The application provides an operator interface for validating communication with external services.

These endpoints are intended for development and deployment verification, not as public product features.

### 1. API Connectivity Test

Select **Check API connection**.

This checks:

- Whether the Railway API is reachable.
- Whether the backend health endpoint responds.
- Whether the browser can communicate with the API.
- Whether the configured CORS policy allows the frontend origin.

### 2. API Rate-Limit Test

Select **Test rate limit**.

This sends up to 75 lightweight health-check requests and stops when the API returns HTTP 429.

Important considerations:

- The requests share the same per-IP rate-limit quota as other API requests.
- Running this test can temporarily consume the available API quota.
- Avoid repeatedly executing this test against production services.

### 3. Gemini Claim Checker

Enter a factual statement in **Check a claim**.

The backend uses:

- Gemini for claim assessment.
- Tavily for retrieving supporting web evidence.

The result provides a grounded assessment and source links.

This endpoint uses the configured API rate limit and does not require the tester token.

### 4. Brevo Email Test

To test email delivery:

1. Enter the backend `TESTER_TOKEN`.
2. Enter a recipient email address.
3. Select **Send test email**.
4. Check the recipient's inbox.

Ensure that the Brevo API key and verified sender email are configured on the backend.

### 5. Supabase PostgreSQL Test

The database integration check validates the backend connection to Supabase PostgreSQL.

Available operations:

- Check Supabase PostgreSQL connectivity.
- Create the dedicated smoke-test table.
- Delete the dedicated smoke-test table.

The dedicated table is:

```sql
public.social_platform_connection_smoke_test
```

Important safeguards:

- Table operations require `TESTER_TOKEN`.
- The browser cannot supply an arbitrary table name.
- Deleting the table requires browser confirmation.
- The deletion operation only drops the dedicated smoke-test table.
- Use a development database, never a production database containing application data.

---

## CI/CD Pipeline

The project uses GitHub Actions for continuous integration and provider-native deployment checks for continuous deployment.

This ensures that code changes pass the configured quality checks before reaching production.

### Phase 0: Continuous Integration (CI)

GitHub Actions automatically executes the configured workflows when:

- Code is pushed to `main`.
- A pull request is opened or updated.

The pipeline performs the following checks:

**Backend**
- Installs dependencies using `npm ci`.
- Builds the TypeScript application.
- Runs automated tests using `npm test`.

**Frontend**
- Installs dependencies using `npm ci`.
- Runs linting using `npm run lint`.
- Generates a production build using `npm run build`.

These workflows do not require deployment credentials.

### Configure Railway CI Deployment Gate

1. Open the Railway project.
2. Select the backend service.
3. Navigate to the service deployment settings.
4. Set the GitHub trigger branch to `main`.
5. Enable **Wait for CI**.

Railway will wait for the GitHub Actions workflow associated with the commit.

If a required workflow fails, Railway skips the deployment.

### Configure Vercel Deployment Checks

1. Open the Vercel project.
2. Navigate to **Settings → Git**.
3. Set `main` as the production branch.
4. Navigate to the project's **Deployment Checks** settings.
5. Add the following GitHub Actions checks:
   - `Backend build`
   - `Frontend lint and build`

Vercel will prevent production promotion to the production domain until the configured checks pass.

### CI/CD Workflow Behavior

| Event | GitHub Actions | Railway | Vercel |
|---|---|---|---|
| Push to `main` | Runs CI | Waits for CI | Runs production checks |
| Pull request | Runs CI | No production deployment | Preview deployment behavior is managed by Vercel |
| CI failure | Reports failure | Skips deployment | Blocks production promotion |
| CI success | Reports success | Deployment can proceed | Production promotion can proceed |

**Important:** The GitHub workflow runs CI for pushes to `main` and pull requests. Vercel preview deployments for other branches remain managed by Vercel.

No Railway or Vercel deployment tokens are required in GitHub Actions.

---

## Automated Tests and Smoke Checks

### Phase 2: Testing and Deployment Health Checks

The backend CI job runs automated tests without contacting external providers.

The test suite covers:

| Test | Description |
|---|---|
| Health endpoint | Verifies the API health response |
| Tester-token rejection | Ensures protected endpoints reject invalid credentials |
| Claim validation | Validates submitted claims |
| Grounded-source parsing | Checks evidence and source parsing |
| API rate limiting | Verifies the configured request limit |

The tests do not contact:

- Gemini.
- Brevo.
- Supabase.

This allows the automated test suite to run without requiring external service credentials.

### Railway Deployment Health Check

Railway's deployment health check probes:

```text
/api/health
```

The health check runs against each new backend deployment before Railway marks it active.

The endpoint also reports:

```text
RAILWAY_GIT_COMMIT_SHA
```

as the deployed backend's `version` when deployed from GitHub.

This makes it possible to identify which Git commit is currently running.

### Vercel Production Deployment Checks

Vercel uses the Phase 1 GitHub Actions deployment checks to gate production promotion.

This approach avoids creating a separate GitHub Actions workflow that waits for deployment completion while Railway itself is waiting for CI.

---

## Deployment Guide

The production architecture uses:

- Vercel for the frontend.
- Railway for the backend.
- GitHub Actions for automated CI.
- Supabase for PostgreSQL.
- Gemini and Tavily for AI-assisted claim checking.
- Brevo for email delivery.

### 1. Deploy the Backend to Railway

1. Create or open a Railway project.
2. Connect the GitHub repository.
3. Create a service using the `backend` directory.
4. Configure the service's build and start commands:

Build:

```bash
npm run build
```

Start:

```bash
npm start
```

5. Configure the backend environment variables using `backend/.env.example`.
6. Set:

```env
NODE_ENV=production
FRONTEND_URL=https://<YOUR-VERCEL-DOMAIN>
```

7. Configure the required provider credentials.
8. Generate a public Railway API domain.
9. Enable the Railway health check for:

```text
/api/health
```

10. Configure the GitHub trigger branch as `main` and enable **Wait for CI**.

### 2. Deploy the Frontend to Vercel

1. Open Vercel.
2. Import the GitHub repository.
3. Set the project root directory to:

```text
frontend
```

4. Configure the production environment variable:

```env
VITE_API_URL=https://<YOUR-RAILWAY-API-DOMAIN>
```

5. Deploy the frontend.
6. Open the generated Vercel URL.
7. Verify that the frontend can communicate with Railway.

**Important:** Vite embeds `VITE_API_URL` during the build. Whenever this value changes, rebuild and redeploy the frontend.

### 3. Configure Production CI/CD

For Railway:

- Set the GitHub trigger branch to `main`.
- Enable **Wait for CI**.

For Vercel:

- Set `main` as the production branch.
- Add the `Backend build` check.
- Add the `Frontend lint and build` check.

This ensures that both deployment providers use the intended CI checks before production deployment or promotion.

### 4. Verify Production Services

After deployment, verify the following:

| Component | Verification |
|---|---|
| Vercel frontend | Open the deployed website |
| Railway backend | Access `/api/health` |
| Frontend-to-backend connection | Use Check API connection |
| Gemini | Submit a factual claim |
| Tavily | Verify source retrieval through claim checking |
| Brevo | Send a protected test email |
| Supabase | Run the database connectivity check |

Do not assume that a successful deployment automatically means every external integration is configured correctly.

Run the integration checks manually.

---

## Monitoring and Recovery

### Phase 3: Monitoring and Rollback

The project supports manual operational monitoring and recovery procedures.

Configure read-only uptime monitors for:

- The production frontend.
- The production backend API.

Recommended monitoring targets:

| Service | Monitoring Target |
|---|---|
| Vercel | Production frontend URL |
| Railway | `/api/health` endpoint |

### Monitoring Setup

1. Choose an external uptime-monitoring provider.
2. Create a monitor for the production frontend.
3. Create another monitor for the Railway health endpoint.
4. Configure the desired monitoring interval.
5. Configure alert notifications.
6. Verify that alerts are received when a monitored service becomes unavailable.

External monitoring and alert accounts must be configured by an operator.

### Rollback and Recovery

For deployment investigation and recovery, follow:

[`docs/operations.md`](./docs/operations.md)

The operations runbook covers:

- Expected health-check responses.
- Monitoring and alert configuration.
- Deployment investigation.
- Service recovery.
- Manual provider rollback procedures.

**Note:** Automatic rollback is not configured. Recovery and rollback are performed manually by an operator.

---

## Using This as a GitHub Template

This repository can be reused as a starting point for different hackathon projects.

### 1. Create a GitHub Repository

Create a new GitHub repository and push the starter code to its `main` branch.

### 2. Enable Template Repository

Navigate to:

**Settings → General → Template repository**

Enable the template repository option.

### 3. Create a Repository from the Template

When starting a new project:

1. Open the template repository.
2. Select **Use this template**.
3. Select **Create a new repository**.
4. Enter the new repository name.
5. Choose the repository visibility.
6. Create the repository.

### 4. Customize the New Project

Update the following:

- Project name and README.
- Frontend branding and application identity.
- Backend service identity.
- Environment configurations.
- Smoke-test table name, including its corresponding backend implementation and documentation.

### 5. Configure Deployment Services

Connect the new repository to:

- Railway.
- Vercel.
- GitHub Actions.

Configure the required environment variables through the respective provider dashboards.

Never copy real `.env` files into the new repository.

### 6. Begin Product Development

Add the actual project functionality and tests incrementally.

Keep the CI pipeline passing as new features are introduced.

---

## Suggested Hackathon Development Workflow

The starter provides the technical foundation. The actual application functionality should be developed according to the hackathon's requirements.

### Phase 1: Define the Product

1. Identify the problem the application aims to solve.
2. Define the target users.
3. Establish the minimum viable product (MVP).
4. Identify the primary user journey.
5. Prioritize features based on available development time.

### Phase 2: Develop Core Functionality

1. Define the application data model.
2. Implement the backend API endpoints.
3. Develop the frontend user interface.
4. Connect the frontend to the backend.
5. Implement authentication and authorization before storing private user data.

### Phase 3: Add Optional Features

Depending on the product requirements, implement:

- Participant accounts.
- User profiles.
- Participant discovery.
- Connection requests.
- AI-powered features.
- Social media integrations.
- Email notifications.
- Additional external services.

These are potential product features, not existing functionality provided by this starter.

### Phase 4: Test and Deploy

1. Test the main user journey.
2. Validate frontend and backend communication.
3. Verify database operations.
4. Run automated tests.
5. Verify the production deployment.
6. Prepare safe demonstration data.
7. Test the complete demo flow using a fresh deployment.

**Recommendation:** Prioritize a working end-to-end MVP over integrating every available service.

---

## Available Commands

The following commands are available for development and validation.

### Backend Commands

Run these commands from the `backend` directory.

| Command | Description |
|---|---|
| `npm ci` | Install dependencies from the lockfile |
| `npm run dev` | Start the backend development server |
| `npm run build` | Compile the TypeScript backend |
| `npm start` | Start the production backend |
| `npm test` | Run automated backend tests |

### Frontend Commands

Run these commands from the `frontend` directory.

| Command | Description |
|---|---|
| `npm ci` | Install dependencies from the lockfile |
| `npm run dev` | Start the Vite development server |
| `npm run lint` | Run frontend lint checks |
| `npm run build` | Generate the production frontend build |

---

## Security Considerations

Follow these guidelines when configuring and operating the application.

### Credential Protection

- Never commit `.env` files.
- Never expose provider API keys through frontend code.
- Never use `VITE_` variables for sensitive credentials.
- Keep Gemini, Tavily, Brevo, and database credentials on Railway.
- Store production secrets in provider environment-variable settings.

### API Protection

- Keep integration-testing endpoints protected by `TESTER_TOKEN`.
- Restrict access to the operator interface.
- Maintain the configured CORS allow-list.
- Keep rate limiting enabled.
- Avoid exposing integration-testing endpoints as public product features.

### Database Safety

- Use a dedicated development database for smoke tests.
- Do not run destructive integration checks against production databases.
- Keep database credentials restricted to the backend.
- Do not allow the browser to supply arbitrary table names for database operations.

### CI/CD Security

- Do not commit deployment credentials.
- Do not expose provider secrets in GitHub Actions logs.
- Use provider-native deployment integrations.
- Require CI checks before production deployment.

---

## Troubleshooting

| Problem | Possible Cause | Recommended Solution |
|---|---|---|
| Frontend cannot connect to backend | Incorrect API URL | Verify `VITE_API_URL` |
| CORS error | Frontend origin is not allowed | Verify `FRONTEND_URL` |
| API unavailable | Railway service is not running | Check Railway deployment logs |
| Gemini request fails | Invalid API key or model configuration | Verify Gemini environment variables |
| No claim sources returned | Tavily configuration or retrieval issue | Verify `TAVILY_API_KEY` |
| Brevo email fails | Invalid API key or unverified sender | Verify Brevo credentials and sender |
| Supabase connection fails | Incorrect database URL or unavailable database | Verify `SUPABASE_DB_URL` |
| Protected endpoint returns HTTP 401/403 | Missing or invalid tester token | Verify `TESTER_TOKEN` |
| API returns HTTP 429 | Rate limit exceeded | Wait for the configured rate-limit window |
| GitHub Actions fails | Build, lint, or test failure | Inspect the failing workflow job |
| Railway deployment skipped | CI workflow failed | Fix the failed GitHub Actions check |
| Vercel production deployment blocked | Required deployment check failed | Inspect the configured GitHub checks |
| Frontend still uses an old API URL | Vite build-time configuration | Update `VITE_API_URL` and redeploy |

---

## Final Verification Checklist

Before considering the starter ready for a hackathon project, verify the following:

### Local Development

- [ ] Backend dependencies installed.
- [ ] Frontend dependencies installed.
- [ ] Backend environment variables configured.
- [ ] Frontend environment variables configured.
- [ ] Backend starts successfully.
- [ ] Frontend starts successfully.
- [ ] Frontend can communicate with the backend.

### External Integrations

- [ ] Gemini claim checker works.
- [ ] Tavily source retrieval works.
- [ ] Brevo test email succeeds.
- [ ] Supabase PostgreSQL connection succeeds.
- [ ] Database smoke-test table can be created and deleted.

### CI/CD

- [ ] GitHub Actions workflows execute successfully.
- [ ] Backend build and tests pass.
- [ ] Frontend lint and build pass.
- [ ] Railway Wait for CI is enabled.
- [ ] Vercel production deployment checks are configured.

### Production Deployment

- [ ] Backend deployed to Railway.
- [ ] Frontend deployed to Vercel.
- [ ] Production environment variables configured.
- [ ] Frontend-to-backend connection verified.
- [ ] Railway health check responds successfully.
- [ ] Monitoring configured.
- [ ] Operations and rollback procedures reviewed.

---

## Documentation and References

Consult the official documentation for additional configuration and troubleshooting guidance.

| Technology | Documentation |
|---|---|
| React | https://react.dev/ |
| TypeScript | https://www.typescriptlang.org/docs/ |
| Vite | https://vite.dev/guide/ |
| Express | https://expressjs.com/ |
| Node.js | https://nodejs.org/docs/ |
| Google Gemini | https://ai.google.dev/gemini-api/docs |
| Tavily | https://docs.tavily.com/ |
| Brevo | https://developers.brevo.com/ |
| Supabase | https://supabase.com/docs |
| Railway | https://docs.railway.com/ |
| Vercel | https://vercel.com/docs |
| GitHub Actions | https://docs.github.com/en/actions |

For project-specific operational procedures, refer to [`docs/operations.md`](./docs/operations.md).

---

**Connection Starter** — A reusable foundation for connecting, testing, deploying, and expanding full-stack hackathon applications.

Use only the integrations your project requires, customize the starter according to your product requirements, and maintain passing CI checks throughout development.
