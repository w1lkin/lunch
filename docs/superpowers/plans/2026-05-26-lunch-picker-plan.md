# 中午吃什么随机小工具 — 实现计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 构建一个日系小清新风格的 H5 单页面，用户预设午餐选项，点击按钮后通过老虎机滚动动画随机选出结果。

**Architecture:** 单文件 `index.html`，HTML+CSS+JS 全部内联。两个屏幕状态（主页/结果页）通过 CSS class 切换。老虎机动画用 CSS `transform: translateY` + `transition` 驱动三列变速滚动。结果揭晓用 Canvas `requestAnimationFrame` 绘制粒子特效。数据存 localStorage。

**Tech Stack:** 纯 HTML/CSS/JS，零依赖，无构建步骤。

---

### Task 1: 创建 HTML 骨架与 CSS 样式

**Files:**
- Create: `index.html`

- [ ] **Step 1: 创建完整的 HTML 结构和 CSS 样式**

写入 `index.html`：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<title>中午吃什么</title>
<style>
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

:root {
  --green: #81C784;
  --green-light: #A5D6A7;
  --green-bg: #E8F5E9;
  --yellow: #FFF9C4;
  --yellow-light: #FFFDE7;
  --white: #FFFFFF;
  --text: #555555;
  --text-light: #999999;
  --shadow: 0 4px 16px rgba(0,0,0,0.06);
  --radius: 16px;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", sans-serif;
  background: linear-gradient(180deg, #E8F5E9 0%, #FFF9C4 50%, #FFFDE7 100%);
  min-height: 100vh;
  min-height: 100dvh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 16px;
  color: var(--text);
  -webkit-tap-highlight-color: transparent;
}

.container {
  width: 100%;
  max-width: 400px;
  background: var(--white);
  border-radius: 24px;
  padding: 32px 24px;
  box-shadow: var(--shadow);
  position: relative;
  overflow: hidden;
}

/* Header */
.header {
  text-align: center;
  margin-bottom: 24px;
}
.header-emoji { font-size: 40px; margin-bottom: 4px; }
.header-title { font-size: 20px; font-weight: 700; color: var(--green); }
.header-sub { font-size: 13px; color: var(--text-light); margin-top: 4px; }

/* Slot machine */
.slot-machine {
  display: flex;
  gap: 10px;
  justify-content: center;
  align-items: center;
  background: #FAFAFA;
  border-radius: var(--radius);
  padding: 16px 12px;
  margin-bottom: 20px;
  min-height: 140px;
  border: 1.5px dashed var(--green-light);
}
.slot-col {
  flex: 1;
  max-width: 100px;
  height: 100px;
  overflow: hidden;
  border-radius: 12px;
  background: var(--white);
  box-shadow: inset 0 2px 8px rgba(0,0,0,0.04);
  position: relative;
}
.slot-col::before,
.slot-col::after {
  content: '';
  position: absolute;
  left: 0;
  right: 0;
  height: 30px;
  z-index: 2;
  pointer-events: none;
}
.slot-col::before {
  top: 0;
  background: linear-gradient(180deg, rgba(255,255,255,0.9), transparent);
}
.slot-col::after {
  bottom: 0;
  background: linear-gradient(0deg, rgba(255,255,255,0.9), transparent);
}
.slot-strip {
  display: flex;
  flex-direction: column;
  align-items: center;
  transition: transform 0.1s ease-out;
}
.slot-item {
  height: 100px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  font-size: 14px;
  font-weight: 600;
  color: var(--text);
  gap: 4px;
  flex-shrink: 0;
}
.slot-item .emoji { font-size: 28px; }

/* Option tags */
.option-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin-bottom: 20px;
  justify-content: center;
}
.option-tag {
  background: var(--green-bg);
  color: var(--green);
  padding: 6px 14px;
  border-radius: 20px;
  font-size: 14px;
  border: 1.5px dashed var(--green-light);
  display: flex;
  align-items: center;
  gap: 4px;
  cursor: pointer;
  transition: transform 0.15s;
  user-select: none;
}
.option-tag:active { transform: scale(0.95); }
.option-tag .tag-emoji { font-size: 16px; }
.option-tag .tag-del {
  margin-left: 2px;
  font-size: 12px;
  opacity: 0.5;
  font-weight: bold;
}

