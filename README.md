# VidPulse AI — Final Version

All-in-one social video summarizer + transcript extractor.
Front-end: single `index.html` (Tailwind, dark theme).
Back-end: FastAPI + yt-dlp + Gemini 2.5 Flash (`backend/`).

## What changed vs the Gemini original

- Front-end is now English-only (the earlier draft had Roman Urdu strings in
  the UI — removed per the product rule).
- X (Twitter) platform detection added (TikTok, YouTube, Instagram Reels,
  Facebook, X).
- Loader now shows live progress steps: Downloading audio → Transcribing →
  Building summary.
- Backend-down errors show an inline error box with a Retry button instead of
  a bare alert. Render free tier sleeps — the first request can take up to a
  minute while the server wakes up.
- Download .MD added next to .TXT.
- Recent videos history (last 5, localStorage, click to reload).
- Backend: videos longer than 30 minutes are rejected with a clear message;
  a real 400 error (e.g. private video) is no longer swallowed into a 500;
  invalid AI JSON is handled; `GET /health` endpoint added.
- `requirements.txt` is pinned to known-good versions.
- Default backend URL is the Render placeholder — replace it in
  API Settings with your real deployed URL.

## Deploy — backend (Render)

1. Create a GitHub repo, e.g. `vidpulse-ai-backend`, and push the `backend/`
   folder contents (`main.py`, `requirements.txt`, `Dockerfile`).
2. Render dashboard → New → Web Service → connect the repo.
   Runtime: Docker. (Render reads the Dockerfile automatically.)
3. Environment → add `GEMINI_API_KEY` = your key from Google AI Studio
   (https://aistudio.google.com — free tier works).
4. Deploy. Copy the service URL, e.g.
   `https://vidpulse-ai-backend.onrender.com`.
5. Check it: open `https://vidpulse-ai-backend.onrender.com/health`
   — it must return `{"status":"ok"}`.

## Deploy — front-end (GitHub Pages)

1. Create a GitHub repo, e.g. `vidpulse-ai`, and upload `index.html` to it.
2. Repo Settings → Pages → Source: Deploy from a branch → main → `/ (root)`.
3. Open the Pages URL, click API Settings, paste your Render URL +
   `/api/extract`, e.g. `https://vidpulse-ai-backend.onrender.com/api/extract`,
   and Save. (The setting is saved in the browser's localStorage.)

## Notes

- The example backend URL printed in the original Gemini artifact
  (`vidpulse-backend-gwcm.onrender.com`) does NOT run this code — its root
  returns 404. Deploy your own backend with the steps above.
- A single HTML file alone cannot fetch or transcribe videos — the backend +
  a Gemini API key are required for the real flow.
- TikTok / Instagram downloads depend on yt-dlp working for those sites;
  if a platform blocks downloads, the backend returns a clear 400 error.
- Render free tier sleeps after inactivity — the first request after a pause
  is slow. That is expected, not a bug.
