# Japan Driving License Wrongbook

Single-file HTML wrongbook (错题本) for the Japanese driving test: [index.html](index.html). No build, no dependencies beyond Google Fonts at runtime — open in a browser.

## Workflow: screenshot → new card

When the user sends a screenshot of a wrong-answer driving-test question, the expected response is to **append one new card** to `#cardsContainer` in `index.html`. No clarifying questions, no confirmation, no re-implementation of existing behavior.

Steps:
1. Grep for a distinctive phrase from the question (e.g. `倒退`, `安全岛`) to confirm it's not already in the file. Skip if duplicate.
2. Extract from the screenshot: question text (Chinese), correct answer (○ 正确 / × 错误), the explanation 气泡, and any sign image.
3. Append the card at the end of `#cardsContainer` using the template below.

## Card template

```html
<div class="card" data-id="<id>" data-correct="○|×" data-mastered="false">
  <div class="card-header">
    <span class="card-tag tag-<color>"><label></span>
    <div class="stars" onclick="setStars(this, event)">
      <span class="star filled">★</span><span class="star filled">★</span><span class="star">★</span>
    </div>
  </div>
  <div class="card-body">
    <!-- optional inline SVG sign: <img src="data:image/svg+xml;charset=utf-8,..." alt="..." style="width:130px;display:block;margin-bottom:10px;border-radius:6px"> -->
    <p class="question-text">原题（中文）</p>
  </div>
  <div class="card-answer wrong">
    <span class="answer-icon">❌</span>
    <span class="answer-text">正确规则的解释</span>
  </div>
  <div class="card-footer">
    <button class="mastered-btn" onclick="toggleMastered(this)">✓ 标记已掌握</button>
  </div>
</div>
```

### Attributes
- `data-id` — unique. Use the original test question number (e.g. `14`); fall back to `new<n>` if the number collides with an existing card.
- `data-correct` — the *actual* correct answer of the true/false question: `○` for 正确, `×` for 错误. Read by quiz mode.
- `data-mastered` — always `false` on creation; toggled by the 标记已掌握 button.

### Tag classes (color)
- `tag-signal` (yellow): 信号灯 · 标识 · 警察手势
- `tag-parking` (teal): 行车操作 · 速度限制 · 安全带 · 超车 · 转弯 · 隧道 · 内轮差
- `tag-pedestrian` (purple): 行人道 · 人行道 · 自行车横道 · 交叉路口 · 铁道口 · 紧急车辆 · 安全岛

### Conventions
- `class="card-answer wrong"` and icon `❌` on every card — the style marks "a wrong-question entry in the book," not the boolean answer of the prompt. Put any 正确/错误 clarification in `.answer-text`.
- Questions stay in simplified Chinese; keep Japanese sign/term names in 「 」 (e.g. 「車両通行止め」、「中央線」).
- Sign images: inline SVG data-URL, width `130–170px`. Use `%23` for `#` in hex colors inside the URL.
- Stars: three `<span class="star">`s; add `filled` class to light them up. 1★ easy, 2★ tricky, 3★ keeps tripping me up.
- Append new cards at the end of `#cardsContainer`; don't reorder existing ones.
- No memo/备注 section — removed to keep cards compact.

## Stats & quiz mode

`updateStats()` refreshes the three header counters on load and on every mastery toggle — it reads the live count of `.card` elements, so no manual count edits are needed when adding cards. `startQuiz()` shuffles cards and grades user taps against `data-correct`.

## Style tokens

CSS custom properties on `:root` at the top of `<style>`: `--red`, `--ink`, `--paper`, `--green`, etc. Fonts: Noto Sans SC + Noto Serif SC from Google Fonts.