/* Add option row */
.add-row {
  display: flex;
  gap: 8px;
  margin-bottom: 20px;
}
.add-row input {
  flex: 1;
  padding: 10px 14px;
  border: 1.5px dashed var(--green-light);
  border-radius: 12px;
  font-size: 14px;
  outline: none;
  background: #FAFFFA;
  color: var(--text);
}
.add-row input:focus { border-color: var(--green); }
.add-row input::placeholder { color: var(--text-light); }
.btn-add {
  background: var(--green);
  color: white;
  border: none;
  padding: 10px 18px;
  border-radius: 12px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  white-space: nowrap;
  transition: transform 0.15s;
}
.btn-add:active { transform: scale(0.95); }

/* Main roll button */
.roll-btn {
  width: 100%;
  background: linear-gradient(135deg, #81C784, #66BB6A);
  color: white;
  border: none;
  padding: 16px;
  border-radius: 18px;
  font-size: 20px;
  font-weight: 700;
  cursor: pointer;
  box-shadow: 0 4px 16px rgba(129,199,132,0.35);
  transition: transform 0.15s, box-shadow 0.15s;
}
.roll-btn:active { transform: scale(0.97); box-shadow: 0 2px 8px rgba(129,199,132,0.25); }
.roll-btn:disabled {
  opacity: 0.6;
  pointer-events: none;
}

/* Result screen */
#result-screen { display: none; }
#result-screen.active { display: flex; }
#main-screen.hidden { display: none; }

.result-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 300px;
  position: relative;
}
.result-text {
  font-size: 24px;
  font-weight: 700;
  color: var(--green);
  text-align: center;
  animation: popIn 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275) both;
}
.result-emoji {
  font-size: 64px;
  margin-bottom: 12px;
}
.result-slogan {
  font-size: 15px;
  color: var(--text-light);
  margin-top: 8px;
  text-align: center;
}

@keyframes popIn {
  from { transform: scale(0); opacity: 0; }
  to { transform: scale(1); opacity: 1; }
}

.result-actions {
  display: flex;
  gap: 12px;
  margin-top: 24px;
}
.btn-secondary {
  flex: 1;
  background: var(--green-bg);
  color: var(--green);
  border: 1.5px dashed var(--green-light);
  padding: 12px;
  border-radius: 14px;
  font-size: 15px;
  font-weight: 600;
  cursor: pointer;
  transition: transform 0.15s;
}
.btn-secondary:active { transform: scale(0.95); }

/* Canvas */
#particle-canvas {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 1;
}
.result-content > *:not(canvas) {
  position: relative;
  z-index: 2;
}

/* Edit mode modal */
.edit-overlay {
  display: none;
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.3);
  z-index: 100;
  justify-content: center;
  align-items: center;
  padding: 16px;
}
.edit-overlay.active { display: flex; }
.edit-panel {
  background: white;
  border-radius: 20px;
  padding: 24px;
  width: 100%;
  max-width: 400px;
  max-height: 80vh;
  overflow-y: auto;
}
.edit-panel h3 {
  text-align: center;
  color: var(--green);
  margin-bottom: 16px;
  font-size: 18px;
}
.edit-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 0;
  border-bottom: 1px solid #f0f0f0;
}
.edit-item:last-child { border-bottom: none; }
.edit-item input {
  flex: 1;
  padding: 8px 10px;
  border: 1.5px dashed var(--green-light);
  border-radius: 10px;
  font-size: 14px;
  outline: none;
}
.edit-item input:focus { border-color: var(--green); }
.edit-item .edit-emoji { font-size: 20px; }
.edit-del {
  color: #E57373;
  cursor: pointer;
  padding: 4px 8px;
  font-size: 18px;
}
.edit-done {
  width: 100%;
  margin-top: 16px;
  background: var(--green);
  color: white;
  border: none;
  padding: 12px;
  border-radius: 14px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
}

/* Hint text */
.hint {
  text-align: center;
  font-size: 12px;
  color: var(--text-light);
  margin-bottom: 12px;
}
</style>
</head>
<body>

