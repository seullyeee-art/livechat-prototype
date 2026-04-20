# 타이머 디자인 변형 (보관용)

현재 라이브챗 프로토타입에서 쓰고 있는 것은 **옵션 3 (HP바 게이지, 블루→민트 그라데이션)**. 이 문서는 검토 과정에서 만들어둔 다른 안들을 필요 시 되살릴 수 있도록 남김.

대상 요소: `#headerTimer` (채팅 인풋 바 바로 위에 위치, `.chat-sheet-input` 안의 첫 자식)

---

## 옵션 1. 진행바 + 큰 숫자 (프로그레스바)

가로 진행바가 시간 경과에 따라 줄어들고, 오른쪽에 큰 숫자. 가장 심플하고 직관적.

### HTML
```html
<div class="chat-timer-banner" id="headerTimer">
  <div class="timer-row">
    <span class="header-timer-label">다음턴까지</span>
    <span class="header-timer-val" id="headerTimerVal">30s</span>
  </div>
  <div class="timer-bar"><div class="timer-bar-fill" id="timerBarFill"></div></div>
</div>
```

### CSS
```css
.chat-timer-banner{
  display:flex;flex-direction:column;gap:4px;
  padding:6px 12px;margin:0 0 6px 0;
  border-radius:12px;
  background:rgba(0,0,0,0.55);
  backdrop-filter:blur(8px);-webkit-backdrop-filter:blur(8px);
  border:1px solid var(--line-alternative);
  color:var(--label-normal);
  pointer-events:none;
  transition:background 0.3s, border-color 0.3s;
}
.chat-timer-banner .timer-row{display:flex;justify-content:space-between;align-items:baseline}
.chat-timer-banner .timer-bar{
  height:3px;border-radius:2px;overflow:hidden;
  background:rgba(255,255,255,0.1);
}
.chat-timer-banner .timer-bar-fill{
  height:100%;width:100%;border-radius:2px;
  background:linear-gradient(90deg,#38bdf8,#2dd4bf);
  transition:width 0.2s linear, background 0.3s;
}
.header-timer-label{font-size:11px;font-weight:500;color:var(--label-neutral);letter-spacing:0.02em}
.header-timer-val{font-size:18px;font-weight:700;font-variant-numeric:tabular-nums;color:var(--label-strong);line-height:1}
.chat-timer-banner.danger{background:rgba(248,113,113,0.12);border-color:var(--status-negative);animation:timerPulse 1s ease-in-out infinite}
.chat-timer-banner.danger .header-timer-label{color:var(--status-negative);opacity:0.8}
.chat-timer-banner.danger .header-timer-val{color:var(--status-negative)}
.chat-timer-banner.danger .timer-bar-fill{background:var(--status-negative)}
@keyframes timerPulse{0%,100%{box-shadow:0 0 0 0 rgba(248,113,113,0)}50%{box-shadow:0 0 0 3px rgba(248,113,113,0.25)}}
```

### JS (startCountdown 내부)
```js
const timerEl = document.getElementById('headerTimer');
const timerVal = document.getElementById('headerTimerVal');
const timerFill = document.getElementById('timerBarFill');
timerEl.classList.remove('danger');
timerVal.textContent = '30s';
if (timerFill) timerFill.style.width = '100%';

// ... countdown interval ...
const pct = (remaining / duration) * 100;
timerVal.textContent = sec + 's';
if (timerFill) timerFill.style.width = pct + '%';
if (sec <= 10) timerEl.classList.add('danger');
else timerEl.classList.remove('danger');
```

---

## 옵션 2. 원형 링 + 내부 숫자

원형 프로그레스 링이 돌면서 지워지고 내부에 숫자. 게임 UI 느낌.

### HTML
```html
<div class="chat-timer-banner" id="headerTimer">
  <svg class="timer-ring" width="44" height="44" viewBox="0 0 44 44">
    <circle cx="22" cy="22" r="18" fill="none" stroke="rgba(255,255,255,0.12)" stroke-width="3"/>
    <circle id="timerRingFill" cx="22" cy="22" r="18" fill="none" stroke="url(#timerGrad)" stroke-width="3" stroke-linecap="round" transform="rotate(-90 22 22)"/>
    <defs>
      <linearGradient id="timerGrad" x1="0%" y1="0%" x2="100%" y2="0%">
        <stop offset="0%" stop-color="#38bdf8"/>
        <stop offset="100%" stop-color="#2dd4bf"/>
      </linearGradient>
    </defs>
  </svg>
  <div class="timer-info">
    <span class="header-timer-val" id="headerTimerVal">30</span>
    <span class="header-timer-label">다음턴까지</span>
  </div>
</div>
```

