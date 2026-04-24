# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository status

The repo currently contains no source code — only `README.md` (stub) and `CLAUDE 2.pdf`, the full product specification for **Mental Buddy**, a React Native (Expo) mental-health learning app. Treat the PDF as the source of truth for product scope and conventions until the codebase is initialized. When initializing, follow the stack and structure below; do not invent alternative architectures.

## Planned tech stack

- **Frontend:** React Native via Expo
- **Navigation:** React Navigation (Bottom Tabs + Stack)
- **State:** Zustand (one store per domain; see `app/store/`)
- **Local storage:** AsyncStorage + `expo-sqlite` (no cloud by default)
- **Audio:** Expo AV
- **Animation:** React Native Reanimated + Lottie
- **Charts:** Victory Native
- **Backend (optional, only for the community feature):** Node.js + Express
- **Language:** German first (`de`); structure copy for later i18n (`en`).

No `package.json` exists yet. When scaffolding, use `npx create-expo-app` with TypeScript and align directory layout with the structure below.

## Project structure (target)

```
app/
  screens/      OnboardingScreen, HomeScreen, LibraryScreen (Verstehen),
                PracticeScreen (Üben), CharacterScreen, PlannerScreen,
                ReflectScreen, HelpScreen, AcuteHelpScreen, CommunityScreen
  components/   SkillCard, ExercisePlayer, CharacterAvatar, GoalTracker,
                WeeklyPlanner, EnergyBar, ProgressReport, TherapyHint,
                HelpNowButton, ModeToggle
  data/         skills.ts, topics.ts, enemies.ts, equipment.ts,
                acuteStates.ts, labels.ts
  store/        userStore, progressStore, plannerStore, reflectionStore,
                settingsStore
  utils/        spacedRepetition.ts (SM-2), reportGenerator.ts (PDF),
                therapyMatcher.ts, acuteMatcher.ts
assets/         animations/ (Lottie JSON), audio/ (MP3), character/ (sprites)
```

## Architecture: the six pillars

The app is organized around six pillars. Each maps to a screen and a small set of stores/utils — keep that mapping intact when adding features.

1. **Verstehen (`LibraryScreen`)** — Topic library. Each `Topic` has both `introText` (weeks 1–2, gentle/metaphorical) and `deepText` (week 3+, clinical terms). `isSensitive: true` forces a `TherapyHint`.
2. **Üben (`PracticeScreen` + `ExercisePlayer`)** — The core. `Exercise.type` is one of `'text' | 'timer' | 'animation' | 'audio' | 'combined'`. `ExercisePlayer` drives instructions → audio (Expo AV) → Lottie → timer ring → XP animation → reflection prompt → character update.
3. **Wiederholen** — `utils/spacedRepetition.ts` implements simplified SM-2: `quality 0–2` repeats today, `3–5` extends interval (3d → 1w → 2w → 1m). Mastered skills return as Level+1 with framing "you remember? that was hard. now you do level 2."
4. **Reflektieren (`ReflectScreen`)** — `Reflection` (per exercise) + `FutureMessage` (delivered via push at 1m/3m/1y). PDF export via `utils/reportGenerator.ts` with five fixed sections (big goal, helpful skills with method names, therapy topics, this week, notes).
5. **Struktur (`PlannerScreen`)** — Three-tier goals: `BigGoal` (no deadline) → `MonthlyGoal` (rescheduling allowed, never judged) → `GoalStep`. Plus `WeeklyTask` optionally linked to an exercise.
6. **Hilfe finden (`HelpScreen`)** — 5-question orientation (no diagnosis), info pages, crisis hotline `0800 111 0 111` always visible.

## Two cross-cutting systems

These are the most error-prone parts of the app — read carefully before touching anything user-facing.

### Acute Help flow (`HelpNowButton` → `AcuteHelpScreen`)

- **Globally visible** in the header of every screen (except during a running exercise).
- Label: **"Jetzt helfen"** (never "Notfall" / "Krise"). Color: calm teal `#0D9488`, **never red**.
- One question (*"Was brauchst du gerade?"*) → 4–6 large tap targets from `data/acuteStates.ts` → exercise (2–5 min) → optional *"Hat das geholfen?"*.
- `utils/acuteMatcher.ts` picks the exercise: prefer ones the user previously rated helpful, rotate when tied, fall back to shortest.
- **No gamification in this flow** — no XP animation, no level-up, no enemies, ever. Even in RPG mode. The only success is that the exercise was done.
- Method transparency at the end (`"Das war eine DBT-Technik. Mehr dazu unter [Skill]."`).
- The crisis hotline lives on `HelpScreen`, not here. The acute button leads to *exercises*, never hotlines.