<div class="container">

  <!-- Main screen -->
  <div id="main-screen">
    <div class="header">
      <div class="header-emoji">🧑‍🍳💭</div>
      <div class="header-title">中午吃啥呢</div>
      <div class="header-sub" id="header-sub">又是纠结的一天…</div>
    </div>

    <div class="slot-machine" id="slot-machine">
      <div class="slot-col" id="slot-col-0"><div class="slot-strip"></div></div>
      <div class="slot-col" id="slot-col-1"><div class="slot-strip"></div></div>
      <div class="slot-col" id="slot-col-2"><div class="slot-strip"></div></div>
    </div>

    <p class="hint">点击标签删除 · 下方添加新选项</p>

    <div class="option-tags" id="option-tags"></div>

    <div class="add-row">
      <input type="text" id="add-name" placeholder="添加选项…" maxlength="12">
      <input type="text" id="add-emoji" placeholder="🍕" maxlength="4" style="max-width:56px;text-align:center;">
      <button class="btn-add" id="btn-add">＋</button>
    </div>

    <button class="roll-btn" id="roll-btn">🎲 今天吃啥？</button>
  </div>

  <!-- Result screen -->
  <div id="result-screen">
    <div class="result-content" id="result-content">
      <canvas id="particle-canvas"></canvas>
      <div class="result-emoji" id="result-emoji"></div>
      <div class="result-text" id="result-text"></div>
      <div class="result-slogan" id="result-slogan"></div>
    </div>
    <div class="result-actions">
      <button class="btn-secondary" id="btn-retry">🔄 再抽一次</button>
      <button class="btn-secondary" id="btn-edit-result">✏️ 编辑选项</button>
    </div>
  </div>

</div>

<!-- Edit panel -->
<div class="edit-overlay" id="edit-overlay">
  <div class="edit-panel">
    <h3>✏️ 编辑选项</h3>
    <div id="edit-list"></div>
    <button class="edit-done" id="edit-done">完成</button>
  </div>
</div>

<script>
// === Data layer ===
const STORAGE_KEY = 'lunch-options';

const DEFAULTS = [
  { id: '1', name: '食堂自选', emoji: '🍱' },
  { id: '2', name: '兰州拉面', emoji: '🍜' },
  { id: '3', name: '麻辣烫', emoji: '🌶️' },
  { id: '4', name: '黄焖鸡米饭', emoji: '🍗' },
  { id: '5', name: '沙县小吃', emoji: '🥟' },
  { id: '6', name: '汉堡薯条', emoji: '🍔' },
  { id: '7', name: '螺蛳粉', emoji: '🍲' },
  { id: '8', name: '寿司', emoji: '🍣' },
  { id: '9', name: '煲仔饭', emoji: '🍚' },
  { id: '10', name: '煎饼果子', emoji: '🥞' },
  { id: '11', name: '麻辣香锅', emoji: '🥘' },
  { id: '12', name: '轻食沙拉', emoji: '🥗' },
];

const SLOGANS = [
  '命运选择了 {name} {emoji}',
  '今天轮到 {name} 了！{emoji}',
  '别再纠结了，就吃 {name} {emoji}',
  '天意如此——{name} {emoji}',
  '决定了！{name} {emoji}',
  '宇宙的信号：{name} {emoji}',
  '就是它了——{name} {emoji}',
  '你的胃说想吃 {name} {emoji}',
  '这一刻，{name} {emoji} 赢了',
  '闭眼选也是 {name} {emoji}',
];

const SUB_TITLES = [
  '又是纠结的一天…',
  '大脑已宕机，交给命运吧',
  '今天中午的哲学难题…',
  '想不动了，让它决定',
  '每天的灵魂拷问时间',
];

function loadOptions() {
  try {
    const raw = localStorage.getItem(STORAGE_KEY);
    if (raw) return JSON.parse(raw);
  } catch (e) { /* ignore */ }
  return [...DEFAULTS];
}

function saveOptions(options) {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(options));
}

function randomPick(arr) {
  return arr[Math.floor(Math.random() * arr.length)];
}

// === State ===
let options = loadOptions();
let isRolling = false;

