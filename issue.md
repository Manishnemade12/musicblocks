### Description

The `InputBlock` in `js/blocks/SensorsBlocks.js` has three critical flaws within its `flow()` method, leading to XSS vulnerabilities and memory leaks.
Link: https://github.com/sugarlabs/musicblocks/blob/master/js/blocks/SensorsBlocks.js

The issues are:
1. **Security Vulnerability (XSS):** It uses `innerHTML` with string concatenation to dynamically insert an `<input>` field into the DOM. This violates standard security practices (preferring `createElement` or `textContent`) and goes against the repo's guidelines in `AGENTS.md`.
2. **Memory Leak (Event Listener Leak):** A `keypress` event listener is attached to `docById("textLabel")` to detect when the user presses Enter. However, this event listener is *never removed*. Each time the Input block runs, a new listener is attached, causing them to stack (e.g., 10 executions = 10 duplicate listeners firing simultaneously on every keypress).
3. **Deprecated API:** It uses `event.keyCode === 13`, which is deprecated in modern browsers.

### Expected Behavior

- The input element should be constructed programmatically using `document.createElement("input")` to avoid injecting raw HTML via `.innerHTML`.
- The `keypress` event listener must be removed immediately after it handles the user's `Enter` key input, preventing event listener accumulation/memory leaks.
- The event logic should use the modern standard `event.key === "Enter"`.

### Screenshots

*(Not applicable as this is a backend memory leak / source-code security issue, but the codebase shows the flawed implementation at lines 75-115 in `js/blocks/SensorsBlocks.js`)*

### How to Reproduce

1. Open Music Blocks.
2. Drag and drop the `input` block from the Sensors palette onto the workspace.
3. Attach a text block to it and run it. 
4. The input field appears. Open the browser's DevTools console.
5. In the console, inspect the event listeners attached to the `#textLabel` element.
6. Run the block 5 times. Notice that there are now 5 identical `keypress` event listeners attached to the element.
7. Observe `typeof docById("labelDiv").innerHTML` in the code, showing the HTML string injection.

### Console log Errors:

*(No visible console errors, the leak silently degrades performance and the XSS is structural)*

### Environment:

-   Operating System: MacOS, Linux, Windows
-   Browser (if applicable): All modern browsers (Chrome, Firefox, Safari, Edge)
-   Version of Software/Project: Master Branch

### Checklist

-   [x] I have read and followed the project's code of conduct.
-   [x] I have searched for similar issues before creating this one.
-   [x] I have provided all the necessary information to understand and reproduce the issue.
-   [x] I am willing to contribute to the resolution of this issue.

---

Thank you for contributing to our project! We appreciate your help in improving it.

📚 See [contributing instructions](https://github.com/sugarlabs/musicblocks/blob/master/README.md).

🙋🏾🙋🏼 Questions: [Community Matrix Server](https://matrix.to/#/#sugar:matrix.org).
