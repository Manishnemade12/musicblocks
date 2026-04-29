# DMP 2026 Proposal: Music Blocks — Replace CreateJS with Konva.js

## About Me

**Manish Nemade** | B.Tech CSE, G H Raisoni College of Engineering, Jalgaon (2023-2027)

- 6 months as FullStack Engineering Intern at Melsta Studio — built interactive UI modules, optimized canvas rendering, worked with CI pipelines
- Active open source contributor at Layer5 — fixed event listener memory leaks, did cross-file refactoring (27 image migrations), built reusable hooks
- Skills: JavaScript (ES6+), HTML5 Canvas, React.js, Jest, ESLint, RequireJS

**Links:** [GitHub](https://github.com/Manishnemade12) | [LinkedIn](https://www.linkedin.com/in/manish-nemade-aaa69b28a/)

---

## Problem Understanding

Music Blocks depends on three CreateJS libraries (EaselJS, TweenJS, PreloadJS) abandoned since 2017 for all its canvas rendering. Every visual element — blocks, turtles, trashcan, animations — is a CreateJS display object. If a browser update breaks CreateJS, the entire application stops working for the students and educators who rely on it.

### Codebase Audit Results

| File | CreateJS Refs | APIs Used |
|------|:---:|-----------|
| activity.js | 38 | Stage, Ticker, Touch, Tween, Container, Bitmap, Shape, Text, DOMElement, ColorFilter |
| block.js | 31 | Bitmap, Container, Shape, Text, Tween |
| turtle.js | 21 | Bitmap, Shape, Text, Container |
| turtles.js | 12 | Container, Bitmap, Shape |
| artwork.js | 12 | Shape, Tween (block highlight animations) |
| trash.js | 7 | Container, Bitmap, Tween |
| pastebox.js | 5 | Container, Shape, Bitmap |
| protoblocks.js | 3 | Container, Text |
| boundary.js | 3 | Container, Bitmap |
| blocks.js | 3 | Container |
| utils.js | 3 | Readiness check (typeof createjs) |
| planetInterface.js | 2 | Bitmap |
| themebox.js | 2 | ColorFilter (dark mode) |
| munsell.js | 1 | Graphics.getRGB() color conversion |
| SugarAnimation.js | 2332 lines | Full CreateJS (auto-generated Adobe Animate) |
| **Total** | **~160 refs** | **11 distinct APIs** |

**Key insight:** turtle-painter.js already uses native Canvas 2D API for drawing. Only the sprite layer above it uses CreateJS.

---

## Proposed Solution

### Why Konva.js

Konva.js has the closest API mapping to EaselJS among all alternatives:

| CreateJS | Konva.js | Notes |
|----------|----------|-------|
| createjs.Stage | Konva.Stage | Konva uses div container, manages canvases internally |
| createjs.Container | Konva.Group | Same grouping concept |
| createjs.Bitmap | Konva.Image | Wraps HTMLImageElement |
| createjs.Shape | Konva.Shape | Uses sceneFunc callback |
| createjs.Text | Konva.Text | Config-driven |
| createjs.Tween | Konva.Tween | Built-in, no extra dependency |
| createjs.Ticker | Built-in | Konva auto-renders on changes |
| createjs.Touch | Built-in | No separate enable call needed |
| createjs.ColorFilter | Konva.Filters | node.cache() + Konva.Filters.Invert |

**Additional benefits:**
- Multi-layer architecture (each Layer = separate canvas) enables selective redraws
- Built-in drag-and-drop support simplifies current manual pressmove wiring
- Actively maintained (15K+ GitHub stars, regular releases)
- Same Canvas 2D rendering target — compatible with existing turtle-painter.js

### Architecture

```
Konva.Stage (div container)
├── Blocks Layer (separate canvas)
│     └── Konva.Group (per block)
│           ├── Konva.Image (SVG artwork)
│           ├── Konva.Text (labels)
│           ├── Konva.Shape (hit areas)
│           └── Konva.Tween (highlight)
│
├── Trash Layer (separate canvas)
│     └── Konva.Group (trashcan)
│           ├── Konva.Image (icon)
│           └── Konva.Tween (show/hide)
│
└── Turtle Layer (separate canvas)
      └── Konva.Group (per turtle)
            ├── Konva.Image (sprite)
            ├── Konva.Image (penstrokes)
            ├── Konva.Shape (hit detection)
            └── Konva.Text (canvas text)
```

### SugarAnimation.js Strategy

The 2332-line auto-generated Adobe Animate file cannot be migrated line by line. I will replace it with a lightweight CSS-based loading animation that reproduces the same visual sequence. This makes the loading screen maintainable by future contributors.

---

## Implementation Plan

### Phase 0 — Foundation (Week 1)
- Install Konva.js via npm, register in RequireJS loader config
- Replace Stage initialization in index.html and activity.js
- Replace Ticker and Touch setup
- Update render loop to use Konva layer.batchDraw()

### Phase 1 — Low Complexity Files (Weeks 2-3)
- boundary.js (3 refs)
- pastebox.js (5 refs)
- trash.js (7 refs)
- planetInterface.js (2 refs)
- protoblocks.js (3 refs)
- blocks.js (3 refs)
- utils.js (3 refs)
- themebox.js (2 refs)
- munsell.js (1 ref)

### Phase 2 — Turtle Rendering (Weeks 4-5) ← Mid-point Milestone
- turtles.js (12 refs) — sprite container setup, boundary bitmaps
- turtle.js (21 refs) — sprite display, hit areas, text rendering

### Phase 3 — Block Rendering (Weeks 6-8)
- artwork.js (12 refs) — highlight animations
- block.js (31 refs) — SVG artwork, text labels, hit areas, drag-and-drop, Tween

### Phase 4 — Activity Core + Animation (Weeks 9-10)
- activity.js remaining refs (38 refs) — DOMElement, ColorFilter, error arrows
- SugarAnimation.js — replace with CSS animation

### Phase 5 — Cleanup + Testing (Weeks 11-12)
- Delete lib/easeljs.min.js, lib/tweenjs.min.js, lib/preloadjs.min.js
- Remove all createjs globals from headers
- Run npm test, npm run lint, prettier check
- Manual browser testing of all features
- Performance benchmarks

---

## Mid-Point Milestone Deliverables

- Complete CreateJS audit documented
- Konva.js installed and Stage/Ticker/Touch replaced
- All Phase 1 files migrated (9 files)
- Turtle rendering fully working with Konva
- All existing Jest tests passing

---

## Risks and Mitigations

| Risk | Mitigation |
|------|-----------|
| SugarAnimation.js (2332-line auto-generated) cannot be migrated | Replace with CSS loading animation |
| Konva uses div container instead of single canvas | Update Stage setup early in Phase 0, test immediately |
| Render loop depends on createjs.Tween.hasActiveTweens() | Track Konva tweens with finish callbacks and a counter |
| Block drag-and-drop has 31 refs tied to hit areas and pressmove | Test interactions after every change, use Konva's built-in draggable |
| munsell.js uses createjs.Graphics.getRGB() | Replace with standalone RGB helper function |

---

## Acceptance Criteria Alignment

| Criteria | How My Solution Addresses It |
|----------|----------------------------|
| All createjs references removed | Konva.js replaces every API call across all 15 files |
| 3 lib files deleted | Deleted in Phase 5 after full migration |
| Block rendering works | Konva.Image, Konva.Text, Konva.Shape for SVGs, labels, hit areas |
| Turtle rendering works | Konva.Image and Konva.Group for sprites and containers |
| Tween animations work | Konva.Tween for block highlighting, trashcan show/hide |
| Stage/Ticker replaced | Konva.Stage with built-in auto-rendering |
| Jest tests pass | Verified after each phase |
| No visual regressions | Manual browser testing after each phase |

---

## Verification Plan

**Automated:**
- npm test — all Jest tests pass after each phase
- npm run lint — no lint regressions
- npx prettier --check . — formatting compliance
- grep -r "createjs" js/ activity/ index.html — must return 0 at end

**Manual Browser Testing (per phase):**
- Block rendering, text labels, drag-and-drop, connection snapping
- Turtle sprites, rotation, pen drawing
- Trashcan show/hide animation
- Dark mode bitmap inversion
- Touch interactions on mobile
- Loading animation replacement

**Performance:**
- Canvas FPS during block drag (target: ≥55 FPS)
- Memory footprint comparison before/after
