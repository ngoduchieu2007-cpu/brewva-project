# Brewva

A Duolingo-style app for learning programming from zero. Pick Java or Python,
work through bite-sized lessons, and write real code directly in the browser.

**Live:** https://serene-phoenix-131e99.netlify.app

<img width="445" height="361" alt="image" src="https://github.com/user-attachments/assets/ac608dbd-75bb-4149-a7e1-1b30e2016716" />

---

## Features

- **Two language tracks** — Java and Python, switchable from the home screen
- **Five question types** — multiple choice, true/false, tile-ordering, fill-in-the-blank, and write-code
- **Real Python execution** — type-code answers run in-browser via Pyodide (WebAssembly CPython); any valid solution passes, not just string matches
- **AI feedback** — wrong answers on code questions get a contextual hint from Claude, fetched server-side so the API key stays private
- **Gamification** — hearts, XP, daily streak, confetti
- **PWA** — installable on mobile, progress saved in localStorage

<img width="478" height="853" alt="image" src="https://github.com/user-attachments/assets/a33c22f4-869b-4674-83cd-db83699c9d8d" />

---

## Stack

Vanilla HTML, CSS, and JavaScript. No framework, no bundler, no build step.
Scripts share a global scope and load in order via plain `<script>` tags.

AI hints are proxied through a Netlify serverless function (`netlify/functions/check-code.js`)
so the Claude API key is never exposed to the client.

## Project layout

```
index.html                  page shell
css/styles.css
data/
  units.js                  Java lesson content
  python.js                 Python lesson content
js/
  state.js                  save/load, hearts, streak
  screens.js                home, intro, complete screens
  questions.js              question types + answer checking
  pyodide-runner.js         lazy Pyodide loader + Python runner
  icons.js / helpers.js / app.js
netlify/functions/
  check-code.js             Claude API proxy
```

## Running locally

```bash
python3 -m http.server 8000
# open http://localhost:8000
```

A local server is needed because some browsers restrict localStorage on `file://` URLs.
Live Server in VS Code also works.

## Deploying

The site is static. Drag the project folder onto https://app.netlify.com/drop,
or connect the repo for automatic deploys on push (config in `netlify.toml`).

For AI hints to work, add `CLAUDE_API_KEY` in Netlify under
**Site configuration → Environment variables**. The app works fine without it —
hints just won't appear.

## Adding content

Open `data/units.js` or `data/python.js`, find a lesson, and append to its
`questions` array. Five question types are supported:

```js
{ type: 'mc',    q: '...', opts: ['A','B','C','D'], ans: 1, why: '...' }
{ type: 'tf',    q: '...', ans: true, why: '...' }
{ type: 'build', q: '...', tiles: [...], ans: [...], why: '...' }
{ type: 'fill',  q: '...', template: 'int x ____ 5;', opts: [...], ans: 0, why: '...' }

// Java — checked by string comparison
{ type: 'type', q: '...', hint: '...', accept: ['int x = 5;'], why: '...' }

// Python — code runs via Pyodide, stdout is compared
{ type: 'type', q: '...', hint: '...', expected_output: '5', test_code: 'print(x)', why: '...' }
```

## License

MIT