### CSS
```css
.chat-timer-banner{
  display:inline-flex;align-items:center;gap:10px;
  padding:4px 14px 4px 6px;margin:0 auto 6px auto;
  border-radius:999px;
  background:rgba(0,0,0,0.55);
  backdrop-filter:blur(8px);
  border:1px solid var(--line-alternative);
  align-self:center;
  pointer-events:none;
}
.timer-ring-fill{
  stroke-dasharray:113.1;
  stroke-dashoffset:0;
  transition:stroke-dashoffset 0.2s linear, stroke 0.3s;
}
.timer-info{display:flex;flex-direction:column;align-items:flex-start;gap:1px;line-height:1}
```

### JS (startCountdown 내부)
```js
const ringFill = document.getElementById('timerRingFill');
const RING_LEN = 113.1;  // 2πr where r=18

// on init
if (ringFill) ringFill.style.strokeDashoffset = '0';

// in countdown interval
const ratio = 1 - (remaining / duration);
if (ringFill) ringFill.style.strokeDashoffset = (RING_LEN * ratio).toFixed(2);
```

---

## 옵션 4. 인풋 테두리 앰비언트 (컬러만 변화)

인풋 바 테두리가 시간에 따라 색 변화 (파랑 → 노랑 → 빨강 펄스). 숫자는 좌상단 작은 필로.

### HTML
```html
<div class="input-bar ai-mode timer-ambient" id="inputBar">
  <span class="timer-pill" id="headerTimerVal">30s</span>
  <div class="input-row2">...</div>
</div>
```

### CSS
```css
.input-bar.timer-ambient{
  border:2px solid #38bdf8;
  box-shadow:0 0 10px rgba(56,189,248,0.35);
  overflow:visible;
  transition:border-color 0.4s, box-shadow 0.4s;
}
.input-bar.timer-ambient.warn{
  border-color:#facc15;
  box-shadow:0 0 10px rgba(250,204,21,0.4);
}
.input-bar.timer-ambient.danger{
  border-color:#ef4444;
  box-shadow:0 0 12px rgba(239,68,68,0.55);
  animation:ambPulse 0.7s ease-in-out infinite;
}
@keyframes ambPulse{0%,100%{box-shadow:0 0 8px rgba(239,68,68,0.4)}50%{box-shadow:0 0 16px rgba(239,68,68,0.75)}}
.input-bar .timer-pill{
  position:absolute;top:-10px;left:12px;z-index:2;
  padding:2px 8px;border-radius:10px;
  background:rgba(0,0,0,0.85);
  border:1px solid var(--line-alternative);
  font-size:11px;font-weight:700;font-variant-numeric:tabular-nums;
  color:var(--label-strong);line-height:1.3;
  pointer-events:none;
}
```

### JS (startCountdown 내부)
```js
const inputBar = document.getElementById('inputBar');
const timerVal = document.getElementById('headerTimerVal');

// on init
inputBar.classList.remove('danger','warn');

// in countdown
timerVal.textContent = sec + 's';
if (sec <= 10) { inputBar.classList.add('danger'); inputBar.classList.remove('warn'); }
else if (sec <= 20) { inputBar.classList.add('warn'); inputBar.classList.remove('danger'); }
else inputBar.classList.remove('warn','danger');
```

---

## 공통 동작

- `sec <= 10`이면 `.danger` 클래스 추가 (빨강 + 펄스)
- `sec <= 20`이면 `.warn` 클래스 추가 (노랑) — HP바, 앰비언트에서만 사용
- 대사 중(`dlgMode=true`)일 땐 `.streaming` 클래스 추가 → 게이지 숨기고 "캐릭터 응답중" 레이블만 표시
- 유저 채팅 모드일 땐 `.hide-by-mode` 클래스 추가 → 완전히 숨김
- 원본 데이터 mutation 없음, 매 턴 초기화

## 현재 상태
2026-04-20 기준 옵션 3 (HP바 세그먼트) 선택. 파일: `livechat-prototype.html`
