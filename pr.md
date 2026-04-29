# Pull Request

## Description

Fixes the `InputBlock.flow()` method in `js/blocks/SensorsBlocks.js`. 

This PR resolves three significant problems in the Input block:
1. **Security (XSS prevention):** Replaced `.innerHTML = '<input...>'` string concatenation with programmatic DOM creation using `document.createElement("input")`. This adheres to the security rules specified in `AGENTS.md` regarding unsafe innerHTML usage.
2. **Memory/Performance Leak:** Fixed a critical event listener leak where a new `keypress` listener was attached every time the `InputBlock` executed but was never removed. Added `inputElem.removeEventListener()` upon successful input completion so the listener cleans itself up.
3. **Modernization:** Updated the deprecated `event.keyCode === 13` check to standard `event.key === "Enter"`.

## Related Issue
<!-- e.g., Fixes #1234 -->
Fixes #[ISSUE_NUMBER_HERE]

## Type of Change
- [x] Bug fix (non-breaking change which fixes an issue)
- [x] Refactor (code improvement/cleanup)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)

## How Has This Been Tested?
- [x] Ran `npm test` - All Jest tests passing successfully.
- [x] Verified `SensorsBlocks.test.js` still passes the `InputBlock` checks correctly.
- [x] Manual testing: Added an `input` block to the Music Blocks workspace, triggered it multiple times, and verified in browser DevTools that duplicate event listeners are no longer accumulating.

## Checklist:
- [x] My code follows the code style of this project (`npm run lint`, `npx prettier --check .`).
- [x] I have performed a self-review of my own code.
- [x] I have commented my code, particularly in hard-to-understand areas.
- [x] My changes generate no new warnings.
- [x] I have added tests that prove my fix is effective or that my feature works.
- [x] New and existing unit tests pass locally with my changes.
