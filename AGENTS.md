# Agent Instructions — Handwritten Assignment Workflow

> **Read this first.** This file is auto-discovered by OpenCode and most agent
> harnesses. The styled version lives in [`AGENT-INSTRUCTIONS.html`](AGENT-INSTRUCTIONS.html)
> — open it for visuals and the full CSS, but **everything required to do the
> work correctly is duplicated here** so a second read is never needed.

Project folder: `E:\Workplace\Assignment`

---

## 1. Files

| File | Status | Purpose |
|---|---|---|
| `wraith-ink-sonata-regular.ttf` | **KEEP** | The handwriting font. Every assignment loads it by relative path. Never delete or rename. |
| `victor-handwriting.regular.ttf`, `GloriaHallelujah-Regular.ttf`, `davyscrappywrit.regular.ttf`, `james-tan-dinawanao.regular.ttf` | KEEP (unused) | Alternate hands from the original font pack. **Not loaded** — none matches Wraith's ascender/descender proportions (§6). Kept in case a future assignment needs a genuinely different hand. |
| `experiment-05.html` | **KEEP** | Reference implementation. Copy its structure and CSS as the template for any new assignment. |
| `AGENT-INSTRUCTIONS.html` | **KEEP** | Human-readable / printable version of these rules. |
| `AGENTS.md` | **KEEP** | This file. |

Older assignments and all source PDFs/DOCX were deleted at the user's request on
18 Oct 2026. If they reappear, treat them as user work — do not delete without asking.

---

## 2. The deliverable

One self-contained HTML page that looks like **blue handwriting on ruled notebook
paper**, with a red margin rule, words drifting between three close pens (§6), and
printing cleanly to A4.

---

## 3. Workflow

1. **Get content as text.** ChatGPT share links will not load — they require
   JavaScript and return only the title. Ask the user to paste Q&A as plain text.
2. **Read source files.** `.docx` = ZIP → unzip `word/document.xml`, strip tags.
   Scanned `.pdf` has no text layer → extract embedded JPEGs and read as images.
3. **Reuse the template** from `experiment-05.html`. Do not restyle from scratch.
4. **Add pen variation.** Paste the script from §6 before `</body>`. Never
   hand-split words into spans — the script is the single source of truth.
5. **Verify** (section 7) before delivering.

---

## 4. Required CSS core

```css
@font-face {
  font-family: 'Wraith Ink';
  src: url('wraith-ink-sonata-regular.ttf') format('truetype');
}
body {
  font-family: 'Wraith Ink', cursive;
  color: #15249b;                 /* blue ink */
  -webkit-print-color-adjust: exact;
  print-color-adjust: exact;      /* rules must survive printing */
}

/* Ruled paper: red margin rule + 32px blue lines */
.sheet {
  width: 210mm;
  background-color: #fffdf7;
  background-image:
    linear-gradient(90deg, transparent 21mm, rgba(230,60,60,.65) 21mm,
                    rgba(230,60,60,.65) 21.6mm, transparent 21.6mm),
    repeating-linear-gradient(to bottom, transparent 0, transparent 31px,
                    rgba(60,140,220,.45) 31px, rgba(60,140,220,.45) 32px);
  padding: 32px 12mm 32px 25mm;   /* all multiples of 32px */
  line-height: 32px;              /* one line of text per rule */
  min-height: 297mm;
}

/* CRITICAL: ink must sit ON the line, not float above it */
.head-row, h2.q, h3, p, li, .title, .code, .trav, .out, .sign, td, th {
  position: relative;
  top: 7px;
}

/* Pen variation: split words, one pen per chunk (§6) */
.wd { white-space: nowrap; }
.chunk {
  display: inline-block;
  white-space: nowrap;
  transform: translateY(var(--dy, 0px)) rotate(var(--rot, 0deg))
             skewX(var(--sk, 0deg)) scale(calc(var(--sc, 1) * var(--pen, 1)));
  transform-origin: 50% 100%;
  letter-spacing: var(--ls, 0);
}
.chunk[data-pen="1"] { --sk: 0deg; --pen: 1; }
.chunk[data-pen="2"] { --sk: -3.4deg; --pen: 1.03; --ls: .05px; }
.chunk[data-pen="3"] { --sk: 2.6deg; --pen: .97; --ls: .25px; }

@page { size: A4; margin: 0; }
@media print {
  body { background: #fff; }
  .toolbar { display: none !important; }
  .sheet { width: auto; margin: 0; box-shadow: none; padding: 12mm 10mm 12mm 24mm; }
}
```

