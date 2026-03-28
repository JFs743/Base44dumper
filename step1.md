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
1. Open step2.html, drag your `dump.json` file onto the upload button (or click it and select your dump file), also see 
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

Here's a version of step2.html that lives entirely in the url bar (zip only, due to browser limitation), simply copy this and paste it in the url bar: 
```
data:text/html;base64,PCFET0NUWVBFIGh0bWw+CjxodG1sPjxoZWFkPjx0aXRsZT5KU09OIOKGkiBGaWxlIFRyZWU8L3RpdGxlPjwvaGVhZD48Ym9keT48aDI+SW1wb3J0IEpTT04gZHVtcDwvaDI+PGlucHV0IHR5cGU9ImZpbGUiIGlkPSJmaWxlSW5wdXQiIC8+PGJ1dHRvbiBvbmNsaWNrPSJidWlsZFppcCgpIj5Eb3dubG9hZCBaSVA8L2J1dHRvbj48c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG4uanNkZWxpdnIubmV0L25wbS9qc3ppcEAzLjEwLjEvZGlzdC9qc3ppcC5taW4uanMiPjwvc2NyaXB0PjxzY3JpcHQ+CmxldCBkYXRhPXt9O2RvY3VtZW50LmdldEVsZW1lbnRCeUlkKCdmaWxlSW5wdXQnKS5hZGRFdmVudExpc3RlbmVyKCdjaGFuZ2UnLGFzeW5jKGUpPT57Y29uc3QgZmlsZT1lLnRhcmdldC5maWxlc1swXTtjb25zdCB0ZXh0PWF3YWl0IGZpbGUudGV4dCgpO2RhdGE9SlNPTi5wYXJzZSh0ZXh0KTtjb25zb2xlLmxvZygnTG9hZGVkJyxPYmplY3Qua2V5cyhkYXRhKS5sZW5ndGgsJ2ZpbGVzJyl9KTthc3luYyBmdW5jdGlvbiBidWlsZFppcCgpewpjb25zdCB6aXA9bmV3IEpTWmlwKCk7Zm9yKGNvbnN0W3BhdGgsY29udGVudF1vZiBPYmplY3QuZW50cmllcyhkYXRhKSl7emlwLmZpbGUocGF0aCxjb250ZW50KX0KY29uc3QgYmxvYj1hd2FpdCB6aXAuZ2VuZXJhdGVBc3luYyh7dHlwZTonYmxvYid9KTtjb25zdCBhPWRvY3VtZW50LmNyZWF0ZUVsZW1lbnQoJ2EnKTthLmhyZWY9VVJMLmNyZWF0ZU9iamVjdFVSTChibG9iKTthLmRvd25sb2FkPSdmaWxlcy56aXAnO2EuY2xpY2soKX0KPC9zY3JpcHQ+PC9ib2R5PjwvaHRtbD4=
```
