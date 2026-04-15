# Mirai 라이브 챗 — 개발 스펙 문서

> **프로토타입 URL**: [Vercel Preview](https://partychat-prototype-90kwpgrep-seullyeee-1827s-projects.vercel.app)
> **프로토타입 파일**: `livechat-prototype.html` (단일 HTML, 참고용)
> **작성일**: 2026-04-03

---

## 목차

1. [서비스 개요](#1-서비스-개요)
2. [디자인 토큰](#2-디자인-토큰)
3. [레이아웃 구조](#3-레이아웃-구조)
4. [헤더](#4-헤더)
5. [캐릭터 배경 영역](#5-캐릭터-배경-영역)
6. [채팅 피드](#6-채팅-피드)
7. [대사 모드 (비주얼 노벨)](#7-대사-모드-비주얼-노벨)
8. [입력바](#8-입력바)
9. [카운트다운 & 턴 시스템](#9-카운트다운--턴-시스템)
10. [슬롯 룰렛 (추첨)](#10-슬롯-룰렛-추첨)
11. [후원 시스템](#11-후원-시스템)
12. [랭킹 시스템](#12-랭킹-시스템)
13. [미르 충전 시스템](#13-미르-충전-시스템)
14. [이펙트 & 애니메이션](#14-이펙트--애니메이션)
15. [방송 종료 화면](#15-방송-종료-화면)
16. [반응형 (모바일/데스크톱)](#16-반응형-모바일데스크톱)
17. [에셋 목록](#17-에셋-목록)
18. [상태 관리](#18-상태-관리)
19. [데이터 구조](#19-데이터-구조)

---

## 1. 서비스 개요

AI 캐릭터(셀린)와 실시간으로 소통하는 라이브 채팅 서비스.
시청자들이 채팅/후원/AI 메시지를 보내면, **30초 턴제**로 메시지를 추첨하여 AI 캐릭터가 비주얼 노벨 스타일로 반응하는 구조.

### 핵심 루프
```
[30초 카운트다운] → [유저 채팅/후원/AI 메시지 전송]
→ [턴 종료] → [슬롯 룰렛 추첨 (AI 메시지 중)]
→ [당첨 메시지로 대사 모드 진입 (VN 스타일)]
→ [대사 완료] → [다음 턴 시작]
```

### 재화
- **미르(💎)**: 인앱 재화. AI 메시지 전송, 후원 등에 사용
- 초기 잔액: **2,480 미르**
- 1 미르 = 1원 (충전 시)

---

## 2. 디자인 토큰

### 2-1. 색상

| 변수명 | 값 | 용도 |
|--------|-----|------|
| `--primary-normal` | `#3CC8A8` | 메인 민트 (AI 모드, 버튼, 강조) |
| `--primary-strong` | `#2DB897` | 민트 강조 (그라데이션 끝) |
| `--primary-heavy` | `#1FA886` | 민트 최강조 |
| `--label-normal` | `rgba(226,232,240,0.99)` | 기본 텍스트 |
| `--label-strong` | `#fff` | 강조 텍스트 |
| `--label-neutral` | `rgba(186,196,210,0.88)` | 보조 텍스트 |
| `--label-alternative` | `rgba(148,163,184,0.61)` | 약한 텍스트 |
| `--label-assistive` | `rgba(148,163,184,0.28)` | 플레이스홀더 |
| `--label-disable` | `rgba(113,128,150,0.16)` | 비활성 텍스트 |
| `--bg-normal` | `rgba(20,25,35,1)` | 기본 배경 |
| `--bg-alternative` | `rgba(13,16,23,1)` | 대체 배경 (모바일 스크린) |
| `--bg-elevated` | `rgba(18,18,24,1)` | 상승 배경 |
| `--bg-elevated-alt` | `rgba(14,14,18,1)` | 상승 배경 대체 |
| `--interaction-inactive` | `rgba(113,128,150,0.40)` | 비활성 인터랙션 |
| `--interaction-disable` | `rgba(113,128,150,0.22)` | 비활성 |
| `--line-normal` | `rgba(148,163,184,0.32)` | 기본 선 |
| `--line-neutral` | `rgba(148,163,184,0.28)` | 보조 선 |
| `--line-alternative` | `rgba(148,163,184,0.22)` | 약한 선 |
| `--status-positive` | `#34D399` | 성공/긍정 |
| `--status-cautionary` | `#FB923C` | 경고/주의 |
| `--status-negative` | `#F87171` | 에러/위험 |
| `--fill-normal` | `rgba(148,163,184,0.22)` | 기본 채움 |
| `--fill-strong` | `rgba(148,163,184,0.28)` | 강조 채움 |
| `--fill-alternative` | `rgba(148,163,184,0.12)` | 약한 채움 |
| `--material-dimmer` | `rgba(0,0,0,0.78)` | 딤 배경 |
| `--accent-gold` | `#e8c87a` | 후원 골드 |
| `--accent-gold-strong` | `#c9a04a` | 후원 골드 강조 |

### 2-2. 그림자

| 변수명 | 값 |
|--------|-----|
| `--shadow-normal` | `0 1px 2px rgba(0,0,0,0.12), 0 0px 1px rgba(0,0,0,0.08)` |
| `--shadow-emphasize` | `0 2px 8px rgba(0,0,0,0.12), 0 1px 4px rgba(0,0,0,0.08)` |
| `--shadow-strong` | `0 6px 12px rgba(0,0,0,0.12), 0 4px 8px rgba(0,0,0,0.08)` |

### 2-3. 폰트

```
font-family: 'Pretendard JP', 'Pretendard Variable', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
```
CDN: `https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/variable/pretendardvariable-jp.min.css`

---

## 3. 레이아웃 구조

### 3-1. HTML 구조도

```
<html> (background: #000)
└─ <body> (background: transparent, flex center)
   └─ .screen (전체 컨테이너)
      ├─ .char-area (캐릭터 배경)
      │  ├─ .char-img (메인 캐릭터 이미지)
      │  └─ .char-img-overlay (컷신 이미지, crossfade)
      ├─ .top-gradient (상단 그라데이션 오버레이)
      ├─ .bottom-gradient (하단 그라데이션 오버레이)
      ├─ .header (상단바)
      ├─ .winner-popup (당첨 팝업)
      ├─ .end-screen (종료 화면)
      ├─ .slot-overlay (슬롯 룰렛 오버레이)
      ├─ .donation-alert (후원 알림 팝업)
      └─ .bottom-panel (하단 패널)
         ├─ .countdown-wrap (카운트다운 바)
         ├─ .chat-feed (채팅 피드) ← 또는 .dlg-area (대사 모드)
         ├─ .ai-response (AI 응답 영역)
         ├─ .input-bar (입력바)
         └─ .bottom-spacer (safe area)
```

### 3-2. 스크린 사이즈

| 환경 | 너비 | 높이 |
|------|------|------|
| 모바일 | `max-width: 430px` | `100dvh` |
| 데스크톱 | `100%` (전체 뷰포트) | `100vh` |

---

## 4. 헤더

### 4-1. 구조
```
.header (position: absolute, top: 0, z-index: 50)
├─ .header-left
│  ├─ .icon-btn (뒤로가기 "‹")
│  └─ .profile-pill (캐릭터 아바타 + 이름)
│     ├─ .avatar (24×24, 원형)
│     └─ .char-name ("셀린")
└─ .header-right
   ├─ .header-stat (시청자 수: 👁 + 숫자)
   ├─ .icon-btn (랭킹: crown.svg 아이콘)
   └─ .icon-btn (종료: "종료" 텍스트, 빨간색)
```

### 4-2. 상단 그라데이션
```css
.top-gradient {
  height: 200px;
  z-index: 40;
  pointer-events: none;
  background: linear-gradient(180deg,
    rgba(0,0,0,0.7) 0%,
    rgba(0,0,0,0.45) 35%,
    rgba(0,0,0,0.2) 65%,
    transparent 100%
  );
}
```

### 4-3. 스타일 상세

| 요소 | 스타일 |
|------|--------|
| `.icon-btn` | 32×32px, border-radius: 16px, background: rgba(0,0,0,0.2) |
| `.profile-pill` | height: 32px, padding: 4px 8px 4px 4px, border-radius: 16px, background: rgba(0,0,0,0.2) |
| `.char-name` | font-size: 12px, color: --label-strong |
| `.header-stat` | height: 32px, padding: 0 10px, border-radius: 16px, background: rgba(0,0,0,0.25) |
| `.stat-val` | font-weight: 600, color: --label-normal |

### 4-4. 시청자 수 변동
- 4초마다 `rand(-3, +5)` 변동
- 최소값: 1,100
- 형식: 천 단위 콤마

---

## 5. 캐릭터 배경 영역

### 5-1. 구조
```
.char-area (position: absolute, inset: 0)
├─ .char-img (z-index: 1, 메인 이미지)
└─ .char-img-overlay (z-index: 2, 컷신 이미지, 기본 숨김)
```

### 5-2. 모바일 스타일
```css
.char-area {
  background: radial-gradient(ellipse 60% 50% at 15% 30%, rgba(180,40,30,0.2) 0%, transparent 70%),
              radial-gradient(ellipse 70% 60% at 70% 40%, rgba(20,50,100,0.35) 0%, transparent 70%),
              linear-gradient(170deg, #1a1015 0%, #0d1520 40%, var(--bg-alternative) 100%);
}
.char-img {
  background-image: url('./images/char-full.webp');
  background-size: 100% auto;  /* 가로 100% 맞춤 */
  background-position: center 15%;
  background-color: #0c0f15;
}
.char-img-overlay {
  background-size: cover;
  background-position: center 30%;
  opacity: 0;
  transition: opacity 1.2s ease-in-out;
}
```

### 5-3. 데스크톱 스타일 (431px 이상)
```css
.char-area { background: transparent; }
.char-img, .char-img-overlay {
  background-size: auto 100%;  /* 세로 100% 맞춤 */
  background-position: center center;
  background-color: transparent;
}
```

### 5-4. 컷신 전환
- 대사 모드 진입 시 `.char-img-overlay`에 `.show` 클래스 추가
- `opacity: 0 → 1` (1.2s ease-in-out crossfade)
- 대사 모드 종료 후에도 배경은 유지됨 (돌아가지 않음)

### 5-5. 하단 그라데이션
```css
.bottom-gradient {
  height: 520px;
  z-index: 5;
  background: linear-gradient(0deg,
    rgba(0,0,0,0.85) 0%,
    rgba(0,0,0,0.75) 20%,
    rgba(0,0,0,0.55) 40%,
    rgba(0,0,0,0.30) 60%,
    rgba(0,0,0,0.08) 85%,
    transparent 100%
  );
}
```

---

## 6. 채팅 피드

### 6-1. 컨테이너 스타일
```css
.chat-feed {
  display: flex;
  flex-direction: column;
  gap: 4px;
  padding-bottom: 6px;
  max-height: 260px;
  overflow: hidden;
  pointer-events: none;  /* 스크롤 불가 */
}
```

### 6-2. 메시지 타입 (5종)

#### (A) 무료 채팅 (type-free)
| 속성 | 값 |
|------|-----|
| 배경 | `rgba(0,0,0,0.55)` |
| 닉네임 색 | `--label-neutral` |
| 텍스트 색 | `--label-alternative` |
| 아바타 | 18×18px |
| 비용 | 0 미르 |
| 블라인드 | 10% 확률 자동 블라인드 처리 |

#### (B) AI에게 보내기 (type-ai)
| 속성 | 값 |
|------|-----|
| 배경 | `linear-gradient(135deg, rgba(0,0,0,0.55), rgba(60,200,168,0.06))` |
| 닉네임 색 | `--primary-normal` (민트) |
| 텍스트 색 | `--label-normal` |
| 아바타 | 20×20px |
| 비용 | **10 미르** |
| 뱃지 | "AI에게" (민트 배경) |

#### (C) AI 부스터 (type-ai-boost)
| 속성 | 값 |
|------|-----|
| 배경 | `linear-gradient(135deg, rgba(0,0,0,0.55), rgba(60,200,168,0.10))` |
| 좌측 바 | `box-shadow: inset 3px 0 0 var(--primary-normal)` (민트 3px) |
| 닉네임 | `--primary-normal`, font-weight: 700 |
| 아바타 | 20×20px |
| 비용 | **15 미르** |
| 뱃지 | "BOOST" (민트 그라데이션 배경, 검정 텍스트) |
| 추첨 가중치 | **3배** |

#### (D) 후원 메시지 (type-donate)
| 속성 | 값 |
|------|-----|
| 배경 | `linear-gradient(135deg, rgba(232,200,122,0.12), rgba(201,160,74,0.06))` |
| 좌측 바 | `box-shadow: inset 3px 0 0 var(--accent-gold)` (골드 3px) |
| 닉네임 | `--accent-gold`, 14px, font-weight: 700 |
| 텍스트 | `--label-strong`, 14px, font-weight: 500 |
| 아바타 | 22×22px |
| 뱃지 | "후원" (골드 배경) |
| 애니메이션 | `goldGlow` — 2s 무한 글로우 펄스 |

#### (E) 제작자 메시지 (type-creator)
| 속성 | 값 |
|------|-----|
| 배경 | `linear-gradient(135deg, rgba(168,85,247,0.15), rgba(236,72,153,0.08))` |
| 좌측 바 | `box-shadow: inset 3px 0 0 #a855f7` (퍼플 3px) |
| 닉네임 | `#c084fc`, 14px, font-weight: 700, text-shadow |
| 텍스트 | `#f0abfc`, 14px, font-weight: 500 |
| 아바타 | 22×22px, border: 1.5px solid #a855f7, box-shadow |
| 뱃지 | "제작자" (퍼플→핑크 그라데이션) |
| 애니메이션 | `creatorGlow` 2.5s + `creatorShine` 3s (빛 스윕 효과) |

### 6-3. 공통 메시지 스타일
```css
.chat-msg {
  display: inline-flex;
  align-items: center;
  padding: 5px 10px;
  border-radius: 20px;
  max-width: 100%;
  align-self: flex-start;
  gap: 4px;
  animation: msgIn 0.3s ease-out;
  transition: opacity 0.5s, transform 0.5s, box-shadow 0.3s, background 0.3s;
}
```

### 6-4. 뱃지 종류

| 뱃지 | 클래스 | 스타일 |
|------|--------|--------|
| ME | `.msg-me` | 민트 배경 20%, 민트 텍스트, 9px 700 |
| AI에게 | `.badge-ai` | 민트 12% 배경, 민트 텍스트 |
| BOOST | `.badge-boost` | 민트 그라데이션, 검정 텍스트, 글로우 |
| 후원 | `.badge-donate` | 골드 15% 배경, 골드 텍스트 |
| 제작자 | `.badge-creator` | 퍼플→핑크 그라데이션, 흰색 텍스트, 펄스 |
| BLIND | `.blind-badge` | 빨간 12% 배경, 빨간 텍스트, 8px |

### 6-5. 랭킹 크라운
- 후원 누적 Top 3 유저의 닉네임 옆에 크라운 이모지 표시
- 1등: 👑, 2등: 🥈, 3등: 🥉
- 실시간 업데이트

### 6-6. 메시지 관리
- **최대 표시 수**: 25개
- 초과 시 가장 오래된 메시지부터 `fade-out` (opacity: 0, translateY: -10px, 0.5s)
- 새 메시지 추가 시 자동 스크롤 (scrollTop = scrollHeight)

### 6-7. 블라인드 처리
- 무료 채팅의 10% 확률로 자동 블라인드
- 블라인드된 메시지: opacity 0.4, 텍스트 "부적절한 채팅이 감지되어 블라인드 처리되었습니다."
- 후원/AI/제작자 메시지는 블라인드 제외

### 6-8. 더미 메시지 자동 생성
- **간격**: 600~1800ms 랜덤
- **비율**: 무료 채팅 75% / AI 메시지 20% (그 중 30%가 부스터) / 제작자 5%

---

## 7. 대사 모드 (비주얼 노벨)

### 7-1. 진입 조건
- 슬롯 룰렛 추첨 완료 후 자동 진입

### 7-2. 진입 트랜지션
1. 채팅 피드 + 카운트다운 → opacity 0 (0.4s)
2. 채팅 피드 숨김, 대사 영역 표시 → opacity 1 (0.4s)
3. 캐릭터 배경 crossfade (char-img-overlay .show)
4. 전송 버튼 비활성화 (채팅은 입력 가능, 전송만 차단)

### 7-3. 대사 영역 구조
```
.dlg-area (min-height: 260px, flex-end 정렬)
├─ .dlg-name-row (캐릭터/유저 이름)
│  └─ .dlg-name-text (17px, font-weight: 600)
├─ .dlg-wrap (min-height: 180px, max-height: 220px, 스크롤 가능)
│  ├─ .dlg-text (대사 텍스트, 타이핑 애니메이션)
│  └─ .dlg-loading-area (로딩 표시)
│     ├─ .dlg-loading-avatar (32×32, 골드 테두리)
│     ├─ .dlg-loading-dots (바운싱 3개 점)
│     └─ .dlg-loading-text ("셀린이 답변을 생각하고 있어요...")
└─ .dlg-skip-row
   ├─ .dlg-progress ("1/3" 등)
   └─ .dlg-skip-btn ("건너뛰기 ▶")
```

### 7-4. 텍스트 스타일

| 타입 | 색상 | 용도 |
|------|------|------|
| `.is-dialogue` | `#e5dba0` (따뜻한 금빛) | 캐릭터 대사 |
| `.is-narration` | `--label-normal` | 나레이션/설명 |
| `.is-user` | `#e5dba0` | 유저 메시지 (대사 스타일) |

### 7-5. 대사 텍스트 공통 스타일
```css
.dlg-text {
  font-size: 15px;
  font-weight: 400;
  line-height: 1.65;
  letter-spacing: 0.1px;
  text-shadow: 0 0 4px rgba(0,0,0,0.8);
}
```

### 7-6. 로딩 텍스트 스타일
```css
.dlg-loading-text {
  font-size: 15px;      /* 대사 텍스트와 동일 */
  font-weight: 400;
  line-height: 1.65;
  color: var(--label-alternative);
  letter-spacing: 0.1px;
  text-shadow: 0 0 4px rgba(0,0,0,0.8);
}
```

### 7-7. 시퀀스 흐름
```
1. [유저 당첨 메시지 표시] (타이핑 애니메이션 → 1.5초 대기)
2. [로딩 표시] ("셀린이 답변을 생각하고 있어요..." + 바운싱 dots, 2.5초)
3. [AI 대사 턴 1~N] (각 턴: 타이핑 → 대기 2.5~3초)
   - dialogue 턴: 캐릭터 이름 + 대사 (is-dialogue)
   - narration 턴: 이름 숨김 + 나레이션 (is-narration)
4. [1초 대기 후 자동 종료 → 채팅 모드 복귀]
```

### 7-8. 타이핑 애니메이션
- 속도: **35ms/글자**
- 커서: 2px 너비, 14px 높이, 골드 색상, 0.7s 깜빡임
- 줄바꿈 지원 (`\n` → 별도 `<div class="ln">`)

### 7-9. 대사 세트 (예시 8세트)
- 각 세트: 2~3턴 (dialogue + narration 혼합)
- RPG 판타지 컨텍스트 (마법, 모험, 정령, 마왕 등)
- 랜덤으로 1세트 선택

### 7-10. 건너뛰기
- "건너뛰기 ▶" 버튼 → 즉시 대사 모드 종료, 채팅 모드 복귀

---

## 8. 입력바

### 8-1. 전체 구조
```
.input-bar (border-radius: 16px)
├─ .mode-toggle (모드 전환)
│  ├─ .mode-toggle-left
│  │  ├─ [💬 채팅] 버튼 (modeFreeBtn)
│  │  └─ [✨ AI에게] 버튼 (modeAiBtn)
│  └─ .balance-display (💎 잔액 + 충전 링크)
├─ .input-top
│  ├─ textarea.input-field (입력 필드)
│  └─ .char-count (글자수 카운터)
├─ .tts-row (TTS 설정, 100미르 이상 후원 시 표시)
│  ├─ 🔊 읽어주기 + 토글 스위치
│  └─ 보이스 선택 (셀린 / 🖤 다크셀린)
└─ .input-bottom
   ├─ .input-left
   │  ├─ .chat-amount-chips (채팅 모드: 금액 칩)
   │  └─ .ai-mode-extras (AI 모드: 부스터 + 비용)
   └─ .input-right
      └─ .send-btn (전송 버튼)
```

### 8-2. 입력바 기본 스타일
```css
.input-bar {
  margin-top: 6px;
  border-radius: 16px;
  background: rgba(0,0,0,0.35);
  backdrop-filter: blur(8px);
  border: 1px solid var(--line-alternative);
  overflow: hidden;
  transition: border-color 0.3s, box-shadow 0.3s;
}
```

### 8-3. 입력 모드 (2가지)

#### 채팅 모드 (기본)
- placeholder: "채팅을 입력하세요..."
- maxLength: 50자
- 금액 칩 표시: 무료 / 10 / 100 / 500 / 1K / 직접입력
- 후원 금액 선택 시: `donate-mode` 클래스 추가

#### AI 모드
- placeholder: "AI에게 보낼 메시지..."
- maxLength: 200자
- 부스터 버튼 + 비용 표시
- `ai-mode` 클래스 추가
- 전송 후 자동으로 채팅 모드로 복귀

### 8-4. 모드별 입력바 강조

| 모드 | border-color | box-shadow (outer glow) |
|------|-------------|------------------------|
| 기본 | `var(--line-alternative)` | 없음 |
| AI 모드 | `rgba(60,200,168,0.3)` | `0 0 12px rgba(60,200,168,0.15)` |
| 후원 모드 | `rgba(232,200,122,0.3)` | `0 0 12px rgba(232,200,122,0.15)` |

### 8-5. 금액 칩 (채팅 모드)

| 칩 | data-amt | 동작 |
|----|----------|------|
| 무료 | 0 | 기본 선택. 무료 채팅 |
| 10 | 10 | 10미르 후원 |
| 100 | 100 | 100미르 후원 + TTS 토글 표시 |
| 500 | 500 | 500미르 후원 + TTS 토글 표시 |
| 1K | 1000 | 1000미르 후원 + TTS + 다크 보이스 활성화 |
| 직접입력 | custom | 모달 팝업으로 금액 입력 |

#### 칩 선택 스타일
```css
/* 기본 선택 (AI모드) */
.amt-chip.selected {
  background: rgba(60,200,168,0.15);
  color: var(--primary-normal);
}
/* 후원 선택 (골드) */
.amt-chip.selected.is-donate {
  background: rgba(232,200,122,0.15);
  color: var(--accent-gold);
}
```

### 8-6. 부스터 (AI 모드)
- 비용: 기본 10미르, 부스터 활성 시 15미르
- 부스터 활성 시: 골드 테두리 + 배경
- 추첨 가중치 3배

### 8-7. TTS 설정
- **표시 조건**: 100미르 이상 후원 선택 시
- **보이스 종류**:
  - "셀린" (기본) — 항상 표시
  - "🖤 다크셀린" (프리미엄) — 1000미르 이상 시 표시 + 자동 선택
- **프리미엄 보이스 스타일**: 퍼플 계열 (#a855f7 테두리, #c084fc 텍스트)

### 8-8. 전송 버튼
```css
.send-btn {
  width: 28px; height: 28px;
  border-radius: 14px;
  background: linear-gradient(105deg, var(--primary-normal), var(--primary-strong));
}
/* 후원 모드일 때 */
.input-bar.donate-mode .send-btn {
  background: linear-gradient(105deg, var(--accent-gold), var(--accent-gold-strong));
}
```

### 8-9. 직접입력 모달
```
.custom-amt-popup (fixed, 전체 화면 딤 배경)
└─ .custom-amt-inner (260px, 다크 배경, rounded-16)
   ├─ "후원 금액 입력" 타이틀
   ├─ number input (text-align: center, 20px bold)
   └─ [취소] [확인] 버튼
```

### 8-10. 글자수 카운터
- AI 모드: 항상 표시 (`n/200`)
- 채팅 모드: 입력 시만 표시 (`n/50`)
- 80% 도달: `--status-cautionary` (주황)
- 100% 도달: `--status-negative` (빨강)

### 8-11. 잔액 부족 표시
- 필요 미르 > 보유 미르 → 잔액 숫자 색상 `#f87171` (빨강)
- 전송 시도 시: 상단 토스트 "💎 잔액이 부족합니다"

---

## 9. 카운트다운 & 턴 시스템

### 9-1. 카운트다운 바 구조
```
.countdown-wrap
└─ .countdown-bar-outer (flex, gap: 8px)
   ├─ .countdown-label ("Turn 1")
   ├─ .countdown-track (3px 높이 트랙)
   │  └─ .countdown-fill (프로그레스 바)
   └─ .countdown-seconds ("⏳ 30s")
```

### 9-2. 동작
- 30초 카운트다운
- 50ms 간격으로 width % 업데이트 (부드러운 감소)
- **3초 이하**: `.danger` 클래스 → 바 색상 `--status-negative`, 숫자 빨강
- 0초 → 슬롯 룰렛 트리거

### 9-3. 모래시계 애니메이션
```css
.hourglass {
  animation: hourglassFlip 1.5s ease-in-out infinite;
}
@keyframes hourglassFlip {
  0% { transform: rotateZ(0deg) }
  25% { transform: rotateZ(180deg) }
  50% { transform: rotateZ(180deg) }
  75% { transform: rotateZ(360deg) }
  100% { transform: rotateZ(360deg) }
}
```

---

## 10. 슬롯 룰렛 (추첨)

### 10-1. 진입 조건
- 카운트다운 종료 시 자동 실행
- 해당 턴의 AI 메시지(type-ai, type-ai-boost)가 1개 이상 있어야 함
- 0개면 1초 대기 후 다음 턴 시작

### 10-2. 추첨 가중치
| 메시지 타입 | 가중치 |
|------------|--------|
| AI (일반) | 1배 |
| AI (부스터) | **3배** |

### 10-3. 슬롯 머신 구조
```
.slot-overlay (전체 화면 딤, z-index: 200)
├─ .slot-title ("추 첨 중", 6px letter-spacing, 펄스 애니메이션)
└─ .slot-machine (240×80px, rounded-16, 다크 배경)
   ├─ .slot-strip (세로 스크롤되는 아이템 목록)
   │  └─ .slot-item × 30 (각 80px 높이)
   │     ├─ .slot-avatar (44×44, 원형)
   │     └─ .slot-info (이름 + 메시지)
   ├─ .slot-indicator (중앙 선택 라인, 민트 테두리)
   ├─ ::before / ::after (상하 페이드 마스크)
   └─ .slot-winner-reveal (당첨 확정 후 리빌)
```

### 10-4. 애니메이션
- **스핀 시간**: 3.5초
- **이징**: cubic ease-out (`1 - Math.pow(1 - t, 3)`)
- **동작**: 빠르게 시작 → 감속 → 정지
- **당첨 위치**: 인덱스 25 (30개 중)
- **당첨 확정 시**:
  - `.winner-flash` — 골드 테두리 + 강한 글로우
  - `slotWinFlash` 바운스 (scale 1→1.05→0.98→1)
  - 24개 파티클 스파크 (민트 + 골드 랜덤)
- **400ms 딜레이 후 스핀 시작**
- **1200ms 후 오버레이 닫고 → 400ms 후 대사 모드 진입**

---

## 11. 후원 시스템

### 11-1. 후원 방법
채팅 입력바에서 금액 칩 선택 → 메시지 입력 → 전송

### 11-2. 금액별 동작

| 금액 | 채팅 표시 | 팝업 알림 | GIF | 이펙트 |
|------|----------|----------|-----|--------|
| 1~10 | 무료 채팅과 동일 (type-free) | ❌ | ❌ | ❌ |
| 11~49 | 후원 하이라이트 (type-donate) | ❌ | ❌ | ❌ |
| 50+ | 후원 하이라이트 | ✅ 팝업 알림 | ✅ 랜덤 GIF | ✅ |
| 100+ | 후원 하이라이트 | ✅ 팝업 알림 | ✅ 랜덤 GIF | ✅ + TTS 가능 |
| 1000+ | 후원 하이라이트 | ✅ 팝업 알림 | ✅ 랜덤 GIF | ✅ + 프리미엄 TTS |

### 11-3. 후원 알림 팝업 (Chzzk 스타일)
```
.donation-alert (position: absolute, 화면 상단 35%, 중앙 정렬)
├─ .da-gif (160×160, 랜덤 GIF)
├─ .da-donor ("{이름}님이 💎 {금액} 후원!")
│  ├─ .da-name (주황색, font-weight: 800)
│  └─ .da-amount-num (흰색, font-weight: 800)
└─ .da-message (메시지 내용)
```

- **표시 시간**: 3.5초
- **등장 애니메이션**: `alertBounce` (0.5s, scale 0.5→1.05→1, translateY 20→-4→0)
- **큐 시스템**: 연속 후원 시 순차 표시
- **추첨 중에는 대기**: 슬롯 오버레이가 열려있으면 500ms 간격으로 재시도

### 11-4. 후원 이펙트
#### 화면 플래시
```css
.screen-flash {
  background: radial-gradient(ellipse at center,
    rgba(60,200,168,0.3),
    rgba(232,200,122,0.15),
    transparent 70%
  );
  animation: flashBang 0.6s ease-out forwards;
}
```

#### 플로팅 이모지 파티클
- **이모지**: 💎, ✨, ⭐, 💫, 🌟 (랜덤)
- **개수**: 100미르+ → 20개 / 10미르+ → 10개 / 그 외 → 5개
- **애니메이션**: `giftFloat` 2.8s (아래→위, scale 변화, 약간 회전)
- **지속**: 3.5초 후 제거

### 11-5. 더미 후원 자동 생성
- **간격**: 8~18초 랜덤
- **금액 분포**: 10미르 40% / 50미르 30% / 100미르 20% / 300미르 10%
- 대사 모드 중에는 생성 안 함

### 11-6. 잭팟 시스템
- 모든 후원/AI 메시지 금액의 10%가 잭팟에 적립
- (현재 프로토타입에서 잭팟 사용처는 미구현)

---

## 12. 랭킹 시스템

### 12-1. 랭킹 시트 (바텀 시트)
```
.ranking-sheet (바텀 시트, max-height: 75vh)
├─ .rs-handle (드래그 핸들)
├─ .rs-header ("👑 후원 랭킹" + "이번 주")
├─ .podium (시상대 — 2등/1등/3등 순서)
│  └─ .podium-item × 3
│     ├─ .podium-avatar-wrap (아바타 + 순위 번호)
│     ├─ .podium-name
│     ├─ .podium-amount (💎 금액)
│     └─ .podium-bar (높이 차등 막대)
└─ .rank-list (4위 이하 목록)
   └─ .rank-item × N
      ├─ .rank-num (순위)
      ├─ .rank-avatar (36×36)
      ├─ .rank-info (이름 + 부가정보)
      └─ .rank-amount (💎 금액)
```

### 12-2. 시상대 스타일

| 순위 | 아바타 크기 | 색상 | 막대 높이 |
|------|-----------|------|----------|
| 1등 | 60×60px | 골드 그라데이션 | 80px |
| 2등 | 48×48px | 실버 그라데이션 | 56px |
| 3등 | 48×48px | 브론즈 그라데이션 | 40px |

### 12-3. 내 랭킹 (ME)
- `.rank-item.me` — 민트 테두리 + 배경
- 순위 숫자: 민트 색
- 닉네임 옆 "ME" 뱃지

### 12-4. 열기/닫기
- 열기: 헤더의 왕관 아이콘 클릭
- 닫기: 딤 배경 클릭

---

## 13. 미르 충전 시스템

### 13-1. 충전 시트 (바텀 시트)
```
.donate-sheet#chargeSheet (z-index: 302)
├─ 헤더 ("미르 충전하기" + 닫기 버튼)
├─ 현재 보유 미르 표시
├─ 충전할 미르 입력 (number input)
├─ 프리셋 칩 (1,000 / 5,000 / 10,000 / 50,000)
├─ 결제 금액 (₩ 표시)
└─ [취소] [충전하기] 버튼
```

### 13-2. 동작
- "충전" 링크 클릭 → 충전 시트 열림
- 프리셋 또는 직접 입력 → 결제 금액 자동 계산 (1미르 = 1원)
- "충전하기" 클릭 → 잔액 추가 + 상단 토스트 "💎 {금액} 미르 충전 완료!"

---

## 14. 이펙트 & 애니메이션

### 14-1. 메시지 진입
```css
@keyframes msgIn {
  0% { opacity: 0; transform: translateY(8px) scale(0.96) }
  100% { opacity: 1; transform: translateY(0) scale(1) }
}
```

### 14-2. 메시지 퇴장
```css
.chat-msg.fade-out {
  opacity: 0;
  transform: translateY(-10px);
  /* transition으로 0.5s 처리 */
}
```

### 14-3. 골드 글로우 (후원 메시지)
```css
@keyframes goldGlow {
  0%, 100% { box-shadow: 0 0 0 0 rgba(232,200,122,0) }
  50% { box-shadow: 0 0 12px rgba(232,200,122,0.15) }
}
```

### 14-4. 제작자 글로우
```css
@keyframes creatorGlow {
  0%, 100% { box-shadow: 0 0 0 0 rgba(168,85,247,0) }
  50% { box-shadow: 0 0 16px rgba(168,85,247,0.2), 0 0 4px rgba(236,72,153,0.15) }
}
```

### 14-5. 제작자 샤인 (빛 스윕)
```css
@keyframes creatorShine {
  0% { opacity: 0; transform: translateX(-100%) }
  50% { opacity: 1 }
  100% { opacity: 0; transform: translateX(100%) }
}
```

### 14-6. 로딩 도트 바운스
```css
@keyframes dotBounce {
  0%, 80%, 100% { transform: translateY(0); opacity: 0.4 }
  40% { transform: translateY(-6px); opacity: 1 }
}
/* 딜레이: 1번째 0s, 2번째 0.15s, 3번째 0.3s */
```

### 14-7. 슬롯 타이틀 펄스
```css
@keyframes slotTitlePulse {
  0%, 100% { opacity: 0.6; text-shadow: 0 0 10px rgba(60,200,168,0.3) }
  50% { opacity: 1; text-shadow: 0 0 30px rgba(60,200,168,0.6) }
}
```

### 14-8. 모달 진입
```css
@keyframes modalIn {
  from { transform: scale(0.92); opacity: 0 }
  to { transform: scale(1); opacity: 1 }
}
```

### 14-9. 토스트
```css
.toast {
  position: fixed; bottom: 120px;
  /* 아래에서 위로 슬라이드 */
  transform: translateX(-50%) translateY(20px);
  opacity: 0;
  transition: opacity 0.3s, transform 0.3s;
}
.toast.show {
  opacity: 1;
  transform: translateX(-50%) translateY(0);
}
/* 2.5초 후 자동 숨김 */
```

```css
.toast-top {
  position: fixed; top: 60px;
  /* 위에서 아래로 슬라이드 */
  transform: translateX(-50%) translateY(-20px);
}
```

---

## 15. 방송 종료 화면

### 15-1. 구조
```
.end-screen (전체 화면, z-index: 900, 딤 배경)
├─ .end-profile (80×80, 민트 테두리)
├─ .end-title ("라이브가 종료되었습니다")
├─ .end-subtitle ("시청해주셔서 감사합니다 💚")
├─ .end-ranking-title ("👑 후원 랭킹")
├─ .end-rank-list (전체 순위 목록)
│  └─ .end-rank-item × N
│     ├─ 순위 번호 (1~3위 색상 차등: 금/민트/브론즈)
│     ├─ 아바타
│     ├─ 이름 (1~3위 메달 이모지 포함)
│     └─ 💎 금액
└─ [다시 시작] 버튼
```

### 15-2. Top 3 스타일

| 순위 | 배경 | 테두리 | 번호 색상 |
|------|------|--------|----------|
| 1 | 금빛 그라데이션 15%→5% | 금빛 20% | `#ffd700` |
| 2 | 민트 그라데이션 12%→4% | 민트 20% | `--primary-normal` |
| 3 | 브론즈 그라데이션 10%→5% | 브론즈 15% | `#cd7f32` |

### 15-3. 다시 시작
- 모든 상태 리셋 (잔액 2480, 턴 1, 랭킹 초기화, 채팅 클리어)
- init과 동일하게 재시작

---

## 16. 반응형 (모바일/데스크톱)

### 16-1. 모바일 (430px 이하)
- `.screen`: max-width 430px, 중앙 정렬
- 캐릭터 이미지: **가로 100%** 맞춤 (`background-size: 100% auto`)
- 배경: `--bg-alternative` + 그라데이션
- 그라데이션 배경 (char-area): radial-gradient 적용

### 16-2. 데스크톱 (431px 이상)
- `.screen`: **전체 뷰포트 너비**, padding 0 20px
- 배경: **투명** (뒤에 블러 배경 보임)
- `.screen::before` — 캐릭터 이미지 블러 배경:
  ```css
  content: '';
  position: fixed; inset: 0; z-index: 0;
  background-image: url('./images/char-full.webp');
  background-size: cover;
  background-position: center 15%;
  filter: blur(30px) brightness(0.4);
  transform: scale(1.15);  /* 가장자리 블러 잘림 방지 */
  ```
- 캐릭터 이미지: **세로 100%** 맞춤 (`background-size: auto 100%`)
- char-area 배경: **투명** (그라데이션 제거)
- `.bottom-panel`, `.header`: `max-width: 696px`, `margin: 0 auto`

### 16-3. 대형 데스크톱 (1024px 이상)
- `.bottom-panel`, `.header`: `max-width: 940px`

---

## 17. 에셋 목록

### 17-1. 캐릭터 이미지

| 파일 | 용도 |
|------|------|
| `images/char-full.webp` | 메인 캐릭터 전신 (배경 + 블러 배경) |
| `images/char-profile.png` | 프로필 아바타 (헤더, 로딩, 종료화면) |
| `images/cutscene-1.webp` | 대사 모드 컷신 배경 |

### 17-2. 프로필 이미지 (더미 유저 아바타, 16개)

| 파일 |
|------|
| `images/43638C.jpg` |
| `images/76de351d1d9e40526a57aba9c93b6719.jpg` |
| `images/7DHuQE...fkUn1v5UdEddMZBGiQUqAA.webp` |
| `images/cute-anime-3.jpg` |
| `images/i13907973381.png` |
| `images/i14075609394.png` |
| `images/images (1).jpeg` |
| `images/images (2).jpeg` |
| `images/images (3).jpeg` |
| `images/images.jpeg` |
| `images/rank-1.png` |
| `images/rank-2.webp` |
| `images/rank-3.webp` |
| `images/rank-4.webp` |
| `images/rank-5.webp` |
| `images/rank-me.webp` |

### 17-3. 후원 GIF (14개)

| 파일 |
|------|
| `images/donation-gif/petpet.gif` |
| `images/donation-gif/shupogaki.gif` |
| `images/donation-gif/trickcal-trickcal-chibi-go.gif` |
| `images/donation-gif/ado-licking.gif` |
| `images/donation-gif/chibi-doki-chibidoki.gif` |
| `images/donation-gif/chibi-doki-shield.gif` |
| `images/donation-gif/chibidoki-bananadoki.gif` |
| `images/donation-gif/evil-thought-funny.gif` |
| `images/donation-gif/sucrose-genshin-impact.gif` |
| `images/donation-gif/waal-boyss-anime.gif` |
| `images/donation-gif/trickcal-butter.gif` |
| `images/donation-gif/trickcal-revive.gif` |
| `images/donation-gif/trickcal-slap.gif` |
| `images/donation-gif/trickcal-chibi-go-2.gif` |

### 17-4. 티어 이미지 (후원 금액별, 6개)

| 파일 | 금액 |
|------|------|
| `images/tier-10.png` | 10미르 |
| `images/tier-50.png` | 50미르 |
| `images/tier-100.png` | 100미르 |
| `images/tier-300.png` | 300미르 |
| `images/tier-500.png` | 500미르 |
| `images/tier-1000.png` | 1000미르 |

### 17-5. 아이콘 (SVG, 10개)

| 파일 | 용도 |
|------|------|
| `images/icon/crown.svg` | 랭킹 버튼 |
| `images/icon/send.svg` | 전송 |
| `images/icon/gift.svg` | 후원 |
| `images/icon/chat-bubble.svg` | 채팅 |
| `images/icon/chat-bubble-active.svg` | 채팅 (활성) |
| `images/icon/magic-wand.svg` | AI |
| `images/icon/menu.svg` | 메뉴 |
| `images/icon/pencil.svg` | 편집 |
| `images/icon/bulb.svg` | 아이디어 |
| `images/icon/chevron-down.svg` | 드롭다운 |
| `images/icon/annotation.svg` | 주석 |
| `images/icon/history.svg` | 히스토리 |

---

## 18. 상태 관리

### 18-1. 전역 상태 변수

| 변수 | 타입 | 초기값 | 설명 |
|------|------|--------|------|
| `balance` | number | 2480 | 보유 미르 |
| `jackpot` | number | 0 | 잭팟 누적액 (금액의 10%) |
| `turnNumber` | number | 1 | 현재 턴 번호 |
| `countdownSeconds` | number | 10 | 카운트다운 남은 초 |
| `chatPaused` | boolean | false | 더미 채팅 일시정지 |
| `currentTurnMessages` | array | [] | 현재 턴 AI 메시지 DOM 배열 |
| `inputMode` | string | 'free' | 입력 모드 ('free' \| 'ai') |
| `boostActive` | boolean | false | 부스터 활성 여부 |
| `chatSelectedAmount` | number | 0 | 채팅 모드 선택 금액 |
| `dlgMode` | boolean | false | 대사 모드 여부 |
| `dlgAborted` | boolean | false | 대사 건너뛰기 여부 |
| `viewerBase` | number | 1247 | 시청자 수 기준값 |
| `selectedVoice` | string | 'celine' | TTS 보이스 |
| `userMirSpent` | object | {} | 유저별 미르 누적 소비 |

### 18-2. 타이머

| 타이머 | 용도 | 간격 |
|--------|------|------|
| `countdownTimer` | 카운트다운 | 50ms (setInterval) |
| `dummyChatTimer` | 더미 채팅 생성 | 600~1800ms (setTimeout 재귀) |
| `dummyDonationTimer` | 더미 후원 생성 | 8~18초 (setTimeout 재귀) |
| `viewerCount` | 시청자 수 변동 | 4초 (setInterval) |

---

## 19. 데이터 구조

### 19-1. 더미 유저 (16명)
```javascript
{ name: '셀린시발단', img: '...' },
{ name: '만년2등', img: '...' },
{ name: 'ㅇㅇ', img: '...' },
{ name: '흑룡의눈', img: '...' },
{ name: '야근탈출러', img: '...' },
{ name: '셀린결혼해줘', img: '...' },
{ name: '개념글작성자', img: '...' },
{ name: '붉은달', img: '...' },
{ name: '치킨먹는중', img: '...' },
{ name: '눈팅러', img: '...' },
{ name: '고인물', img: '...' },
{ name: '천둥번개', img: '...' },
{ name: 'ㄱㅇㅇ', img: '...' },
{ name: '큰곰자리', img: '...' },
{ name: '해탈한자', img: '...' },
{ name: '존버중', img: '...' },
```

### 19-2. 제작자 정보
```javascript
{ name: '카하', img: './images/char-profile.png' }
```

### 19-3. AI 대사 세트 (8세트)
- 각 세트 2~3턴
- 턴 타입: `dialogue` (대사) 또는 `narration` (나레이션)
- 각 턴에 `lines` 배열 (2줄 정도)

### 19-4. 티어별 AI 반응 (6단계)
```
10미르: 무심한 반응
50미르: 약간 놀란 반응
100미르: 진심 어린 반응
300미르: 감동 반응
500미르: 깊은 감사 반응
1000미르: 전설급 반응
```

---

## 부록: 주요 z-index 레이어

| z-index | 요소 |
|---------|------|
| 0 | `.screen::before` (데스크톱 블러 배경) |
| 1 | `.char-img` |
| 2 | `.char-img-overlay` |
| 5 | `.bottom-gradient` |
| 10 | `.bottom-panel` |
| 30 | `.countdown-wrap` |
| 35 | `.winner-popup` |
| 40 | `.top-gradient` |
| 50 | `.header` |
| 200 | `.slot-overlay` |
| 250 | `.donation-alert` |
| 300 | `.overlay-backdrop` |
| 301 | `.donate-sheet`, `.ranking-sheet` |
| 302 | `#chargeSheet` |
| 399 | `.screen-flash` |
| 400 | `.donate-effect` |
| 500 | `.toast`, `.toast-top` |
| 900 | `.end-screen` |
| 999 | `.custom-amt-popup` |