### Why `line-height: 32px` matters
Rules repeat every 32px. Text line-height must equal 32px and sheet padding must
be a multiple of 32px, or every line drifts off its rule. Block spacing must also
be multiples of 32 (`margin: 32px 0 0 0`, never `16px`).

### Why `top: 7px` matters
Handwriting fonts have large ascender space, so the baseline lands above the rule
and the text looks like it floats. Shifting every text block down 7px seats it on
the line. If it looks off on the user's screen, nudge to 9px — never remove it.

### Alignment rule learned 18 Oct 2026
**Every top-level block inside `.sheet` must be an integer multiple of 32px tall.**
A fixed-height element (e.g. a Page No. stamp box) will silently push *everything
below it* out of phase. Give such wrappers an explicit `height: 64px` (or another
32-multiple) and confirm by eye that text below still sits on the rules.

---

## 5. Conventions the user expects

| Element | Rule |
|---|---|
| Page heading | Only `Assignment - 0N`, centred, underlined, large. **No** name/roll/branch/date/subject block — the user had it removed. |
| Date field | Optional `Date - ____ / ____ / 26` top-left + small Page No./Date stamp box top-right. |
| Section headings | Underlined via CSS only (`h2.q`, `h3`). |
| **Bold text** | **NEVER.** The user explicitly rejected `<b>` on labels such as `Aim :`, `Binary Tree :`, `Types of traversal :`. Labels are plain text — distinguish them by position, not weight. Also avoid `font-weight: bold`. |
| Inline underlines | Almost none. Do **not** wrap labels in `<u>`. At most `Ans.`, `Conclusion :`, `Result :`. |
| Length | Match supplied content exactly. Never pad or summarise. |
| Scope | Mirror the reference sample's length — the user rejected an over-long draft once. |
| Equations | Unicode subscripts: `Fe₂O₃ + 3CO → 2Fe + 3CO₂`. Never keep raw `\mathrm{...}` LaTeX. |
| Code blocks | `<pre class="code">`, same font, 32px line-height. Escape `<` as `&lt;`. |
| Word variation | Every eligible word splits into 2–3 chunks, each on a different pen (§6). Never hand-split; paste the script. |

---

## 6. Pen variation — split words across similar hands

Handwriting is not one typeface. Real script drifts in slant, size and spacing
several times inside a single word. Reproduce that cheaply: break each eligible
word into 2–3 chunks and give each chunk a different pen.

User's wording: *each set of letters is written from a different yet similar font.*
Neighbouring chunks must differ, but never enough to look like two authors.

### The pens are derived from the approved font

All three pens are the same face (`Wraith Ink`) with a small affine change, so they
can never clash:

| Pen | CSS | Look |
|---|---|---|
| 1 | `--sk: 0deg`, `--pen: 1` | the approved hand, unchanged |
| 2 | `--sk: -3.4deg`, `--pen: 1.03` | leans forward, a touch larger |
| 3 | `--sk: 2.6deg`, `--pen: .97`, `letter-spacing: .25px` | leans back, airier |

Do **not** load the other four TTFs for this. Measured against Wraith, Victor's
ascenders are 29% shorter and its descenders 41–51% shorter; Gloria's x-height is
25% larger. Mixed families read as a collage, not as one hand. A second family may
join the pool only if x-height, cap-height, ascender and descender all land within
about 8% of Wraith's.

### The algorithm

A runtime script, pasted once before `</body>`. It never edits the source text by
hand, so the same page always renders the same way.

1. Walk every text node in `.sheet`; skip `pre`, `code`, `h1`, `h2`, `h3`, `.title`
   and anything marked `data-no-split`.
