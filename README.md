# 🍲 Food Verse AI — Multimodal Food Intelligence Platform

React + REST + CSS animations frontend, Next.js App Router API backend (Flask/FastAPI-compatible service layer), PostgreSQL + Drizzle, **zero paid APIs** via a clearly-separated mock/demo AI service.

## ✨ Features (all 6 epics)

| Epic | Module | Route |
|---|---|---|
| 2 | Food Recognition & Nutrition (calories, P/C/F, fiber, score, insight) | `POST /api/analyze/food` |
| 3 | Freshness & Spoilage (Fresh → Possibly Spoiled + disclaimer) | `POST /api/analyze/freshness` |
| 4a | Restaurant Hygiene scan (score, grade, 4 categories, issues) | `POST /api/analyze/hygiene` |
| 4b | Smart Kitchen Assistant (ingredients/dish/time/skill → recipe) | `POST /api/kitchen/recipe` |
| 5 | Personalized Meal Planner (daily + weekly, Replace/Regenerate/High-Protein) | `POST /api/mealplan` |
| 1,6 | Dashboard, history, toasts, charts, responsive + animations | `GET /api/history` |

Dashboard panels: Food Recognition · Freshness Check · Hygiene Scan · Smart Kitchen · Meal Planner · Recent Analyses · Nutrition Summary · Nutrition analytics · AI Insights + **✨ Try Demo** one-click full demo.

### Quality-of-life features
- 📸 Bundled real sample photography (`public/images/`) powering every Demo button — no external image CDN needed.
- 🗂 **Clickable history → report drawer**: inspect the stored JSON payload, copy or download it, jump back into the module.
- ⧉ **Copy / ⤓ Download report** on every result card (client-side JSON export).
- 💾 **Session persistence** via `localStorage` — refreshing the page keeps your latest analyses + plan; header **Reset** clears them.
- 📊 **Weekly calorie-rhythm chart** (CSS-animated bars) driven by your generated plan, with avg kcal + protein badges.
- 🧭 5-stage pipeline explainer, live stale-safe fallbacks, toasts, skeleton loaders and retry banners.

## 🚀 Quick start

```bash
npm install
cp .env.example .env   # set DATABASE_URL
npx drizzle-kit push   # create tables (analyses, meal_plans)
npm run dev            # http://localhost:3000
```

Production:

```bash
npm run build && npm start
```

## 🔌 REST API

```bash
curl -X POST localhost:3000/api/analyze/food -H 'Content-Type: application/json' \
  -d '{"fileName":"bowl.jpg","hint":"chicken buddha bowl"}'

curl -X POST localhost:3000/api/analyze/freshness -H 'Content-Type: application/json' \
  -d '{"fileName":"berries.jpg","hint":"2 days in fridge","foodType":"berries"}'

curl -X POST localhost:3000/api/analyze/hygiene -H 'Content-Type: application/json' \
  -d '{"fileName":"kitchen.jpg","hint":"cloud kitchen"}'

curl -X POST localhost:3000/api/kitchen/recipe -H 'Content-Type: application/json' \
  -d '{"ingredients":"paneer, capsicum, rice","timeMinutes":25,"skill":"Beginner"}'

curl -X POST localhost:3000/api/mealplan -H 'Content-Type: application/json' \
  -d '{"goal":"Muscle Gain","diet":"High-Protein","caloriesTarget":2600,"proteinTarget":170,"highProtein":true}'

curl localhost:3000/api/history?limit=12
```

## 🐍 Python / Flask reference service (`backend/`)

The stack calls for Python too, so a runnable Flask microservice mirrors the REST contract:

```bash
cd backend
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
python app.py                       # http://127.0.0.1:8000/health
python -m unittest discover -s . -p "test_*.py"   # 6 smoke tests, no extra deps
```

Endpoints: `GET /health`, `POST /analyze/food`, `POST /analyze/freshness`, `POST /analyze/hygiene`,
`POST /kitchen/recipe`, `POST /mealplan` — same request/response shapes as the Next.js routes.
Point the UI at it with `AI_PROVIDER=flask` + `PYTHON_BACKEND_URL=http://127.0.0.1:8000`.
Deploy with `gunicorn -w 2 -b 0.0.0.0:8000 app:app`.

## 🧪 Testing

```bash
./scripts/smoke.sh http://127.0.0.1:3000   # all 7 REST endpoints + sample asset
cd backend && python -m unittest discover -s . -p "test_*.py"
npm exec tsc -- --noEmit && npm run build
```

## 🧠 Mock → real AI

All intelligence lives in **`src/lib/ai-service.ts`** (`analyzeFood`, `analyzeFreshness`, `analyzeHygiene`, `generateRecipe`, `generateMealPlan`).
`callRealModel()` is the single stub to implement. Set `AI_PROVIDER=openai|hf|flask` in `.env`:

- **Flask/FastAPI path:** run your Python vision service (e.g. `POST http://flask:8000/vision/food`), set `PYTHON_BACKEND_URL`, and forward from `callRealModel()` — no route or UI changes needed.
- Real-model candidates: CLIP / MobileNet food-101 classifier, ripeness CNN, YOLO kitchen-hazard detector, LLM (Llama/Mistral) for recipes & plans.

## 🗄 Database

`src/db/schema.ts` → `analyses` (kind, title, score, status, result JSONB) + `meal_plans`. History persists every analysis; falls back to demo seed offline.

## 🎨 UI

Dark glassmorphism, CSS-only animations (float, scan-beam, shimmer skeletons, donut charts, animated bar charts, progress bars, marquee ticker, toasts, report drawer slide-in), scrollable pill nav on mobile and desktop, loading / error / empty states everywhere, keyboard `Esc` closes the report drawer.

## ⚠️ Disclaimer

Visual AI analysis cannot guarantee food safety. When in doubt, throw it out. Not medical advice.

## 📦 Deploy

- Frontend: Vercel/Netlify (`npm run build`).
- Backend: these `/api/*` routes deploy as serverless functions, or port `src/lib/ai-service.ts` logic into Flask/FastAPI and point the frontend `fetch()` at it.
- DB: any Postgres (`DATABASE_URL`) + `npx drizzle-kit push`.
