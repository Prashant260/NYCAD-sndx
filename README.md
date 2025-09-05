# NYCAD Analytics Dashboard

- Data source: posgres SQL
- Backend: Node.js + Express + Postgres, daily sync job
- Frontend: React + Tailwind CSS
- Orchestration: docker-compose

## Project Structure

```
/backend
  src/
    server.js
    setup/
      db.js
      init.sql
      routes/
        drivers.js
        stats.js
      services/
        fetchAndStore.js
      utils/
        validation.js
  package.json
  Dockerfile
  .env.example
/frontend
  src/
    App.jsx
    main.jsx
    styles.css
    pages/
      Dashboard.jsx
      Search.jsx
    components/
      BoroughChart.jsx
    lib/
      api.js
  package.json
  Dockerfile
  tailwind.config.js
  postcss.config.js
  vite.config.js

docker-compose.yml
README.md
```

## Backend Setup (Local)

1. Create `backend/.env` from `.env.example` and adjust if needed.
2. Ensure Postgres is running and accessible with the provided credentials.
3. Install deps and start:

```bash
cd backend
npm install
npm run seed # optional: one-time sync
npm run dev  # starts on http://localhost:4000
```

### API Endpoints

- `GET /drivers?borough=Queens&search=smith&page=1&limit=25` — list drivers
- `GET /drivers/:license` — single driver by license
- `GET /stats` — totals and borough breakdown

## Frontend Setup (Local)

```bash
cd frontend
npm install
# Set VITE_API_BASE_URL in .env if backend not on http://localhost:4000
npm run dev  # http://localhost:5173
```

## Run with docker-compose

```bash
docker-compose up --build
```

- Frontend: http://localhost:5173
- Backend: http://localhost:4000
- Postgres: localhost:5432 (user `postgres`, password `postgres`, db `fhv`)

## Notes

- The backend schedules a daily sync (default 3 AM UTC) using `CRON_SCHEDULE` env var.
- You can force a one-time sync by running `npm run seed` in `backend/`.
- Secrets/DB credentials should be managed via environment variables.

# Continuous Integration (CI) Pipeline

To make sure the project stays stable and nothing breaks when new code is pushed, I set up a CI pipeline using GitHub Actions.
You can find the workflow file here: .github/workflows/ci.yml.

🔄 When does it run?

The pipeline runs automatically whenever:

I push code to the main branch

Someone opens a pull request into main

This way, every change is tested before it goes live.

# What happens in the workflow?
🔹 Backend job

Spins up a fresh PostgreSQL (v17) database in the GitHub Actions runner.

Installs backend dependencies.

Runs all backend tests to make sure APIs and DB logic work as expected.

🔹 Frontend job

Installs frontend dependencies.

Builds the React app to confirm there are no errors.

So in short → the backend is tested, and the frontend is built on every commit.

✅ Why is this useful?

I don’t have to manually test things every time I change the code.

If something is broken, the pipeline fails immediately and I know where to look.

It makes collaboration easier because everyone’s changes are verified automatically.

📝 A small note

I removed the database seeding step from the CI pipeline because it was making the runs heavy and slow.
If you want to try the project locally and need sample data, you can still run this manually:

cd backend
npm run seed


That will fetch and load data into your local Postgres database.