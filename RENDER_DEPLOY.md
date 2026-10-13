# Deploy Campus Pulse on Render (Android-friendly)

This project has a React/Vite frontend and Django API. Deploy them as two Render services from the same GitHub repository.

## 1. Backend Web Service
- New + > Web Service > connect your GitHub repository.
- Root Directory: `backend`
- Runtime: Python 3
- Build Command: `pip install -r requirements.txt`
- Start Command: `python manage.py migrate && python manage.py generate_data && python manage.py run_pipeline && gunicorn campus.wsgi:application --bind 0.0.0.0:$PORT`
- Add environment variables:
  - `DJANGO_SECRET_KEY`: a long random secret (generate a unique value)
  - `DJANGO_DEBUG`: `False`
  - `DJANGO_ALLOWED_HOSTS`: `*` for a demo; ideally replace with your backend Render hostname only
  - `CORS_ALLOWED_ORIGINS`: `https://YOUR-FRONTEND.onrender.com` (after creating the frontend service; no trailing slash)
- Deploy and copy the backend service URL, e.g. `https://your-api.onrender.com`.

## 2. Frontend Static Site
- New + > Static Site > connect the same repository.
- Root Directory: `frontend`
- Build Command: `npm install && npm run build`
- Publish Directory: `dist`
- Environment variable: `VITE_API_BASE_URL` = the backend URL from step 1, e.g. `https://your-api.onrender.com` (no trailing slash).
- Deploy. Copy the frontend URL; that is the public app URL.

## 3. Finish CORS
- Return to backend service environment variables and set `CORS_ALLOWED_ORIGINS` to the exact frontend URL, such as `https://campus-pulse.onrender.com`, without a trailing slash. Save/redeploy.
- Open the frontend URL and test Overview, Students, Student Details, Segments and Data Sources.

## Notes
- The sample data is synthetic, not real student data. Do not upload private student records to this demo.
- SQLite on a free web service may be reset when the service restarts/redeploys; suitable for a hackathon demo, not production. For persistent real data use a managed PostgreSQL database and add authentication before exposing student information.
- Keep `DJANGO_SECRET_KEY` private. Never commit secrets into GitHub.