### RPG mode vs. Klartext mode (`settingsStore.mode: 'rpg' | 'plain'`)

Every user-facing string and visual must respect the current mode. Selected at end of onboarding (Screen 8b), switchable any time in settings, **no data loss on switch**.

- Use `data/labels.ts` + `useLabel(key)` hook for all dual-labeled copy. Don't hardcode RPG strings inline.
- Wrapper components silently degrade in plain mode: `<CharacterAvatar/>` → neutral profile, `<EnergyBar/>` → plain progress, `<XPAnimation/>` → silent checkmark.
- `enemies.ts` content **must never render in plain mode**.
- Plain-mode tone: direct, factual, respectful — no metaphors. RPG-mode tone: buddy-like, playful.
- Everything else (exercises, methods, topics, planner, acute help, therapy hints, community, privacy) is identical across modes.

## Data conventions

- IDs are stable strings (e.g. `'5-4-3-2-1-grounding'`, `'thought-observation'`). Used as foreign keys across `skills`, `exercises`, `topics`, `enemies`, `acuteStates`. Don't renumber; add new ones.
- Methods are named transparently in user copy: **CBT, ACT, DBT, MBSR, MSC, Trauerarbeit**. Never hide the method.
- Exercises tagged for acute use: `isAcute: true` + `acuteStates: string[]`.
- All persistence is local (AsyncStorage / SQLite). Never add network calls without an explicit opt-in toggle.

## Design system

```
primary    #0D9488  // teal — calm, trust
dark       #0F1F2E
background #F1F5F9
amber      #D97706  purple #7C3AED  rose #E11D48  blue #1D4ED8
helpNow    #0D9488  // acute button — calm teal, NOT red
```

Typography: **Nunito**, sizes `12 / 14 / 16 / 18 / 22 / 28 / 36`.

## Non-negotiable principles (apply to every PR)

These come from the spec's "Kern-Prinzipien" and override generic best practices when they conflict:

1. **No coercion** — no streak shaming, red numbers, or punitive UI. Going down a level is a strength.
2. **Small steps** — exercises capped at ~5 minutes.
3. **Help is one tap away** — `HelpNowButton` is never hidden, nested, or behind a confirmation.
4. **Privacy first** — nothing leaves the device without explicit user consent. No tracking, no ads, no data sale.
5. **No diagnosis** — the app helps users *understand*, not label themselves. Avoid clinical assertions in copy.
6. **Therapy hints are inviting, not scary** — `"Du bist mutig"`, never `"Du brauchst Hilfe"`. `TherapyHint` triggers via `shouldShowTherapyHint(context)`: onboarding (once), `topic.isSensitive`, long pause (`streak === 0 && daysSinceInstall > 14`), or user request.
7. **Error copy is gentle** — `"Kein Problem, weiter geht's"`, never `"Falsch"`.
8. **First success in minute 3** — onboarding ends in a real exercise, not a tour.

## MVP scope (build order)

Phase 1 (MVP): Onboarding screens 1–9 incl. mode picker 8b · HomeScreen with daily exercise · 10 exercises (text + timer only) · 4 skills (Gedanken beobachten / Innere Ruhe / Grounding / Innerer Anker) · acute help with 6 states + 8–10 exercises · **full Klartext mode** · simple character (level + XP, no equipment sprites) · weekly plan · goal system · static `HelpScreen` + `TherapyHint`.

Phase 2: audio + Lottie · SM-2 · reflection journal + future message · all 14 topics · equipment sprites · PDF export · audio-guided acute exercises.

Phase 3: anonymous community · 5-question therapy orientation · opt-in push · all 7 skills L1–5 · learned acute matching.

## Git workflow

- Active development branch: `claude/add-claude-documentation-9NPNV` (per task instructions).
- `main` is the integration branch. Push feature work to the assigned branch and only open PRs when explicitly asked.