2. Eligible word = `/^[A-Za-z][A-Za-z'’-]*$/` and at least 4 letters. Numbers,
   formulas, e-mail addresses and variable names are left whole.
3. Seed = FNV-1a hash of the word, XORed with the page seed and the word's index,
   fed to a mulberry32 PRNG. Deterministic: reload and print match exactly.
4. 40% of eligible words split (`SPLIT_RATE = 0.40`); the rest stay whole. That
   restraint is what makes it look natural.
5. Split into 2 chunks; 25% of words with 6+ letters get 3 (`THREE_WAY = 0.25`).
6. Cut points keep every chunk at 2+ letters (`MIN_CHUNK = 2`).
7. Pens are drawn without replacement, so neighbouring chunks — and all chunks in
   one word — always differ.
8. Each chunk also gets its own micro-jitter: rotate ±0.5°, shift ±0.7px, scale
   0.99–1.01 on top of its pen's own scale. That is what stops identical pens from
   looking identical.
9. If JavaScript is off, the page is plain text — still correct, just uniform.

### Tuning

| Constant | Default | Effect |
|---|---|---|
| `SPLIT_RATE` | `0.40` | share of eligible words split |
| `THREE_WAY` | `0.25` | chance a 6+ letter word uses 3 pens |
| `MIN_CHUNK` | `2` | shortest allowed piece |
| `SEED` | `0x5f356495` | change per assignment for a different cut pattern |

Keep the defaults unless the user asks. Never raise `SPLIT_RATE` above ~0.6 —
every word splitting reads as a ransom note, not handwriting.

### Script (paste before `</body>`)

```html
<script>
/* Pen variation: split prose words into 2-3 chunks, each in a different pen.
   Deterministic - the same text + seed renders identically on reload and print. */
(function () {
  var SPLIT_RATE = 0.40;   // share of eligible words that get split
  var THREE_WAY = 0.25;    // chance that a 6+ letter split uses 3 chunks
  var MIN_CHUNK = 2;       // never leave a one-letter piece
  var SEED = 0x5f356495;   // one seed per assignment
  var WORD = /^[A-Za-z][A-Za-z'\u2019-]*$/;

  function hash(s) {
    var h = 2166136261;
    for (var i = 0; i < s.length; i++) {
      h ^= s.charCodeAt(i);
      h = Math.imul(h, 16777619);
    }
    return h >>> 0;
  }

  function rng(seed) {
    return function () {
      seed = seed + 0x6D2B79F5 | 0;
      var t = Math.imul(seed ^ seed >>> 15, 1 | seed);
      t = t + Math.imul(t ^ t >>> 7, 61 | t) ^ t;
      return ((t ^ t >>> 14) >>> 0) / 4294967296;
    };
  }

  function cut(word, rnd) {
    var n = word.length;
    if (n < 2 * MIN_CHUNK || rnd() >= SPLIT_RATE) return [word];
    var k = n >= 3 * MIN_CHUNK && rnd() < THREE_WAY ? 3 : 2;
    var cuts = [], c;
    if (k === 2) {
      cuts.push(MIN_CHUNK + Math.floor(rnd() * (n - 2 * MIN_CHUNK + 1)));
    } else {
      c = MIN_CHUNK + Math.floor(rnd() * (n - 3 * MIN_CHUNK + 1));
      cuts.push(c);
      cuts.push(c + MIN_CHUNK + Math.floor(rnd() * (n - c - 2 * MIN_CHUNK + 1)));
    }
    var parts = [], last = 0;
    for (var i = 0; i < cuts.length; i++) {
      parts.push(word.slice(last, cuts[i]));
      last = cuts[i];
    }
    parts.push(word.slice(last));
    return parts;
  }

  function pens(k, rnd) {
    var pool = [1, 2, 3];
    var out = [];
    while (out.length < k) out.push(pool.splice(Math.floor(rnd() * pool.length), 1)[0]);
    return out;
  }

  function build(word, seed) {
    var rnd = rng(seed);
    var parts = cut(word, rnd);
    if (parts.length < 2) return null;
    var names = pens(parts.length, rnd);
    var wd = document.createElement('span');
    wd.className = 'wd';
    parts.forEach(function (part, i) {
      var s = document.createElement('span');
      s.className = 'chunk';
      s.setAttribute('data-pen', names[i]);
      s.style.setProperty('--rot', (rnd() * 1.0 - 0.5).toFixed(2) + 'deg');
      s.style.setProperty('--dy', (rnd() * 1.4 - 0.7).toFixed(2) + 'px');
      s.style.setProperty('--sc', (0.99 + rnd() * 0.02).toFixed(3));
      s.textContent = part;
      wd.appendChild(s);
    });
    return wd;
  }

  var sheet = document.querySelector('.sheet');
  if (!sheet || sheet.querySelector('.chunk')) return;

  var walk = document.createTreeWalker(sheet, NodeFilter.SHOW_TEXT, null);
  var nodes = [], n;
  while ((n = walk.nextNode())) nodes.push(n);

  var index = 0;
  nodes.forEach(function (node) {
    var el = node.parentElement;
    if (!el || el.closest('pre, code, h1, h2, h3, .title, [data-no-split]')) return;
    var text = node.nodeValue;
    if (!/[A-Za-z]{4,}/.test(text)) return;

    var frag = document.createDocumentFragment();
    text.split(/(\s+)/).forEach(function (token) {
      if (!WORD.test(token)) {
        frag.appendChild(document.createTextNode(token));
        return;
      }
      var built = build(token, hash(token) ^ SEED ^ Math.imul(index++, 2654435761));
      frag.appendChild(built || document.createTextNode(token));
    });
    node.parentNode.replaceChild(frag, node);
  });
})();
</script>
```