// === DOM refs ===
const mainScreen = document.getElementById('main-screen');
const resultScreen = document.getElementById('result-screen');
const optionTags = document.getElementById('option-tags');
const slotMachine = document.getElementById('slot-machine');
const slotCols = [0, 1, 2].map(i => document.getElementById(`slot-col-${i}`));
const stripEls = slotCols.map(col => col.querySelector('.slot-strip'));
const rollBtn = document.getElementById('roll-btn');
const addName = document.getElementById('add-name');
const addEmoji = document.getElementById('add-emoji');
const btnAdd = document.getElementById('btn-add');
const headerSub = document.getElementById('header-sub');
const resultEmoji = document.getElementById('result-emoji');
const resultText = document.getElementById('result-text');
const resultSlogan = document.getElementById('result-slogan');
const resultContent = document.getElementById('result-content');
const particleCanvas = document.getElementById('particle-canvas');
const btnRetry = document.getElementById('btn-retry');
const btnEditResult = document.getElementById('btn-edit-result');
const editOverlay = document.getElementById('edit-overlay');
const editList = document.getElementById('edit-list');
const editDone = document.getElementById('edit-done');

// === Render ===
function renderTags() {
  optionTags.innerHTML = options.map(o =>
    `<span class="option-tag" data-id="${o.id}">
      <span class="tag-emoji">${o.emoji}</span>${o.name}
      <span class="tag-del">×</span>
    </span>`
  ).join('');
}

function buildSlotStrips(targetIdx) {
  // Build a repeating list so there's always content to scroll through.
  // The strip contains the options repeated enough times for animation.
  const repeats = 6;
  let html = '';
  for (let r = 0; r < repeats; r++) {
    for (const o of options) {
      html += `<div class="slot-item"><span class="emoji">${o.emoji}</span>${o.name}</div>`;
    }
  }
  stripEls.forEach(el => { el.innerHTML = html; });
}

function showMain() {
  resultScreen.classList.remove('active');
  mainScreen.classList.remove('hidden');
  headerSub.textContent = randomPick(SUB_TITLES);
  buildSlotStrips();
  renderTags();
  // Reset strip positions
  stripEls.forEach(el => { el.style.transition = 'none'; el.style.transform = 'translateY(0)'; });
}

function showResult(picked) {
  mainScreen.classList.add('hidden');
  resultScreen.classList.add('active');
  resultEmoji.textContent = picked.emoji;
  resultSlogan.textContent = randomPick(SLOGANS).replace('{name}', picked.name).replace('{emoji}', picked.emoji);
  resultText.textContent = ''; // handled by slogan
  // Trigger pop animation
  resultSlogan.style.animation = 'none';
  void resultSlogan.offsetWidth;
  resultSlogan.style.animation = 'popIn 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275) both';
  startParticles();
}

// === Slot machine animation ===
function roll() {
  if (isRolling || options.length < 2) return;
  isRolling = true;
  rollBtn.disabled = true;

  const picked = randomPick(options);
  const targetIdx = options.indexOf(picked);
  const itemHeight = 100; // matches .slot-item height
  const totalItems = options.length * 6; // repeats

  // Each column lands on the target option at a different offset
  // All columns show the same target but the travel distance differs
  // to create the staggered stop effect.
  const baseOffset = -(totalItems / 2) * itemHeight; // middle of the strip
  const targetPos = baseOffset - targetIdx * itemHeight;

  // Extra spins for visual effect (each column spins different amounts)
  const extraSpins = [3, 5, 4]; // different spins per column
  const durations = [800, 1300, 2000]; // ms per column

  stripEls.forEach((el, i) => {
    el.style.transition = 'none';
    el.style.transform = 'translateY(0)';

    // Force reflow
    void el.offsetWidth;

    const finalPos = targetPos - extraSpins[i] * options.length * itemHeight;
    el.style.transition = `transform ${durations[i]}ms cubic-bezier(0.12, 0.8, 0.2, 1)`;
    el.style.transform = `translateY(${finalPos}px)`;
  });

  // Show result after the longest column stops
  const maxDuration = Math.max(...durations);
  setTimeout(() => {
    isRolling = false;
    rollBtn.disabled = false;
    showResult(picked);
  }, maxDuration + 400);
}

// === Particle system ===
let particles = [];
let particleRaf = null;

