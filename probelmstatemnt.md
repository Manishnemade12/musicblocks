Ticket Contents
Description
Music Blocks currently depends on three abandoned CreateJS libraries — easeljs.min.js, tweenjs.min.js, and preloadjs.min.js — for all of its canvas-based graphics rendering. CreateJS was abandoned by its maintainers in 2017 and receives no security patches, bug fixes, or compatibility updates. This poses a long-term maintenance and security risk for the project.

The CreateJS suite is deeply integrated into the rendering pipeline: it manages the main Stage, all display object Containers, Bitmap images, Shape vector graphics, Text nodes, and Tween animations. It is used across at least 15 source files including js/block.js, js/activity.js, js/turtle.js, js/turtles.js, js/trash.js, js/boundary.js, js/pastebox.js, js/blocks.js, js/protoblocks.js, js/artwork.js, js/planetInterface.js, and activity/SugarAnimation.js.

The goal of this project is to research viable alternatives, plan a migration strategy, and execute the replacement.

Goals & Mid-Point Milestone
Goals

Audit all usages of createjs.* APIs across the codebase and produce a comprehensive compatibility matrix

Research and evaluate alternative libraries (e.g., Konva.js, PixiJS, Fabric.js, or native Canvas 2D API)

Produce a written migration plan with a phased approach

Goals Achieved By Mid-point Milestone: Complete the audit, select a replacement library, and migrate at least one major subsystem (e.g., block rendering in js/block.js or turtle rendering in js/turtle.js and js/turtles.js)

Complete migration of all remaining subsystems

Remove lib/easeljs.min.js, lib/tweenjs.min.js, and lib/preloadjs.min.js from the codebase

Ensure all existing tests pass and add new tests for migrated rendering code
Setup/Installation
Follow the standard Music Blocks development setup:

Clone the repository: https://github.com/sugarlabs/musicblocks
Install dependencies: npm install
Run locally by serving index.html (e.g., npx http-server)
Run tests: npm test

Expected Outcome
The final product should be a fully functional Music Blocks application with CreateJS completely removed and replaced by a maintained, actively developed library (or native browser APIs). All visual features — block rendering, turtle graphics, animations, drag-and-drop interactions, the trashcan, boundary display, and the Sugar animation — should work identically to the current implementation. The replacement should not regress any existing functionality.

Acceptance Criteria
All createjs.* references are removed from the codebase
lib/easeljs.min.js, lib/tweenjs.min.js, and lib/preloadjs.min.js are deleted
Block rendering (SVG bitmaps, containers, hit areas) works correctly
Turtle sprite rendering, rotation, and image display work correctly
Tween-based animations (e.g., block highlighting) work correctly
The createjs.Stage / createjs.Ticker render loop is replaced
All existing Jest tests pass
No visual regressions in the UI

Implementation Details
Music Blocks uses three CreateJS libraries: easeljs.min.js, tweenjs.min.js, and preloadjs.min.js, all stored in the lib/ folder and loaded via index.html.

The main CreateJS APIs in use are:

createjs.Stage is used in index.html and js/activity.js to create the main canvas rendering stage that everything is drawn onto.

createjs.Container is used in js/block.js, js/turtles.js, js/turtle.js, js/trash.js, js/boundary.js, and js/activity.js to group display objects together so they can be moved and transformed as a unit.

createjs.Bitmap is used in js/block.js, js/turtle.js, js/turtles.js, and js/activity.js to display images such as block SVGs and turtle sprites.

createjs.Shape is used in js/block.js, js/turtle.js, js/activity.js, and js/trash.js to draw vector graphics like hit areas and highlight outlines.

createjs.Text is used in js/block.js and js/activity.js to render text labels on blocks.

createjs.Tween is used in js/activity.js for animations such as block highlighting. It also drives the render loop via createjs.Tween.hasActiveTweens(), so this coupling must be carefully handled during migration.

activity/SugarAnimation.js is an auto-generated Adobe Animate export that uses CreateJS extensively under the alias cjs. This file will likely need to be regenerated with a different export target or rewritten manually.

One important note: the actual turtle drawing (lines, arcs, fills) already uses the native HTML5 Canvas 2D API directly in js/turtle-painter.js. Only the sprite and container layer on top of it uses CreateJS, which makes that part of the migration somewhat simpler.

Candidate replacement libraries to evaluate:

Konva.js — closest API match to EaselJS, uses a Stage/Layer/Group/Image/Shape model
PixiJS — high performance, WebGL-first with Canvas fallback
Native Canvas 2D API — zero dependencies, already partially in use in the project
Technologies involved: JavaScript, HTML5 Canvas, and whichever replacement library is selected after the evaluation phase.

Mockups/Wireframes
No response

Product Name
Music Blocks

Organisation Name
Sugar Labs

Domain
⁠Education

Tech Skills Needed
JavaScript

Mentor(s)
@sum2it @walterbender @omsuneri

Category
Frontend