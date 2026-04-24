# Japan Driving License Wrongbook

Static HTML wrongbook (错题本) for the Japanese driving license test (taken in Chinese). No build, no dependencies beyond Google Fonts. Open [index.html](index.html) in a browser.

Files:
- [index.html](index.html) — layout, styles, quiz/filter logic
- [cards.js](cards.js) — wrong-question data + `renderCards()` / `createCardElement()`

## Workflow: screenshot → new card

When the user sends a screenshot of a wrong-answer driving-test question, **append one object to the `cards` array in [cards.js](cards.js)** — no clarifying questions, no confirmation.

Steps:
1. Grep [cards.js](cards.js) for a distinctive phrase from the question (e.g. `倒退`, `安全岛`). Skip if duplicate.
2. Extract from the screenshot: question text, correct answer (○/×), explanation, and sign (optional).
3. Append a card object at the end of the `cards` array. Template below.

## Card object

```js
{
  correct: "○" | "×",        // the ACTUAL correct answer of the true/false prompt
  tag: "<label>",           // e.g. "信号灯", "行车操作"
  tagClass: "tag-signal" | "tag-parking" | "tag-pedestrian",
  stars: 0 | 1 | 2 | 3,      // difficulty — how often it trips me up
  question: "原题（中文）",
  answer: "正确规则的解释",
  sign: "data:image/svg+xml;charset=utf-8,%3Csvg ...%3E%3C/svg%3E"  // optional
}
```

### Rules

- **`correct`** drives both quiz grading and the displayed icon (`createCardElement` maps `○ → ✅` and `× → ❌`). Keep it accurate.
- **Reading the screenshot's answer:**
  - ✅ 正确 → `correct: "○"`. ❌ 错误 → `correct: "×"`.
  - Sanity-check against the explanation: "如题所述" almost always means the answer is 正确; explanations that add a rule the statement ignored (e.g. "也…", "即使…") usually mean the answer is 错误.
- **Tag classes (color):**
  - `tag-signal` (yellow): 信号灯 · 标识 · 警察手势 · 手势信号
  - `tag-parking` (teal): 行车操作 · 速度限制 · 安全带 · 超车 · 转弯 · 隧道 · 内轮差 · 驾照
  - `tag-pedestrian` (purple): 行人道 · 人行道 · 自行车横道 · 交叉路口 · 铁道口 · 紧急车辆 · 安全岛
- **Questions** are natively simplified Chinese (the test has a Chinese option — do *not* describe this as translation). Keep Japanese sign names in 「 」 (e.g. 「車両通行止め」、「中央線」).
- **Signs** are inline SVG data-URLs; use `%23` for `#` in hex colors. Rendered at `width:130px`.
- **Append** to the end of `cards`; don't reorder existing entries.

## Rendering & stats

`renderCards()` (in cards.js) builds card DOM from `cards` and appends to `#cardsContainer`. It's called once on DOMContentLoaded, before `updateStats()`. `updateStats()` reads the live count of `.card` elements — no counter tweaks needed when adding cards. `startQuiz()` shuffles cards and grades against `data-correct`.

## Style tokens

CSS custom properties on `:root` at top of `<style>` in index.html: `--red`, `--ink`, `--paper`, `--green`, etc. Fonts: Noto Sans SC + Noto Serif SC.