function startParticles() {
  const rect = resultContent.getBoundingClientRect();
  const dpr = window.devicePixelRatio || 1;
  particleCanvas.width = rect.width * dpr;
  particleCanvas.height = rect.height * dpr;
  particleCanvas.style.width = rect.width + 'px';
  particleCanvas.style.height = rect.height + 'px';

  const ctx = particleCanvas.getContext('2d');
  ctx.setTransform(1, 0, 0, 1, 0, 0);
  ctx.scale(dpr, dpr);

  const cx = rect.width / 2;
  const cy = rect.height / 2;
  const colors = ['#81C784', '#A5D6A7', '#FFF9C4', '#FFE082', '#FFCC80', '#EF9A9A', '#90CAF9', '#CE93D8'];

  particles = [];
  for (let i = 0; i < 60; i++) {
    const angle = Math.random() * Math.PI * 2;
    const speed = 2 + Math.random() * 6;
    particles.push({
      x: cx, y: cy,
      vx: Math.cos(angle) * speed,
      vy: Math.sin(angle) * speed - Math.random() * 4,
      color: colors[Math.floor(Math.random() * colors.length)],
      size: 3 + Math.random() * 6,
      life: 1,
      decay: 0.008 + Math.random() * 0.02,
      shape: Math.random() > 0.5 ? 'circle' : 'star',
    });
  }

  if (particleRaf) cancelAnimationFrame(particleRaf);

  function animate() {
    ctx.clearRect(0, 0, rect.width, rect.height);
    let alive = false;

    for (const p of particles) {
      if (p.life <= 0) continue;
      alive = true;
      p.x += p.vx;
      p.y += p.vy;
      p.vy += 0.06; // gravity
      p.life -= p.decay;

      ctx.globalAlpha = Math.max(0, p.life);
      ctx.fillStyle = p.color;

      if (p.shape === 'circle') {
        ctx.beginPath();
        ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
        ctx.fill();
      } else {
        // Simple star shape
        ctx.save();
        ctx.translate(p.x, p.y);
        ctx.rotate(p.life * 5);
        const s = p.size;
        ctx.beginPath();
        for (let j = 0; j < 5; j++) {
          const a = (j * Math.PI * 2) / 5 - Math.PI / 2;
          const r = j === 0 ? s : s * 0.4;
          if (j === 0) ctx.moveTo(Math.cos(a) * s, Math.sin(a) * s);
          else ctx.lineTo(Math.cos(a) * s * 0.4, Math.sin(a) * s * 0.4);
          const a2 = a + Math.PI / 5;
          ctx.lineTo(Math.cos(a2) * s, Math.sin(a2) * s);
        }
        ctx.closePath();
        ctx.fill();
        ctx.restore();
      }
    }

    ctx.globalAlpha = 1;

    if (alive) {
      particleRaf = requestAnimationFrame(animate);
    } else {
      ctx.clearRect(0, 0, rect.width, rect.height);
    }
  }

  particleRaf = requestAnimationFrame(animate);
}

// === Event handlers ===
rollBtn.addEventListener('click', roll);

optionTags.addEventListener('click', (e) => {
  if (isRolling) return;
  const tag = e.target.closest('.option-tag');
  if (!tag) return;
  const id = tag.dataset.id;
  if (options.length <= 2) return; // minimum 2
  options = options.filter(o => o.id !== id);
  saveOptions(options);
  renderTags();
  buildSlotStrips();
});

btnAdd.addEventListener('click', () => {
  if (isRolling) return;
  const name = addName.value.trim();
  if (!name) return;
  const emoji = addEmoji.value.trim() || '🍽️';
  options.push({ id: String(Date.now()), name, emoji });
  saveOptions(options);
  renderTags();
  buildSlotStrips();
  addName.value = '';
  addEmoji.value = '';
  addName.focus();
});

addName.addEventListener('keydown', (e) => {
  if (e.key === 'Enter') btnAdd.click();
});

btnRetry.addEventListener('click', () => {
  showMain();
  // Small delay then auto-roll again
  setTimeout(() => roll(), 300);
});

btnEditResult.addEventListener('click', openEdit);

function openEdit() {
  editList.innerHTML = options.map((o, i) =>
    `<div class="edit-item">
      <span class="edit-emoji">${o.emoji}</span>
      <input type="text" value="${o.name}" data-id="${o.id}" data-field="name" maxlength="12">
      <input type="text" value="${o.emoji}" data-id="${o.id}" data-field="emoji" maxlength="4" style="max-width:56px;text-align:center;">
      <span class="edit-del" data-id="${o.id}">×</span>
    </div>`
  ).join('');
  editOverlay.classList.add('active');
}

