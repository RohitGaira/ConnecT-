# ConnecT

ConnecT is an AI-powered teammate matching platform for student builders. Users describe project ideas, share skills and commitments, and get matched with complementary teammates. The system uses embeddings, skill overlap, and commitment compatibility to score potential matches, with a feedback loop for forming teams.

## Features

- Profile & skills management backed by Supabase Auth + Postgres.
- Project idea creation with canonicalization and freshness tracking.
- Matching pipeline that scores ideas using embeddings, skills, and commitment levels.
- Match feedback flow (connection sent/accepted/dismissed) and team formation.
- Team dashboards with peer ratings and completion tracking.

## Architecture

- **Frontend**: Static HTML/CSS/JS app in `frontend/`.
- **Backend**: FastAPI service in `backend/` (Vercel-ready entrypoint in `backend/api/index.py`).
- **Database**: Supabase Postgres with pgvector, schema and matching logic in `database/`.
- **Workers**: Supabase Edge Functions in `supabase/functions/`:
  - `embedding-worker`: generates Cohere embeddings.
  - `match-worker`: discovers and scores matches.

## Local Development

### Backend

1. Install dependencies:
   ```bash
   cd backend
   python -m venv .venv
   source .venv/bin/activate
   pip install -r requirements.txt
   ```
2. Configure environment:
   ```bash
   cp .env.example .env
   ```
   Fill in Supabase + Cohere values in `.env`.
3. Apply database SQL (Supabase SQL Editor):
   - `database/schema.sql`
   - `database/matching_logic_fns.sql`
   - `database/cron.sql` (after deploying Edge Functions)
4. Run the API:
   ```bash
   uvicorn app.main:app --reload --port 8000
   ```
   API docs: `http://localhost:8000/docs`

### Frontend

1. Configure client settings:
   ```bash
   cp frontend/js/config.example.js frontend/js/config.js
   ```
   Update Supabase URL/anon key and API base if needed.
2. Serve the static site:
   ```bash
   cd frontend
   python -m http.server 5500
   ```
   Open `http://localhost:5500`.

### Supabase Edge Functions

Deploy the workers in `supabase/functions` (requires Supabase CLI). Ensure these env vars are set for the functions:

- `SUPABASE_URL`
- `SUPABASE_SERVICE_ROLE_KEY`
- `COHERE_API_KEY`

Then run `database/cron.sql` to schedule the workers.

## Testing

```bash
cd backend
python -m pytest
```
