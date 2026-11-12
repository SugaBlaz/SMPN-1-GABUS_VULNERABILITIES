(() => {
  'use strict';

  const ROOT_ID = '__lomat_control_center__';
  const existing = document.getElementById(ROOT_ID);
  if (existing) {
    existing.remove();
    console.info('[Lomat] Control Center closed. Run the script again to reopen it.');
    return;
  }

  // The quiz pages expose their question/session data as global lexical bindings.
  // This script is intended to be pasted into DevTools Console on one of the supplied HTML files.
  let adapter;
  try {
    if (typeof Q === 'undefined' || typeof S === 'undefined' || !Array.isArray(Q)) {
      throw new Error('Quiz data not found. Start/open one of the three Lomat HTML files first.');
    }

    if (Q.length && !Array.isArray(Q[0]) && typeof Q[0] === 'object' && 'k' in Q[0]) {
      adapter = {
        name: 'Kelas 7 • Paket 3',
        total: Q.length,
        active: () => !!S && Array.isArray(S.ord) && Number.isInteger(S.i),
        current: () => S.ord[S.i],
        correct: qi => Q[qi].k,
        optionText: (qi, oi) => Q[qi].o[oi],
        displayedPosition: (qi, oi) => S.op[qi].indexOf(oi),
        apply: () => {
          for (let i = 0; i < Q.length; i++) S.a[i] = Q[i].k;
        },
        refresh: () => {
          if (typeof draw === 'function') draw();
        },
        questionNumber: () => S.i + 1
      };
    } else if (typeof K !== 'undefined' && typeof K === 'string') {
      adapter = {
        name: 'Kelas 7 • Paket 2',
        total: Q.length,
        active: () => !!S && Array.isArray(S.ord) && Number.isInteger(S.cur),
        current: () => S.ord[S.cur],
        correct: qi => K.charCodeAt(qi) - 65,
        optionText: (qi, oi) => Q[qi][2][oi],
        displayedPosition: (qi, oi) => S.op[qi].indexOf(oi),
        apply: () => {
          for (let i = 0; i < Q.length; i++) {
            S.a[i] = K.charCodeAt(i) - 65;
          }
        },
        refresh: () => {
          if (typeof draw === 'function') draw();
        },
        questionNumber: () => S.cur + 1
      };
    } else if (
      Array.isArray(Q[0]) &&
      !Array.isArray(Q[0][2]) &&
      Array.isArray(Q[0][3])
    ) {
      adapter = {
        name: 'Kelas 7 • Paket 1',
        total: Q.length,
        active: () => !!S && Array.isArray(S.o) && Number.isInteger(S.i),
        current: () => S.o[S.i],
        correct: () => 0,
        optionText: (qi, oi) => Q[qi][3][oi],
        displayedPosition: (qi, oi) => S.p[qi].indexOf(oi),
        apply: () => {
          for (let i = 0; i < Q.length; i++) S.a[i] = 0;
        },
        refresh: () => {
          if (typeof draw === 'function') draw();
        },
        questionNumber: () => S.i + 1
      };
    } else {
      throw new Error('This page does not match any of the three supported Lomat versions.');
    }
  } catch (err) {
    console.error('[Lomat] ' + err.message);
    alert('[Lomat] ' + err.message);
    return;
  }

  const host = document.createElement('div');
  host.id = ROOT_ID;
  host.style.cssText =
    'position:fixed;top:90px;right:24px;z-index:2147483647;';
  document.documentElement.appendChild(host);

  const shadow = host.attachShadow({ mode: 'open' });

  shadow.innerHTML = `
    <style>
      :host {
        all: initial;
        color-scheme: dark;
      }

      * {
        box-sizing: border-box;
      }

      #panel {
        width: 310px;
        max-width: calc(100vw - 24px);
        font: 13px/1.45 Inter, ui-sans-serif, system-ui, Segoe UI, sans-serif;
        color: var(--fg);
        background: var(--panel);
        border: 1px solid var(--line);
        border-radius: 15px;
        box-shadow: 0 18px 55px #0008;
        overflow: hidden;
        user-select: none;
      }

      #head {
        height: 43px;
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 0 12px;
        background: var(--head);
        border-bottom: 1px solid var(--line);
        cursor: grab;
        touch-action: none;
      }

      #head:active {
        cursor: grabbing;
      }

      .brand {
        font-weight: 850;
        letter-spacing: .3px;
      }

      .brand span {
        color: var(--accent);
      }

      .headBtns {
        display: flex;
        gap: 5px;
      }

      .iconbtn {
        width: 25px;
        height: 25px;
        padding: 0;
        border: 1px solid var(--line);
        border-radius: 7px;
        background: #ffffff0c;
        color: var(--fg);
        cursor: pointer;
      }

      #body {
        padding: 12px;
        display: grid;
        gap: 10px;
      }

      .meta {
        display: flex;
        justify-content: space-between;
        align-items: center;
        color: var(--muted);
        font-size: 11px;
      }

      select {
        width: 100%;
        padding: 8px 9px;
        border-radius: 8px;
        border: 1px solid var(--line);
        background: var(--input);
        color: var(--fg);
        font: inherit;
        outline: none;
      }

      .switchrow {
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 10px;
        border: 1px solid var(--line);
        border-radius: 10px;
        background: #ffffff06;
      }

      .switchcopy b {
        display: block;
        font-size: 12px;
      }

      .switchcopy small {
        color: var(--muted);
        font-size: 10px;
      }

      .switch {
        position: relative;
        width: 39px;
        height: 22px;
        flex: none;
      }

      .switch input {
        position: absolute;
        opacity: 0;
        width: 1px;
        height: 1px;
      }

      .track {
        position: absolute;
        inset: 0;
        border-radius: 20px;
        background: #596273;
        cursor: pointer;
        transition: .18s;
      }

      .track:before {
        content: '';
        position: absolute;
        width: 16px;
        height: 16px;
        left: 3px;
        top: 3px;
        background: #fff;
        border-radius: 50%;
        transition: .18s;
      }

      .switch input:checked + .track {
        background: var(--accent);
      }

      .switch input:checked + .track:before {
        transform: translateX(17px);
      }

      button.action {
        width: 100%;
        padding: 10px;
        border: 0;
        border-radius: 9px;
        font: 750 12px/1.2 system-ui, Segoe UI, sans-serif;
        cursor: pointer;
        transition: filter .15s, transform .15s;
      }

      button.action:hover {
        filter: brightness(1.12);
      }

      button.action:active {
        transform: scale(.985);
      }

      #reveal {
        background: var(--accent);
        color: #fff;
      }

      #apply {
        background: var(--second);
        color: #07120e;
      }

      #answer {
        display: none;
        padding: 10px;
        border: 1px solid var(--answerline);
        border-radius: 9px;
        background: var(--answerbg);
        color: var(--fg);
        overflow-wrap: anywhere;
      }

      #answer strong {
        color: var(--good);
      }

      #answer small {
        display: block;
        color: var(--muted);
        margin-top: 3px;
      }

      #foot {
        display: flex;
        justify-content: space-between;
        gap: 8px;
        color: var(--muted);
        font-size: 10px;
        padding-top: 2px;
      }

      #panel[data-theme="midnight"] {
        --panel: #0b1020;
        --head: #111a30;
        --input: #111a30;
        --fg: #e8eeff;
        --muted: #8f9bb8;
        --line: #263453;
        --accent: #7c5cff;
        --second: #38d9a9;
        --answerbg: #15233a;
        --answerline: #3b5c86;
        --good: #5be0a4;
      }

      #panel[data-theme="terminal"] {
        --panel: #07110c;
        --head: #0c1d14;
        --input: #0b1b12;
        --fg: #c9f7d5;
        --muted: #79a78a;
        --line: #244a32;
        --accent: #20c56b;
        --second: #b5f36b;
        --answerbg: #0c2416;
        --answerline: #347b4a;
        --good: #a8f27b;
      }

      #panel[data-theme="glass"] {
        --panel: #172033ee;
        --head: #26344dee;
        --input: #202d45;
        --fg: #f2f6ff;
        --muted: #a8b5ce;
        --line: #ffffff26;
        --accent: #58a6ff;
        --second: #72e0c0;
        --answerbg: #1d3554;
        --answerline: #5a8ec4;
        --good: #86e8c2;
        backdrop-filter: blur(16px);
      }

      #panel.min #body {
        display: none;
      }

      #panel.min {
        width: 210px;
      }

      @media (max-width: 420px) {
        #panel {
          width: 280px;
        }
      }
    </style>

    <section id="panel" data-theme="midnight">
      <header id="head">
        <div class="brand">LOMAT <span>CONTROL</span></div>

        <div class="headBtns">
          <button class="iconbtn" id="min" title="Minimize">−</button>
          <button class="iconbtn" id="close" title="Close">×</button>
        </div>
      </header>

      <div id="body">
        <div class="meta">
          <span id="version"></span>
          <span id="position"></span>
        </div>

        <label>
          <span style="display:block;color:var(--muted);font-size:10px;margin-bottom:5px">
            INTERFACE THEME
          </span>

          <select id="theme">
            <option value="midnight">Midnight Violet</option>
            <option value="terminal">Terminal Green</option>
            <option value="glass">Glass Blue</option>
          </select>
        </label>

        <div class="switchrow">
          <div class="switchcopy">
            <b>Auto-answer</b>
            <small>Fill every question with its stored key</small>
          </div>

          <label class="switch">
            <input id="auto" type="checkbox">
            <span class="track"></span>
          </label>
        </div>

        <button class="action" id="apply">APPLY ANSWERS</button>
        <button class="action" id="reveal">SHOW CORRECT ANSWER</button>

        <div id="answer"></div>

        <div id="foot">
          <span>Local page helper</span>
          <span id="status">Ready</span>
        </div>
      </div>
    </section>
  `;

  const $ = id => shadow.getElementById(id);
  const panel = $('panel');

  $('version').textContent = adapter.name;

  function getCurrentAnswer() {
    if (!adapter.active()) {
      throw new Error('Start or resume the quiz before using answer controls.');
    }

    const qi = adapter.current();
    const oi = adapter.correct(qi);
    const pos = adapter.displayedPosition(qi, oi);
    const letter = pos >= 0 ? 'ABCD'[pos] : '?';
    const text = adapter.optionText(qi, oi);

    return { qi, oi, pos, letter, text };
  }

  function showAnswer() {
    try {
      const a = getCurrentAnswer();

      $('answer').style.display = 'block';
      $('answer').innerHTML = `
        <strong>
          Question ${adapter.questionNumber()} · ${a.letter}
        </strong>
        <div>${escapeHTML(a.text)}</div>
        <small>Correct option for this question</small>
      `;

      $('position').textContent =
        `Q ${adapter.questionNumber()} / ${adapter.total}`;

      $('status').textContent = 'Answer shown';
    } catch (e) {
      $('status').textContent = 'Not started';
      alert('[Lomat] ' + e.message);
    }
  }

  function applyAnswers() {
    try {
      if (!adapter.active()) {
        throw new Error('Start or resume the quiz first.');
      }

      adapter.apply();
      adapter.refresh();

      $('status').textContent =
        `Filled ${adapter.total}/${adapter.total}`;

      $('position').textContent =
        `Q ${adapter.questionNumber()} / ${adapter.total}`;

      $('answer').style.display = 'none';

      console.info(
        `[Lomat] Applied the embedded answer key to ${adapter.total} questions (${adapter.name}).`
      );
    } catch (e) {
      $('status').textContent = 'Error';
      alert('[Lomat] ' + e.message);
    }
  }

  function escapeHTML(value) {
    return String(value).replace(/[&<>"']/g, c => ({
      '&': '&amp;',
      '<': '&lt;',
      '>': '&gt;',
      '"': '&quot;',
      "'": '&#39;'
    })[c]);
  }

  $('theme').addEventListener('change', e => {
    panel.dataset.theme = e.target.value;
  });

  $('reveal').addEventListener('click', showAnswer);
  $('apply').addEventListener('click', applyAnswers);

  $('auto').addEventListener('change', e => {
    if (e.target.checked) {
      applyAnswers();
    }

    $('status').textContent = e.target.checked
      ? `Auto-applied ${adapter.total}`
      : 'Auto-answer off';
  });

  $('min').addEventListener('click', () => {
    panel.classList.toggle('min');

    $('min').textContent = panel.classList.contains('min')
      ? '+'
      : '−';
  });

  $('close').addEventListener('click', () => host.remove());

  // Drag by the title bar only.
  // Pointer capture keeps dragging smooth outside the header.
  const head = $('head');
  let drag = null;

  head.addEventListener('pointerdown', e => {
    if (e.target.closest('button')) return;

    const rect = host.getBoundingClientRect();

    drag = {
      x: e.clientX - rect.left,
      y: e.clientY - rect.top
    };

    head.setPointerCapture(e.pointerId);
  });

  head.addEventListener('pointermove', e => {
    if (!drag) return;

    const maxX = Math.max(0, innerWidth - host.offsetWidth);
    const maxY = Math.max(0, innerHeight - host.offsetHeight);

    host.style.left =
      Math.min(maxX, Math.max(0, e.clientX - drag.x)) + 'px';

    host.style.top =
      Math.min(maxY, Math.max(0, e.clientY - drag.y)) + 'px';

    host.style.right = 'auto';
  });

  const stopDrag = () => {
    drag = null;
  };

  head.addEventListener('pointerup', stopDrag);
  head.addEventListener('pointercancel', stopDrag);

  // Keep the question indicator current without polling.
  const syncPosition = () => {
    try {
      if (adapter.active()) {
        $('position').textContent =
          `Q ${adapter.questionNumber()} / ${adapter.total}`;
      }
    } catch (_) {}
  };

  syncPosition();

  console.info(
    `[Lomat] Control Center loaded: ${adapter.name}. Choose a theme, then use Auto-answer or Show correct answer.`
  );
})();