---

## 7. Verify before delivering

1. Font path resolves relative to the HTML file — keep both in the same folder.
2. No raw `<` inside `<pre>` blocks (use `&lt;`).
3. Count `<b>` and `<u>` tags — both should be **0**.
4. Every top-level block in `.sheet` is a multiple of 32px tall; text sits on the rules.
5. If output examples are shown, verify them by hand or by running the code.
6. Word splitting: every `.wd` reads back as the original word, every chunk has 2+
   letters, and no two neighbouring chunks share a `data-pen`.
7. No `.chunk` inside `pre`, `code`, `h1`–`h3`, `.title` or `[data-no-split]`.
8. Reload twice — the cut pattern must be identical (deterministic seed).

---

## 8. Humanistic errors (make it look real)

Light, deliberate imperfection. Too many looks fake; too few looks printed.

| Type | Markup | Examples used |
|---|---|---|
| Struck-out correction | `<span class="cross">typo</span> fixedword` | ~~resorces~~ resources · ~~machinary~~ machinery · ~~similiar~~ similar |
| Ink smudge | `<span class="smudge"></span>` | Once or twice per assignment, near a paragraph end |
| Plain misspelling | write it directly | `seperating`, `occuring`, `tranportation`, `temprature`, `sulpher`, `maintanance`, `managment` |

```css
.cross { text-decoration: line-through; text-decoration-thickness: 2px; }
.smudge {
  display: inline-block; width: 14px; height: 10px;
  background: radial-gradient(ellipse at center, rgba(21,36,155,.55) 0,
              rgba(21,36,155,.25) 60%, transparent 70%);
  border-radius: 50%; vertical-align: baseline; margin: 0 2px;
}
```

**Target:** 2–3 struck corrections · 1–2 smudges · 2–4 plain misspellings.
Never corrupt formulas, code, variable names, or numerical answers.

---

## 9. How the user prints

1. Open in Chrome or Edge.
2. Click **Print Assignment** in the top toolbar.
3. Set **Paper: A4**, **Margins: None**, and enable **Background graphics** —
   without it the ruled lines and red margin will not print.
4. Print or Save as PDF.

---

## Quick start for the next assignment

1. Copy `experiment-05.html` to a new file.
2. Swap the heading to `Assignment - 0N`, replace the body with the new content.
3. Keep the CSS core untouched, apply the humanistic errors, paste the §6 script
   before `</body>`, then run section 7.
