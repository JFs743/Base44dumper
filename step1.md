# Manual Project Consolidation (Step 1)

This process allows you to manually aggregate the files of your project into a single JSON archive for local storage. This is a multi-step, user-driven process.

## Instructions
1. **Open your Project**: Navigate to your project on the platform and enter the Code View.
1. **Open Developer Tools**: Press `F12` (or `Cmd+Option+I` on Mac) and select the Console tab.
1. **Initialize the Session**: Copy and paste the Initialization Script below into the console. If prompted by your browser, type `allow pasting` to enable the command line.
1. **Manually Index Files**: One by one, click on each file in your project sidebar.
1. As you view each file, the script will temporarily hold the content in your browser's memory.
1. Check the console for a `[Captured]` confirmation message for each file.
1. **Finalize & Export**: Once you have manually cycled through all necessary files, copy and paste the End Script into the console.
1. **Save Archive**: A file named `dump.json` will be generated. Save this to your computer.
1. Open step2.html, drag your `dump.json` file onto the upload button (or click it and select your dump file)
1. Either download as a zip or write to disk, it will ask for a folder to write to, it's slower than the zip, leave it be for a minute

Main script:
```js
(() => {
  const SELECTOR = '#\\:r6r\\: > div.flex.items-center.justify-between.px-3.py-1\\.5.bg-white.border-b.border-slate-100';

  // Global store (persistent during session)
  window.__fileDump = window.__fileDump || {};

  function getPath() {
    const container = document.querySelector(SELECTOR);
    if (!container) return null;

    return Array.from(container.querySelectorAll('span'))
      .map(el => el.textContent.trim())
      .filter(Boolean)
      .join('');
  }

  function getContent() {
    try {
      return monaco.editor.getModels()[0].getValue();
    } catch {
      return null;
    }
  }

  function capture() {
    const path = getPath();
    const content = getContent();

    if (!path || !content) return;

    if (window.__fileDump[path] !== content) {
      window.__fileDump[path] = content;
      console.log('[Captured]', path);
    }
  }

  // Run every 100ms
  window.__fileDumpInterval = setInterval(capture, 100);

  // Download function
  window.downloadDump = () => {
    const dataStr = JSON.stringify(window.__fileDump, null, 2);
    const blob = new Blob([dataStr], { type: 'application/json' });

    const a = document.createElement('a');
    a.href = URL.createObjectURL(blob);
    a.download = 'dump.json';
    a.click();

    URL.revokeObjectURL(a.href);
  };

  console.log ( '📦 Archiving session started. Please click through your files to index them.' ) ;
})();
```

End script:
```js
downloadDump()
clearInterval(window.__fileDumpInterval);
```
