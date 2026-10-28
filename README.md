# Sam-code-runner
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Code Runner</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500&family=Sora:wght@500;600;700&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #12141c;
    --panel: #181b26;
    --panel-2: #1f2331;
    --line: #2b3042;
    --text: #e6e9f5;
    --muted: #8a91ab;
    --accent: #8b9bff;
    --run: #ffb84d;
    --run-hover: #ffc775;
    --ok: #6fe0a8;
    --err: #ff7a8a;
    --warn: #ffd166;
    --radius: 10px;
    --mono: 'JetBrains Mono', ui-monospace, 'Cascadia Code', Consolas, monospace;
    --sans: 'Sora', system-ui, -apple-system, 'Segoe UI', sans-serif;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  html, body { height: 100%; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: var(--sans);
    display: flex;
    flex-direction: column;
    height: 100vh;
    height: 100dvh;
    overflow: hidden;
  }

  /* ---------- Top bar ---------- */
  header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 12px;
    padding: 10px 16px;
    background: var(--panel);
    border-bottom: 1px solid var(--line);
    flex-wrap: wrap;
  }

  .brand {
    display: flex;
    align-items: center;
    gap: 10px;
    font-weight: 700;
    font-size: 17px;
    letter-spacing: 0.2px;
  }

  .brand-mark {
    width: 28px; height: 28px;
    border-radius: 8px;
    background: var(--accent);
    color: #12141c;
    display: grid; place-items: center;
    font-family: var(--mono);
    font-weight: 500;
    font-size: 13px;
  }

  .actions { display: flex; align-items: center; gap: 8px; flex-wrap: wrap; }

  .toggle {
    display: flex; align-items: center; gap: 8px;
    font-size: 13px; color: var(--muted);
    cursor: pointer; user-select: none;
    padding: 0 6px;
  }
  .toggle input { accent-color: var(--accent); width: 15px; height: 15px; cursor: pointer; }

  button {
    font-family: var(--sans);
    font-size: 13px;
    font-weight: 600;
    border-radius: 8px;
    border: 1px solid var(--line);
    background: var(--panel-2);
    color: var(--text);
    padding: 8px 14px;
    cursor: pointer;
    transition: background .15s, border-color .15s, transform .05s;
  }
  button:hover { background: #272c3f; border-color: #3a4160; }
  button:active { transform: translateY(1px); }
  button:focus-visible, .tab:focus-visible, textarea:focus-visible {
    outline: 2px solid var(--accent);
    outline-offset: 2px;
  }

  .run-btn {
    background: var(--run);
    border-color: var(--run);
    color: #2a1d00;
    padding: 8px 18px;
  }
  .run-btn:hover { background: var(--run-hover); border-color: var(--run-hover); }

  kbd {
    font-family: var(--mono);
    font-size: 11px;
    background: rgba(0,0,0,.18);
    border-radius: 4px;
    padding: 1px 5px;
    margin-left: 6px;
  }

  /* ---------- Workspace ---------- */
  main {
    flex: 1;
    min-height: 0;
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 10px;
    padding: 10px;
  }

  .pane {
    display: flex;
    flex-direction: column;
    min-height: 0;
    min-width: 0;
    background: var(--panel);
    border: 1px solid var(--line);
    border-radius: var(--radius);
    overflow: hidden;
  }

  .pane-bar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    background: var(--panel-2);
    border-bottom: 1px solid var(--line);
    min-height: 40px;
    padding: 0 6px 0 0;
  }

  .tabs { display: flex; height: 100%; }

  .tab {
    font-family: var(--mono);
    font-size: 13px;
    font-weight: 500;
    color: var(--muted);
    background: none;
    border: none;
    border-bottom: 2px solid transparent;
    border-radius: 0;
    padding: 11px 16px;
  }
  .tab:hover { color: var(--text); background: none; }
  .tab[aria-selected="true"] { color: var(--text); border-bottom-color: var(--accent); }

  .pane-title { font-size: 12px; color: var(--muted); padding: 0 14px; font-weight: 600; }

  /* ---------- Editors ---------- */
  .editors { flex: 1; min-height: 0; position: relative; }

  .editor {
    position: absolute; inset: 0;
    display: none;
    background: #0e1017;
  }
  .editor.active { display: flex; }

  .gutter {
    width: 46px;
    flex-shrink: 0;
    overflow: hidden;
    padding: 14px 8px 14px 0;
    text-align: right;
    font-family: var(--mono);
    font-size: 13.5px;
    line-height: 1.6;
    color: #4d5470;
    background: #0b0d13;
    border-right: 1px solid var(--line);
    user-select: none;
    white-space: pre;
  }

  textarea {
    flex: 1;
    min-width: 0;
    background: transparent;
    color: var(--text);
    border: none;
    padding: 14px 16px;
    font-family: var(--mono);
    font-size: 13.5px;
    line-height: 1.6;
    tab-size: 2;
    resize: none;
    outline: none;
    white-space: pre;
    overflow: auto;
  }

  /* ---------- Preview + console ---------- */
  .preview-wrap { flex: 1; min-height: 0; display: flex; flex-direction: column; }

  iframe { flex: 1; min-height: 0; width: 100%; border: none; background: #fff; }

  .console {
    height: 150px;
    display: flex;
    flex-direction: column;
    border-top: 1px solid var(--line);
    background: #0e1017;
  }
  .console.collapsed { height: 34px; }
  .console.collapsed .console-body { display: none; }

  .console-bar {
    display: flex; align-items: center; justify-content: space-between;
    padding: 0 8px 0 14px;
    min-height: 34px;
    font-size: 12px; font-weight: 600; color: var(--muted);
    background: var(--panel-2);
  }
  .console-bar button { padding: 4px 10px; font-size: 12px; }
  .console-bar .btns { display: flex; gap: 6px; }

  .badge {
    display: inline-block;
    min-width: 18px;
    text-align: center;
    margin-left: 8px;
    padding: 1px 6px;
    border-radius: 99px;
    font-family: var(--mono);
    font-size: 11px;
    background: var(--line);
    color: var(--text);
  }
  .badge.has-error { background: var(--err); color: #2a0008; }

  .console-body {
    flex: 1;
    overflow: auto;
    padding: 6px 0;
    font-family: var(--mono);
    font-size: 12.5px;
    line-height: 1.5;
  }
  .console-body .row {
    padding: 3px 14px;
    border-bottom: 1px solid rgba(255,255,255,.04);
    white-space: pre-wrap;
    word-break: break-word;
  }
  .row.log { color: var(--text); }
  .row.info { color: var(--accent); }
  .row.warn { color: var(--warn); background: rgba(255, 209, 102, .06); }
  .row.error { color: var(--err); background: rgba(255, 122, 138, .08); }
  .empty { padding: 8px 14px; color: #4d5470; font-family: var(--mono); font-size: 12.5px; }

  /* ---------- Toast ---------- */
  .toast {
    position: fixed; bottom: 18px; left: 50%;
    transform: translateX(-50%) translateY(20px);
    background: var(--panel-2);
    border: 1px solid var(--line);
    padding: 9px 16px;
    border-radius: 8px;
    font-size: 13px;
    opacity: 0; pointer-events: none;
    transition: opacity .2s, transform .2s;
  }
  .toast.show { opacity: 1; transform: translateX(-50%) translateY(0); }

  /* ---------- Mobile ---------- */
  @media (max-width: 800px) {
    main { grid-template-columns: 1fr; grid-template-rows: 1fr 1fr; }
    .actions { width: 100%; justify-content: space-between; }
    kbd { display: none; }
  }

  @media (prefers-reduced-motion: reduce) {
    * { transition: none !important; }
  }
</style>
</head>
<body>

<header>
  <div class="brand">
    <div class="brand-mark">&lt;/&gt;</div>
    Code Runner
  </div>
  <div class="actions">
    <label class="toggle">
      <input type="checkbox" id="auto-run" checked>
      Auto-run
    </label>
    <button id="btn-reset" title="Restore the starter code">Reset</button>
    <button id="btn-download" title="Download as a single .html file">Download</button>
    <button class="run-btn" id="btn-run">Run<kbd>Ctrl+Enter</kbd></button>
  </div>
</header>

<main>
  <!-- Editor -->
  <section class="pane" aria-label="Code editor">
    <div class="pane-bar">
      <div class="tabs" role="tablist">
        <button class="tab" role="tab" id="tab-html" data-target="html" aria-selected="true">HTML</button>
        <button class="tab" role="tab" id="tab-css"  data-target="css"  aria-selected="false">CSS</button>
        <button class="tab" role="tab" id="tab-js"   data-target="js"   aria-selected="false">JavaScript</button>
      </div>
    </div>
    <div class="editors">
      <div class="editor active" data-editor="html">
        <div class="gutter" aria-hidden="true"></div>
        <textarea id="in-html" spellcheck="false" autocapitalize="off" autocomplete="off" aria-label="HTML code"></textarea>
      </div>
      <div class="editor" data-editor="css">
        <div class="gutter" aria-hidden="true"></div>
        <textarea id="in-css" spellcheck="false" autocapitalize="off" autocomplete="off" aria-label="CSS code"></textarea>
      </div>
      <div class="editor" data-editor="js">
        <div class="gutter" aria-hidden="true"></div>
        <textarea id="in-js" spellcheck="false" autocapitalize="off" autocomplete="off" aria-label="JavaScript code"></textarea>
      </div>
    </div>
  </section>

  <!-- Preview -->
  <section class="pane" aria-label="Output">
    <div class="pane-bar"><div class="pane-title">Output</div></div>
    <div class="preview-wrap">
      <iframe id="preview" title="Output preview"
        sandbox="allow-scripts allow-modals allow-forms allow-popups"></iframe>
      <div class="console" id="console">
        <div class="console-bar">
          <span>Console<span class="badge" id="err-count">0</span></span>
          <div class="btns">
            <button id="btn-clear">Clear</button>
            <button id="btn-collapse">Hide</button>
          </div>
        </div>
        <div class="console-body" id="console-body"></div>
      </div>
    </div>
  </section>
</main>

<div class="toast" id="toast" role="status"></div>

<script>
(function () {
  'use strict';

  /* ---------- Starter code ---------- */
  const STARTER = {
    html: `<main class="card">
  <h1>Hello Sameer! 🚀</h1>
  <p>This is your live code output.</p>
  <button id="btn">Click me</button>
  <p id="count">Clicks: 0</p>
</main>`,
    css: `body {
  min-height: 100vh;
  display: grid;
  place-items: center;
  margin: 0;
  background: #f0fdf4;
  color: #14532d;
  font-family: system-ui, sans-serif;
}

.card {
  text-align: center;
  background: #fff;
  padding: 32px 40px;
  border-radius: 14px;
  box-shadow: 0 6px 24px rgba(20, 83, 45, .12);
}

button {
  margin-top: 8px;
  padding: 10px 20px;
  font-size: 15px;
  border: 0;
  border-radius: 8px;
  background: #16a34a;
  color: #fff;
  cursor: pointer;
}`,
    js: `let clicks = 0;

document.getElementById('btn').addEventListener('click', () => {
  clicks++;
  document.getElementById('count').textContent = 'Clicks: ' + clicks;
  console.log('Button clicked', clicks, 'times');
});`
  };

  /* ---------- Elements ---------- */
  const $ = (s, r = document) => r.querySelector(s);
  const $$ = (s, r = document) => Array.from(r.querySelectorAll(s));

  const inputs = { html: $('#in-html'), css: $('#in-css'), js: $('#in-js') };
  const preview = $('#preview');
  const consoleBody = $('#console-body');
  const errBadge = $('#err-count');
  const autoRun = $('#auto-run');
  const toastEl = $('#toast');

  let errorCount = 0;
  let debounceTimer = null;

  /* ---------- Storage (safe: may be blocked) ---------- */
  const KEY = 'code-runner-v1';
  function load() {
    try {
      const saved = JSON.parse(localStorage.getItem(KEY));
      if (saved && typeof saved === 'object') return saved;
    } catch (e) { /* ignore */ }
    return null;
  }
  function save() {
    try {
      localStorage.setItem(KEY, JSON.stringify({
        html: inputs.html.value, css: inputs.css.value, js: inputs.js.value
      }));
    } catch (e) { /* ignore */ }
  }

  /* ---------- Line numbers ---------- */
  function updateGutter(textarea) {
    const gutter = textarea.parentElement.querySelector('.gutter');
    const lines = textarea.value.split('\n').length;
    let out = '';
    for (let i = 1; i <= lines; i++) out += i + '\n';
    gutter.textContent = out;
    gutter.scrollTop = textarea.scrollTop;
  }

  /* ---------- Console ---------- */
  function renderEmpty() {
    if (!consoleBody.children.length) {
      consoleBody.innerHTML = '<div class="empty">Console output and errors will appear here.</div>';
    }
  }

  function addRow(type, text) {
    const empty = $('.empty', consoleBody);
    if (empty) empty.remove();
    const row = document.createElement('div');
    row.className = 'row ' + type;
    row.textContent = text;
    consoleBody.appendChild(row);
    consoleBody.scrollTop = consoleBody.scrollHeight;
    if (type === 'error') {
      errorCount++;
      errBadge.textContent = errorCount;
      errBadge.classList.add('has-error');
    }
  }

  function clearConsole() {
    consoleBody.innerHTML = '';
    errorCount = 0;
    errBadge.textContent = '0';
    errBadge.classList.remove('has-error');
    renderEmpty();
  }

  window.addEventListener('message', (e) => {
    if (e.source !== preview.contentWindow) return;
    const d = e.data;
    if (!d || d.__runner !== true) return;
    addRow(d.type, d.text);
  });

  /* ---------- Build + run ---------- */
  // Injected into the preview so console.log and errors reach the Console panel.
  const BRIDGE = '(' + function () {
    function fmt(a) {
      try {
        if (a instanceof Error) return a.name + ': ' + a.message;
        if (typeof a === 'object' && a !== null) return JSON.stringify(a, null, 2);
        return String(a);
      } catch (e) { return String(a); }
    }
    function send(type, args) {
      parent.postMessage({ __runner: true, type: type, text: args.map(fmt).join(' ') }, '*');
    }
    ['log', 'info', 'warn', 'error'].forEach(function (m) {
      var orig = console[m];
      console[m] = function () {
        send(m, Array.prototype.slice.call(arguments));
        orig.apply(console, arguments);
      };
    });
    window.addEventListener('error', function (e) {
      send('error', [e.message + (e.lineno ? ' (line ' + e.lineno + ')' : '')]);
    });
    window.addEventListener('unhandledrejection', function (e) {
      send('error', ['Unhandled promise rejection: ' + fmt(e.reason)]);
    });
  } + ')();';

  function buildDocument(forDownload) {
    const html = inputs.html.value;
    const css = inputs.css.value;
    // Stop user code from closing our <script> / <style> tags early.
    const js = inputs.js.value.replace(/<\/script/gi, '<\\/script');
    const safeCss = css.replace(/<\/style/gi, '<\\/style');

    return '<!DOCTYPE html>\n<html lang="en">\n<head>\n<meta charset="UTF-8">\n' +
      '<meta name="viewport" content="width=device-width, initial-scale=1.0">\n' +
      '<title>My Page</title>\n' +
      (forDownload ? '' : '<script>' + BRIDGE + '<\/script>\n') +
      '<style>\n' + safeCss + '\n</style>\n</head>\n<body>\n' +
      html + '\n<script>\n' + js + '\n<\/script>\n</body>\n</html>';
  }

  function runCode() {
    clearConsole();
    preview.srcdoc = buildDocument(false);
    save();
  }

  /* ---------- Tabs ---------- */
  function showTab(name) {
    $$('.tab').forEach(t => t.setAttribute('aria-selected', String(t.dataset.target === name)));
    $$('.editor').forEach(ed => ed.classList.toggle('active', ed.dataset.editor === name));
    inputs[name].focus();
    updateGutter(inputs[name]);
  }

  $$('.tab').forEach(t => t.addEventListener('click', () => showTab(t.dataset.target)));

  /* ---------- Editor behaviour ---------- */
  Object.values(inputs).forEach((ta) => {
    ta.addEventListener('input', () => {
      updateGutter(ta);
      if (autoRun.checked) {
        clearTimeout(debounceTimer);
        debounceTimer = setTimeout(runCode, 500);
      }
    });

    ta.addEventListener('scroll', () => {
      ta.parentElement.querySelector('.gutter').scrollTop = ta.scrollTop;
    });

    // Tab key inserts two spaces instead of moving focus away.
    ta.addEventListener('keydown', (e) => {
      if (e.key === 'Tab' && !e.ctrlKey && !e.altKey && !e.metaKey) {
        e.preventDefault();
        const s = ta.selectionStart, end = ta.selectionEnd;
        ta.value = ta.value.slice(0, s) + '  ' + ta.value.slice(end);
        ta.selectionStart = ta.selectionEnd = s + 2;
        ta.dispatchEvent(new Event('input'));
      }
    });
  });

  document.addEventListener('keydown', (e) => {
    if ((e.ctrlKey || e.metaKey) && e.key === 'Enter') {
      e.preventDefault();
      runCode();
    }
  });

  /* ---------- Buttons ---------- */
  $('#btn-run').addEventListener('click', runCode);

  $('#btn-reset').addEventListener('click', () => {
    if (!confirm('Reset all three editors to the starter code?')) return;
    Object.keys(inputs).forEach(k => { inputs[k].value = STARTER[k]; updateGutter(inputs[k]); });
    runCode();
    toast('Starter code restored');
  });

  $('#btn-download').addEventListener('click', () => {
    const blob = new Blob([buildDocument(true)], { type: 'text/html' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'my-page.html';
    document.body.appendChild(a);
    a.click();
    a.remove();
    setTimeout(() => URL.revokeObjectURL(url), 1000);
    toast('Downloaded my-page.html');
  });

  $('#btn-clear').addEventListener('click', clearConsole);

  $('#btn-collapse').addEventListener('click', () => {
    const c = $('#console');
    const collapsed = c.classList.toggle('collapsed');
    $('#btn-collapse').textContent = collapsed ? 'Show' : 'Hide';
  });

  /* ---------- Toast ---------- */
  let toastTimer;
  function toast(msg) {
    toastEl.textContent = msg;
    toastEl.classList.add('show');
    clearTimeout(toastTimer);
    toastTimer = setTimeout(() => toastEl.classList.remove('show'), 1800);
  }

  /* ---------- Init ---------- */
  const saved = load();
  Object.keys(inputs).forEach(k => {
    inputs[k].value = saved && typeof saved[k] === 'string' ? saved[k] : STARTER[k];
    updateGutter(inputs[k]);
  });
  renderEmpty();
  runCode();
})();
</script>
</body>
</html>
