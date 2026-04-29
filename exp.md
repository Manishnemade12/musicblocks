internship 

FullStack Engineering Intern Oct 2025 – March 2026
Melsta Studio Pvt. Ltd Remote
• Developed multiple Admin Panel modules using a scalable component-based architecture with reusable,
well-structured React components.
• Improved performance through memoization (useMemo, useCallback) and code splitting, reducing render
overhead by 20%.
• Implemented lazy loading and client-side caching strategies, cutting redundant API calls and lowering backend
load by 30%.
• Built mobile-first, responsive UI layouts ensuring consistent cross-browser experience across all modules.
• Integrated REST APIs using async/await and axios with proper loading, error, and success state handling.
• Engaged with CI workflows, resolving merge conflicts and writing frontend test cases to maintain build stability.
• Technologies: javascript, React.js, CircleCI






open source contributions

in layer5 organizations 

 perf(images): Replace Kanvas <img> Tags with StaticImage
Addresses image rendering performance in the Kanvas section by replacing 27 raw HTML <img> tags with Gatsby's StaticImage component across kanvas-catalog.js, index.js, and kanvas-modes.js. Added StaticImage imports where needed, removed now-unneeded static image imports, and updated styled-component selectors from img to .gatsby-image-wrapper where layout depended on direct <img> targeting. Enables the full Gatsby image pipeline for these assets, improving optimized delivery and rendering consistency.

🔗 View PR

2. fix(navigation): Clean Up Leaked Global Scroll Listeners
Fixes memory leaks caused by uncleaned global scroll listeners in SPA navigation flows. Two components attached window.addEventListener("scroll", ...) using anonymous callbacks inside useEffect without corresponding cleanup, causing listeners to accumulate across route transitions. Added named handleScroll and handleHeaderScroll functions with cleanup returns in Navigation/index.js and Brand/index.js respectively, enabling window.removeEventListener(...) on unmount and preventing stacked listeners, memory leaks, and scroll-jank over long sessions.

🔗 View PR

3. fix(smi-table): Optimize Row Rendering with Memoized Rows
Resolves the SMI table rendering performance issue by isolating row rendering and collapse state per row. Refactored table row rendering from inline mapping into a separate TableRow component in src/components/SMI-Table/index.js, wrapped it with React.memo(), and moved collapse state from a parent-level array to per-row local useState. Clicking one row now updates only that row's state instead of triggering a full table body re-render, improving interaction responsiveness for larger datasets with no change to existing visual behavior.

🔗 View PR

4. fix(a11y): Improve Alt Text in Features and Kanvas Visuals
Improves accessibility (WCAG-focused image semantics) by fixing missing, generic, and empty alt attributes across high-impact Components and Kanvas sections. Updated instructional images to use descriptive alt text, marked decorative images with alt="" and aria-hidden="true", and replaced generic "image" alt values with context-aware descriptions. Changes span seven files across the Features, Academy, and Kanvas sections, improving screen-reader accessibility and reducing ambiguous image announcements.

🔗 View PR


Centralized webhook submission into a useWebhookSubmit hook and updated forms to await submissions in Formik onSubmit, preventing false success states. Added the hook for handling axios requests and errors, refactored CommonForm, EventForm, and ContactForm to use it, and included basic failure alerts while keeping success scroll behavior.