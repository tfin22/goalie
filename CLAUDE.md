# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Football Tactics Quiz — a single-page browser app that quizzes youth players (U14 competitive level) on match scenarios. Supports 5 positions: Goalkeeper, Defender, Midfielder, Winger, and Striker. No build system, no dependencies, no server required.

## Architecture

Everything lives in a single `index.html` file (~5400 lines):

- **CSS**: Embedded in `<style>` — dark theme (`#0a1628` background), responsive layout, mobile-friendly
- **`questions` array**: Goalkeeper-specific questions (~61 objects), each with `category`, `scenario`, `question`, `options` (4 choices), `correct` (0-indexed), `explanation`, and `tip`
- **`positionQuestions` array**: Outfield questions (~218 objects), each adding a `scope` field (`'defender'`, `'midfielder'`, `'winger'`, `'striker'`, `'outfield'`, or `'team'`) and `difficulty` (1–3)
- **App logic**: State management, quiz flow, rendering, scoring — at the bottom of the file

### Screens

- `positionScreen` — player picks their position (shown on first load)
- `startScreen` — category filter grid + mode selection + best scores leaderboard
- `quizScreen` — question card with scenario, options, explanation panel, score strip
- `endScreen` — results with grade, per-category breakdown, redo wrong answers

### Question banks

| Position | Scope filter | Questions |
|---|---|---|
| Goalkeeper | `questions` array only | ~61 |
| Defender | `scope === 'defender'` + `'outfield'` + `'team'` | ~58 |
| Midfielder | `scope === 'midfielder'` + `'outfield'` + `'team'` | ~58 |
| Winger | `scope === 'winger'` + `'outfield'` + `'team'` | ~68 |
| Striker | `scope === 'striker'` + `'outfield'` + `'team'` | ~58 |

### Key JS functions

- `state` object holds all runtime state (position, selected categories, queue, scores, streaks, lives)
- `init()` builds the position selection cards on load
- `selectPosition(p)` sets the position, builds the category grid, goes to start screen
- `buildCategoryGrid(pool)` populates category toggle cards and sets `state.selectedCategories`
- `buildQueue()` filters and shuffles questions based on selected categories and mode
- `renderQuestion()` / `selectAnswer()` / `showExplanation()` handle the quiz flow
- `showEnd()` computes final scores with per-category breakdown and grade
- `show(id)` toggles visibility between screens

### Quiz modes

- **All Questions** — full pool of selected categories, no lives/game-over
- **Quick 10** — random 10 questions, 3-lives game-over
- **Match 20** — random 20 questions, 3-lives game-over
- **Full 30** — random 30 questions, 3-lives game-over

### Gameplay mechanics

- **3 lives** — lose a life per wrong answer in Quick/Match/Full modes; game ends when all lives are lost. All Questions mode has no lives limit (shows ∞).
- **Streak bonuses** — bonus points at streaks of 3, 6, 10, 15, 20 correct in a row (shown as a toast)
- **Skip** — moves current question to the end of the queue
- **Redo wrong answers** — replays only incorrectly answered questions after finishing
- **Best scores leaderboard** — top 5 scores saved in `localStorage`, resettable with confirmation

## Deployment

Hosted on GitHub Pages at https://tfin22.github.io/goalie/. Pushes to `main` auto-deploy via `.github/workflows/pages.yml`.

## Development

Open `index.html` directly in a browser — no build step, no dev server needed. To test changes, refresh the page.

There are no tests, linters, or CI pipelines.

## iOS Compatibility

iOS does not execute JavaScript in locally opened HTML files (WhatsApp file preview, Files app Quick Look, etc.). The app must be accessed via a URL (e.g. the GitHub Pages link) to work on iPhones. All interactive elements use `touch-action: manipulation` and `-webkit-tap-highlight-color` for responsive taps on iOS. Category card child elements have `pointer-events: none` so taps register on the card itself. Dynamically created elements use `addEventListener` instead of `.onclick` for WKWebView reliability.

## Content Guidelines

All scenarios must reflect youth football realities (U14 competitive level), not adult/professional situations. Each question needs a coaching explanation for the correct answer and a practical tip.