editDone.addEventListener('click', () => {
  const items = editList.querySelectorAll('.edit-item');
  const newOptions = [];
  items.forEach(item => {
    const nameInput = item.querySelector('[data-field="name"]');
    const emojiInput = item.querySelector('[data-field="emoji"]');
    const name = nameInput.value.trim();
    if (!name) return;
    newOptions.push({
      id: nameInput.dataset.id,
      name,
      emoji: emojiInput.value.trim() || '🍽️',
    });
  });
  if (newOptions.length >= 2) {
    options = newOptions;
    saveOptions(options);
    renderTags();
    buildSlotStrips();
  }
  editOverlay.classList.remove('active');
});

editList.addEventListener('click', (e) => {
  const delBtn = e.target.closest('.edit-del');
  if (!delBtn) return;
  const id = delBtn.dataset.id;
  if (options.length <= 2) return;
  options = options.filter(o => o.id !== id);
  saveOptions(options);
  openEdit(); // refresh
});

editOverlay.addEventListener('click', (e) => {
  if (e.target === editOverlay) editOverlay.classList.remove('active');
});

// === Init ===
showMain();
</script>

</body>
</html>
```

- [ ] **Step 2: 在浏览器中打开验证**

Run: `open index.html`
Verify: 页面显示日系小清新风格，看到选项标签、添加输入框和抽签按钮。

- [ ] **Step 3: 提交**

```bash
git add index.html
git commit -m "feat: 创建中午吃什么随机小工具 — HTML 骨架、CSS 样式、完整交互逻辑"
```

---

### Task 2: 代码自查与边缘情况修复

**Files:**
- Modify: `index.html`

- [ ] **Step 1: 审查并修复已知问题**

检查以下边缘情况：

1. **选项不足 2 个时** — 点击抽签按钮不触发（已在 `roll()` 中处理），但需要视觉提示。在选项标签区上方添加提示：

在 `option-tags` div 上方已有 `.hint` 段落。确保当 `options.length < 2` 时 roll 按钮视觉上变灰。此逻辑已通过 `roll()` 中的 guard 和 `disabled` 属性处理。

2. **动画期间重复点击** — `isRolling` 标志位 + `rollBtn.disabled` 已处理。

3. **localStorage 数据损坏** — `loadOptions()` 中 try/catch 已处理，损坏时回退到默认数据。

4. **Canvas 尺寸** — `startParticles()` 中使用 `getBoundingClientRect()` 动态获取容器尺寸，适配不同屏幕。

5. **滚动穿透** — 编辑面板打开时背景不可滚动。添加 body overflow 控制：

修改 `openEdit` 和关闭编辑面板的逻辑：

编辑 `index.html`，在 `openEdit` 函数中添加：
```javascript
document.body.style.overflow = 'hidden';
```

在关闭编辑面板处（`editDone` 点击处理 和 `editOverlay` 点击处理）添加：
```javascript
document.body.style.overflow = '';
```

- [ ] **Step 2: 浏览器验证边缘情况**

- 删到只剩 1 个选项，确认无法继续删除，抽签按钮不触发
- 快速连续点击抽签按钮，确认只触发一次动画
- 打开编辑面板，确认背景不可滚动
- 清除 localStorage (`localStorage.clear()`)，刷新页面确认显示默认 12 个选项

- [ ] **Step 3: 提交**

```bash
git add index.html
git commit -m "fix: 边缘情况处理 — 滚动穿透、选项数量下限、数据损坏回退"
```

---

### Task 3: 最终验证与微调

**Files:**
- Modify: `index.html`

- [ ] **Step 1: 视觉微调**

打开页面检查实际效果，根据需要进行微调：

- 确认渐变色在移动端显示正常
- 确认老虎机滚动动画流畅度
- 确认粒子特效性能（低端设备上可能需减少粒子数到 ~40）
- 确认微信内置浏览器中打开正常

- [ ] **Step 2: 添加 favicon 和 Open Graph 标签（可选）**

在 `<head>` 中添加基础 OG 标签：

```html
<meta property="og:title" content="中午吃什么">
<meta property="og:description" content="随机决定午餐的治愈小工具">
<meta property="og:type" content="website">
```

- [ ] **Step 3: 最终提交**

```bash
git add index.html
git commit -m "polish: 视觉微调、添加 OG 标签"
```
