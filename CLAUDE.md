# Startup Idea workspace

Two read-only reference repos live here. Never edit files inside `MiroFish-main/` or `founder-skill-main/`; new work goes in new top-level folders (one per idea, e.g. `ideas/<idea-name>/`).

- `MiroFish-main/` — multi-agent swarm-prediction engine (Flask + Vue). Architecture and style template.
- `founder-skill-main/` — 11 Claude skills + 4 stdlib Python tools that pre-test a business idea (MIT, Jake Schincariol). Outputs markdown/JSON only.

## `/startup <idea>`

Scaffold the idea as a project in MiroFish's style.

1. Re-read `MiroFish-main/` (README, `backend/app/`, `frontend/src/`) for conventions before generating anything.
2. Map the idea onto the five-step workflow (seed -> structure -> simulate/execute -> report -> interact) when it fits; otherwise adapt the shape but keep the rest of the conventions.
3. Confirm scope briefly with the user before generating a large scaffold. Don't create the full tree for a small idea.

MiroFish conventions to copy:
- Backend: Python >=3.11,<3.13 with **uv**; Flask 3 app factory in `app/__init__.py`; one blueprint per domain under `/api/<domain>`; `config.py` loads `.env` via dotenv and exposes `Config.validate()` returning a list of errors; LLM calls through the OpenAI SDK format (`LLM_API_KEY`, `LLM_BASE_URL`, `LLM_MODEL_NAME`); Zep Cloud for graph memory (`ZEP_API_KEY`) when the idea needs it.
- Models: `@dataclass` with `to_dict()` / `from_dict()`, `str` Enum statuses, all-classmethod `Manager` classes, JSON-file persistence under `uploads/`, IDs like `proj_<12 hex>`.
- API envelope: `{"success": bool, "data": ..., "error": ...}`; localized messages via `t('api.key', ...)`.
- Long-running work: `TaskManager` + background threads + per-resource locks; subprocess workers talk over an IPC layer.
- Agents: ReACT with tool-calling and reflection, configurable via env, one JSONL log per run.
- Frontend: Vue 3 `<script setup>` + Vite + vue-router + vue-i18n + axios + d3; one `StepN*.vue` component per workflow step driven by a `MainView`; tiny vanilla `store/` modules (no Vuex/Pinia).
- Look: terminal/console aesthetic, black and white with orange accents, monospace stack (`JetBrains Mono`, `Space Grotesk`, `Noto Sans SC`), glyphs like `◇ ■ ↑ →`, big "START ENGINE →" CTA.
- Root: `concurrently`-driven `npm run dev` (frontend :3000, backend :5001), Dockerfile + docker-compose, `.env.example`, bilingual README, AGPL-3.0.
- Module docstrings are in Chinese in MiroFish; match that only if the user asks, otherwise write English.

## `/visualization <idea>`

Run the founder-skill pack on the idea, then render the results as one HTML Artifact dashboard.

1. Read each `founder-skill-main/skills/founder-*/SKILL.md` and follow it in order: board -> competitors -> consumer -> pricing -> offer -> cfo -> marketing -> brand -> ops -> launch -> plan.
2. Write outputs to `founder/` (inside the idea's folder, or the workspace root if none). Use the real Python tools instead of re-implementing the math:
   - `python3 founder-skill-main/skills/founder-cfo/unit_economics.py founder/numbers.json --out founder/cfo.md`
   - `python3 founder-skill-main/skills/founder-consumer/panel.py init --customer founder/customer.json --pitch founder/pitch.md`
   - `python3 founder-skill-main/skills/founder-pricing/van_westendorp.py founder/panel/answers --price <price>`
   - `python3 founder-skill-main/skills/founder-plan/compile.py --dir founder`
3. Default the consumer panel to `--quick` (20 buyers) unless the user wants the full 100, and say which was used.
4. Load the `artifact-design` and `dataviz` skills, then publish one dashboard Artifact with: verdict banner (Profitable / Not yet / Incomplete), board scorecard (Offers / Monopoly / Product lenses with votes), competitor map, consumer-panel buy/maybe/no bars with top objections and segment buy rates, Van Westendorp chart with the chosen price marked, CFO break-even and year-1 month-by-month line, 30-day launch timeline, and compact tiles for offer / brand / ops / launch. Give the user the link. The markdown stays on disk as the source of truth.

Caveats to keep visible in the dashboard and summaries:
- The board applies published frameworks (Hormozi, Thiel, Isaacson's Jobs biography); it never speaks as those people.
- Panel buy rate is simulated and an upper bound, not a forecast or testimonial.
- Numbers are only as good as the inputs; label estimates as estimates.
- Research uses public sources with real links; never invent competitors, prices or quotes.
- Not financial, legal or tax advice.
