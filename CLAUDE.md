# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Goalkeeper Tactics Quiz — a single-page browser app that quizzes youth goalkeepers (U14 competitive level) on match scenarios across 7 categories. No build system, no dependencies, no server required.

## Architecture

Everything lives in a single `index.html` file (~1670 lines):

- **Lines 1–548**: HTML structure + embedded CSS (dark theme, responsive layout)
- **Lines 549–1440**: `questions` array — 48 question objects, each with `category`, `scenario`, `question`, `options` (4 choices), `correct` (0-indexed), `explanation`, and `tip`
- **Lines 1441–1670**: App logic — state management, quiz flow, rendering, scoring

Key components of the JS app logic:
- `state` object holds all runtime state (selected categories, queue, scores, streaks)
- `init()` builds category filter cards and sets up the start screen
- `buildQueue()` filters and shuffles questions based on selected categories and mode
- `renderQuestion()` / `selectAnswer()` / `showExplanation()` handle the quiz flow
- `showEnd()` computes final scores with per-category breakdown and letter grades
- `show(id)` toggles visibility between screens (`startScreen`, `quizScreen`, `endScreen`)

Quiz modes: All Questions, Quick 10, Match 20, Full 30.

## Deployment

Hosted on GitHub Pages at https://tfin22.github.io/goalie/. Pushes to `main` auto-deploy via `.github/workflows/pages.yml`.

## Development

Open `index.html` directly in a browser — no build step, no dev server needed. To test changes, refresh the page.

There are no tests, linters, or CI pipelines.

## iOS Compatibility

iOS does not execute JavaScript in locally opened HTML files (WhatsApp file preview, Files app Quick Look, etc.). The app must be accessed via a URL (e.g. the GitHub Pages link) to work on iPhones. All interactive elements use `touch-action: manipulation` and `-webkit-tap-highlight-color` for responsive taps on iOS. Category card child elements have `pointer-events: none` so taps register on the card itself. Dynamically created elements use `addEventListener` instead of `.onclick` for WKWebView reliability.

## Content Guidelines

All scenarios must reflect youth football realities (U14 competitive level), not adult/professional situations. Each question needs a coaching explanation for the correct answer and a practical tip.
