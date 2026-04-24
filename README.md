# Mental Buddy

> Eine kostenlose, spielerische Lernplattform für mentale Gesundheit – ein täglicher Begleiter, der wissenschaftlich fundierte Methoden durch Wiederholung und Spielmechanik in den Alltag bringt.

Mental Buddy ist **kein Therapieersatz**. Die App richtet sich an Menschen, die sich zu spät oder gar nicht Hilfe holen – besonders Männer – und senkt die Hürde, sich überhaupt mit der eigenen mentalen Gesundheit zu beschäftigen.

- **Plattform:** iOS & Android (React Native / Expo)
- **Kosten:** 100 % kostenlos – keine Werbung, kein Abo, kein Tracking
- **Sprache:** Deutsch (mehrsprachig vorgesehen)
- **Wissenschaftliche Basis:** CBT, ACT, DBT, MBSR, MSC – transparent benannt, verständlich erklärt

## Status

Frühe Konzept-Phase. Die vollständige Spezifikation liegt in [`CLAUDE 2.pdf`](./CLAUDE%202.pdf), die Architektur- und Konventions-Übersicht für KI-gestützte Entwicklung in [`CLAUDE.md`](./CLAUDE.md). Das Expo-Projekt wird gerade initialisiert.

## Features

### Die sechs Säulen

| Säule           | Screen            | Inhalt                                                                 |
| --------------- | ----------------- | ---------------------------------------------------------------------- |
| Verstehen       | `LibraryScreen`   | Themen-Bibliothek mit sanfter Sprache zu Beginn, klarer Sprache später |
| Üben            | `PracticeScreen`  | Geführte Übungen mit Timer, Audio und Animation                        |
| Wiederholen     | (intern, SM-2)    | Spaced Repetition – mastered Skills kommen als Level+1 zurück          |
| Reflektieren    | `ReflectScreen`   | Tagebuch, Nachricht ans zukünftige Ich, PDF-Export                     |
| Struktur        | `PlannerScreen`   | Drei-Ebenen-Ziele + Wochenplan, ohne Druck                             |
| Hilfe finden    | `HelpScreen`      | Orientierung welche Art von Unterstützung passt, Krisentelefon         |

### Akut-Hilfe – immer einen Tap entfernt

Ein globaler **„Jetzt helfen"**-Button ist im Header jedes Screens sichtbar. Eine einzige Frage – *„Was brauchst du gerade?"* – führt direkt zu einer 2–5-minütigen Übung. Kein Menü, kein Scrollen, keine Diagnose, keine Gamification.

### Klartext-Modus

Das RPG-Framing (Charakter, Klassen, Übungs-Belohnungen) hilft vielen, kann aber befremdlich wirken – besonders wenn es schlecht geht. Der Klartext-Modus bietet **dieselben Inhalte ohne Spiel-Metaphern**: direkte, sachliche Sprache. Jederzeit umschaltbar, ohne Datenverlust.

## Technischer Stack

| Bereich         | Technologie                              |
| --------------- | ---------------------------------------- |
| Framework       | React Native (Expo)                      |
| Navigation      | React Navigation (Bottom Tabs + Stack)   |
| State           | Zustand                                  |
| Lokaler Speicher| AsyncStorage + `expo-sqlite`             |
| Audio           | Expo AV                                  |
| Animation       | React Native Reanimated + Lottie         |
| Charts          | Victory Native                           |
| Backend         | Node.js + Express *(optional, Community)* |
| Sprache         | TypeScript                               |

## Projektstruktur

```
app/
├── screens/      # Onboarding, Home, Library, Practice, Character, Planner,
│                 # Reflect, Help, AcuteHelp, Community
├── components/   # ExercisePlayer, HelpNowButton, ModeToggle, TherapyHint, ...
├── data/         # skills, topics, enemies, equipment, acuteStates, labels
├── store/        # Zustand-Stores: user, progress, planner, reflection, settings
└── utils/        # spacedRepetition (SM-2), reportGenerator, acuteMatcher, ...
assets/
├── animations/   # Lottie JSON
├── audio/        # Geführte Übungen (MP3)
└── character/    # Sprites & Equipment
```

## Erste Schritte

> Hinweis: Das Expo-Projekt wird derzeit initialisiert. Sobald `package.json` existiert, gelten die folgenden Befehle.

Voraussetzungen:

- Node.js ≥ 20
- npm oder pnpm
- Expo Go auf deinem Test-Gerät *(oder iOS-Simulator / Android-Emulator)*

```bash
git clone https://github.com/Blackwarrior309/Site.git mental-buddy
cd mental-buddy
npm install
npx expo start
```

Im Expo-Dev-Tools-Browser dann den QR-Code mit Expo Go scannen oder einen Simulator starten.

## Design-Prinzipien

Diese Prinzipien gehen vor generische Best Practices, wenn etwas in Konflikt steht:

1. **Kein Zwang, niemals.** Keine Streak-Strafen, keine roten Zahlen, kein Shame.
2. **Kleine Schritte.** Jede Übung max. 5 Minuten. Lieber 3 Min täglich als 30 Min nie.
3. **Hilfe ist immer einen Tap entfernt.** Der Akut-Button ist nie versteckt.
4. **Datenschutz first.** Nichts verlässt das Gerät ohne explizite Zustimmung.
5. **Keine Diagnose.** Die App hilft zu verstehen, nicht zu etikettieren.
6. **Therapie betonen ohne zu erschrecken.** *„Du bist mutig"*, nicht *„Du brauchst Hilfe"*.
7. **Methodentransparenz.** Jeder Skill zeigt, welche Methode (CBT, DBT, MBSR …) dahintersteckt.
8. **Zurückgehen ist erlaubt.** Ein niedrigeres Level zu wählen ist eine Stärke, kein Versagen.
9. **Der Nutzer wählt seine Sprache.** RPG oder Klartext – beides ist richtig.

## Datenschutz & Ethik

- Alle Daten **lokal** auf dem Gerät (AsyncStorage / SQLite) – kein Cloud-Zwang
- Kein Account nötig (optionaler Account ausschließlich für Geräte-Backup)
- Kein Tracking, keine Werbung, kein Verkauf von Daten
- Berichte werden nur auf expliziten Wunsch erzeugt – niemals automatisch geteilt

## Roadmap

- **Phase 1 (MVP):** Onboarding · 4 Skills · 10 Übungen (Text + Timer) · Akut-Hilfe · Klartext-Modus · einfacher Wochenplan · Zielsystem · statische Hilfe-Seiten
- **Phase 2:** Audio-Übungen (Expo AV) · Lottie-Animationen · Spaced Repetition · Reflexions-Journal & Nachricht ans zukünftige Ich · alle 14 Themen · Charakter-Sprites · PDF-Export
- **Phase 3:** Anonyme Community (stille Solidarität) · Therapie-Orientierungshelfer · Push-Notifications · alle 7 Skills L1–5 · lernendes Akut-Matching

## Krisenhilfe

Wenn es dir gerade akut schlecht geht und du jemanden zum Reden brauchst:

**Telefonseelsorge:** [`0800 111 0 111`](tel:08001110111) – kostenlos, anonym, 24/7

## Mitwirken

Issues und Pull Requests sind willkommen. Vor größeren Änderungen bitte erst ein Issue öffnen, damit wir die Richtung abstimmen können. Lies vorher [`CLAUDE.md`](./CLAUDE.md) – dort stehen die architektonischen und sprachlichen Konventionen.

## Lizenz

Noch nicht festgelegt.
