# MIRAI 라이브챗 — 프로토타입 v2 스펙

> 버전: v2.0 (2026-04-16)
> 파일: `livechat-prototype.html` (단일 HTML, 바텀시트 버전)
> 배포: GitHub Pages `https://seullyeee-art.github.io/partychat-prototype-v2/livechat-prototype.html`

---

## 목차
1. [디자인 상세](#1-디자인-상세) ← 개발 핸즈오프용 주요 참고
2. [화면 구조](#2-화면-구조)
3. [상태 & 모드](#3-상태--모드)
4. [채팅 시스템](#4-채팅-시스템)
5. [인풋 시스템](#5-인풋-시스템)
6. [후원 시스템](#6-후원-시스템)
7. [대사 모드](#7-대사-모드)
8. [기술 스택 & 파일 구조](#8-기술-스택--파일-구조)

---

## 1. 디자인 상세

```
mirai-chat-prototype/
├── livechat-prototype.html   ← 메인 프로토타입 (바텀시트 버전)
├── CLAUDE.md                 ← AI 작업 규칙 + 디자인 토큰
├── SPEC.md                   ← 이 문서
├── vercel.json
├── .github/workflows/pages.yml
└── images/
    ├── char-full.webp        ← 캐릭터 전신 배경
    ├── char-profile.png      ← 캐릭터 프로필 아바타
    ├── cutscene-1.webp       ← 컷씬/대사 배경
    ├── tier-{10,50,100,300,500,1000}.png ← 후원 티어 아이콘
    ├── rank-{1~5}.png/webp   ← 랭킹 유저 프로필
    ├── rank-me.webp          ← 내 프로필
    ├── donation-gif/         ← 후원 이펙트 GIF
    └── icon/
        ├── Frame 32.png      ← 1등 메달 아이콘 (보라색)
        ├── Frame 31.png      ← 2등 메달 아이콘 (민트)
        ├── Frame 33.png      ← 3등 메달 아이콘 (골드)
        ├── Badge 02.png      ← 방패 하트 (미르 소비 유저 뱃지)
        ├── crown.svg         ← 왕관
        ├── gift.svg          ← 선물
        └── send.svg          ← 전송
```

---

## 3. 화면 구조

### 3.1 레이아웃 개요

```
┌─────────────────────────────┐
│ 헤더 (타이머 | 뷰어수 | 종료)  │ ← z-index:50, position:absolute
├─────────────────────────────┤
│                             │
│   캐릭터 배경 (char-full)     │ ← 전체 배경
│                             │
│   ┌── 대사 영역 (dlg-area) ──┐│
│   │ 캐릭터 이름              ││ ← 채팅 열릴 때 숨김
│   │ 대사 텍스트 (타이핑)      ││
│   └─────────────────────────┘│
│                             │
│   ┌── 채팅 피드 (compact) ──┐│ ← 기본: 작은 영역
│   │ 최근 채팅 메시지들        ││
│   └─────────────────────────┘│
│                             │
│   ┌── 인풋바 + 선물 아이콘 ──┐│
│   │ [캐릭터에게 메세지...💎10]││
│   └─────────────────────────┘│
└─────────────────────────────┘
```

### 3.2 채팅 확장 시 (바텀시트)

```
┌─────────────────────────────┐
│ 헤더                         │
├─────────────────────────────┤
│ 캐릭터 배경 + 대사 (축소)     │
├─ 바텀시트 ──────────────────┤
│   ── 핸들 (닫기용) ──         │ ← 터치 시 닫힘
│   [👑1등 🥈2등 🥉3등] 랭킹바  │ ← 후원 1명 이상일 때 표시
│                             │
│   채팅 메시지들               │ ← 배경 투명 (후원만 카드)
│   ...                       │
│                             │
│   [인풋: 시청자들과 대화...]   │ ← free 모드
│   [선물 아이콘]               │
└─────────────────────────────┘
```

---

## 4. 상태 & 모드

### 4.1 디폴트 상태 (캐릭터 채팅 모드)
- 대사 영역(`dlg-area`) 표시 + 타이핑 애니메이션
- 채팅 피드 compact (max-height:70px)
- 인풋: AI 모드 (`캐릭터에게 메세지 보내세요 · 💎 10`)
- 보내기 버튼: **텍스트 입력 전 비활성화**
- 배경 그라디언트(`dlg-gradient::after`) 적용

### 4.2 일반 채팅 모드 (바텀시트)
- 말풍선 아이콘 클릭 → 채팅 확장 (바텀시트 스타일)
- 인풋: free 모드 (`시청자들과 자유롭게 대화하세요`)
- 아이콘 전환: 말풍선 → X
- 캐릭터 이름 숨김
- 토스트: "💬 시청자 채팅모드로 전환되었습니다"
- 핸들 또는 X 아이콘으로 닫기 → 디폴트 복귀
- 토스트: "✨ 캐릭터 채팅모드로 전환되었습니다"

### 4.3 대사 턴 사이클
```
타이머 30초 카운트다운
  → 유저들이 AI 메시지 전송 (💎 10미르)
  → 타임아웃 → 슬롯 룰렛 (당첨자 선택)
  → 대사 모드 진입 (enterDialogueMode)
  → AI 대사 타이핑 (다단계: 유저메시지 → 로딩 → AI 응답)
  → 대사 종료 (exitDialogueMode)
  → 대사 영역 유지 (마지막 대사 계속 보임)
  → 다음 턴 타이머 시작
```

---

## 5. 채팅 시스템

### 5.1 메시지 타입

| 타입 | 설명 | 뱃지 | 비용 |
|------|------|------|------|
| `free` | 일반 무료 채팅 | 없음 | 무료 |
| `ai` | 캐릭터에게 보내기 | `캐릭터에게` (민트) | 💎 10 |
| `donate` | 후원 메시지 | 보라색 카드 | 후원 금액 |
| `creator` | 제작자 메시지 | `제작자` (보라+핑크) | - |

### 5.2 채팅 스타일 (치지직 참고)
- **닉네임 컬러**: 유저별 해시 기반 20가지 뮤트 컬러 자동 배정
- **방패 하트 뱃지** (`Badge 02.png`): 미르 1이라도 소비한 유저
- **랭킹 메달**: 1등(`Frame 32.png`), 2등(`Frame 31.png`), 3등(`Frame 33.png`)
- **뱃지 순서** (닉네임 앞): 타입뱃지 → 랭킹메달 → 방패하트 → 닉네임
- **후원 메시지**: 보라색 그라디언트 카드 + 💎 금액 태그
- **바텀시트 열릴 때**: 일반 채팅 배경 투명 (후원만 카드 유지)
- **프로필 이미지**: 숨김 처리 (`display:none`)

### 5.3 랭킹 바
- 위치: 채팅 바텀시트 상단 (핸들 아래)
- 후원 0명이면 숨김, 1명 이상이면 표시
- 터치 시 랭킹 시트 열림
- 내용: 메달 아이콘 + 닉네임(화이트) + 금액(화이트)

### 5.4 더미 채팅
- 간격: 2~4초 랜덤
- 초기 메시지: 3개 프리로드
- 비율: 무료 75% / AI 20% / 제작자 5%

---

## 6. 인풋 시스템

### 6.1 구조
```
┌─ input-bar-row ─────────────────────────┐
│ ┌─ input-bar ────────────────────────┐  │
│ │ [textarea] [말풍선/X] [전송]        │  │
│ └────────────────────────────────────┘  │
│ [선물 아이콘]                            │
└─────────────────────────────────────────┘
```

### 6.2 모드별 동작

| | AI 모드 (디폴트) | Free 모드 |
|---|---|---|
| placeholder | `캐릭터에게 메세지 보내세요 · 💎 10` | `시청자들과 자유롭게 대화하세요` |
| maxLength | 100 | 50 |
| 전송 비용 | 💎 10 미르 | 무료 |
| 보내기 버튼 | 텍스트 없으면 비활성 | 항상 활성 |
| 테두리 | 민트 글로우 | 기본 |

### 6.3 선물 아이콘
- input-bar 오른쪽 외부 배치 (38x38px)
- 터치 시 후원 시트 열림
- 기본 선택값: 💎 100 미르

---

## 7. 타이머

- 위치: 헤더 오른쪽 (`header-timer`)
- 표시: `다음턴까지 30s`
- 5초 이하: 빨간색 (`danger` 클래스)
- 대사 모드 중: 숨김 → 대사 종료 시 복원

---

## 8. 후원 시스템

### 8.1 후원 바텀시트
- 진입: 선물 아이콘 탭
- 기본 선택: 💎 100 미르
- 프리셋: 10 / 100 / 500 / 1K / 전액
- 직접입력 필드
- 확인 버튼 → 즉시 처리

### 8.2 후원 메시지 (채팅 내)
- 보라색 그라디언트 카드 스타일
- 닉네임 (연보라) + 메시지 텍스트 (화이트) + 💎 금액 태그
- 짧은 메시지: 금액 인라인 / 긴 메시지: 줄바꿈

### 8.3 후원 이펙트
- 100미르 미만: 틱톡 스타일 플로팅 이모지
- 100미르 이상: 시네마틱 풀스크린 연출

### 8.4 랭킹 시트
- 진입: 채팅 랭킹 바 터치
- 포디움 (1~3위) + 리스트 (4위~) + 내 순위

---

## 9. 슬롯 룰렛

- 트리거: 타이머 종료 시
- 대상: 해당 턴의 AI 메시지 전송자
- 프로필 이미지: 숨김 처리
- 당첨 → 위너 팝업 → 대사 모드 진입

---

## 10. 대사 모드 (Dialogue)

### 10.1 구조
- `dlg-area`: 캐릭터 이름 + 대사 텍스트 + 로딩 인디케이터
- 상단 세이프 에리어: `padding-top: max(56px, safe-area + 48px)`
- 대사 종료 후에도 영역 유지 (마지막 대사 계속 표시)

### 10.2 대사 타입
- `dialogue`: 캐릭터 대사 (골드 `#e5dba0`)
- `narration`: 지문 (화이트)
- `user`: 유저 메시지 (골드)

### 10.3 플로우
```
enterDialogueMode()
  → 채팅 compact + 타이머 숨김
  → 유저 메시지 표시 → 로딩 → AI 대사 타이핑 (35ms/글자)
exitDialogueMode()
  → 대사 영역 유지 (숨기지 않음)
  → 타이머 복원 + 다음 턴 시작
```

---

## 11. 가이드 모달

- 첫 시작 시 0.5초 후 표시
- 2개 모드 안내:
  - 💬 채팅하기: 시청자 대화 + 후원 메시지
  - ✨ 캐릭터에게: 직접 말걸기 + 💎 10미르 소모

---

## 12. 토스트

- 위치: 상단 중앙 고정
- 스타일: 다크 반투명 (`rgba(10,10,14,0.75)`) + `blur(16px)`
- 자동 dismiss: 2초
- 사용처: 모드 전환, 잔액 부족, 후원 완료 등

---

## 13. 반응형

- **모바일**: max-width 430px, safe-area 대응
- **데스크탑**: 블러 배경 (`screen::before`, `filter:blur(30px)`)
- **데스크탑 max-width**: 696px (bottom-panel, header)

---

## 14. 디자인 상세

### 14.1 채팅 메시지 스타일

**기본 메시지 (`type-free`)**
```
배경: rgba(0,0,0,0.55)
border-radius: 16px
padding: 4px 10px
font-size: 13px
line-height: 1.5
닉네임: font-weight 600, 유저별 해시 컬러
텍스트: var(--label-neutral) (rgba 186,196,210,0.88)
```

**AI 메시지 (`type-ai`)**
```
배경: linear-gradient(135deg, rgba(0,0,0,0.55), rgba(60,200,168,0.06))
닉네임: var(--primary-normal) #13bb8f
텍스트: var(--label-normal)
뱃지: "캐릭터에게" — bg rgba(60,200,168,0.12), color var(--primary-normal)
```

**후원 메시지 (`type-donate`)**
```
배경: linear-gradient(135deg, rgba(147,51,234,0.35), rgba(126,34,206,0.25))
border-radius: 12px
padding: 10px 14px
닉네임: #c4b5fd, 13px, bold
텍스트: var(--label-strong) #fff, 14px, weight 500
금액 태그: inline-flex, bg rgba(0,0,0,0.3), border-radius 8px
  — padding 2px 8px, color var(--accent-gold) #e8c87a, 💎 아이콘
  — 짧은 메시지: 텍스트 옆 인라인 (margin-left 6px)
  — 긴 메시지: 자연스럽게 줄바꿈
타입뱃지: display:none (카드 자체가 후원임을 표현)
```

**제작자 메시지 (`type-creator`)**
```
배경: linear-gradient(135deg, rgba(168,85,247,0.15), rgba(236,72,153,0.08))
닉네임: #c084fc, text-shadow 0 0 6px rgba(168,85,247,0.3)
텍스트: #f0abfc, 13px, weight 500
뱃지: "제작자" — gradient bg #a855f7→#ec4899, 화이트 텍스트
shine 애니메이션: ::before pseudo, translateX(-100%→100%)
```

### 14.2 채팅 뱃지 & 아이콘 순서

닉네임 앞에 왼쪽→오른쪽 순서:
```
[타입뱃지] → [랭킹메달] → [방패하트] → [닉네임] → [텍스트]
```

**타입뱃지** (`.msg-badge`)
```
padding: 1px 5px
border-radius: 4px
font-size: 9px, font-weight 700
vertical-align: middle
```

**랭킹 메달** (`.rank-icon`)
```
width/height: 16px
이미지: Frame 32.png(1등), Frame 31.png(2등), Frame 33.png(3등)
vertical-align: middle
margin-right: 2px
```

**방패 하트** (`.mir-heart`)
```
width/height: 14px
이미지: Badge 02.png (보라색 방패+하트)
조건: userMirSpent[user] > 0
vertical-align: middle
margin-right: 2px
```

### 14.3 닉네임 컬러 팔레트 (20색, 뮤트톤)

```
#C88A8A  #D4A09A  #C4899E  #B893BF  #A99BC4
#8E9CC7  #8FADB8  #89B8B8  #8BBFA8  #9BBF8E
#B0BF8E  #C4BA8E  #D4B88E  #C9A68E  #BF9A8E
#C4A0C0  #8EB8BF  #A0C4B0  #D4C9A0  #A8AEBF
```
- 해시 함수: 닉네임 문자열 → 31진법 해시 → 인덱스
- 동일 닉네임은 항상 같은 컬러

### 14.4 바텀시트 (채팅 확장)

```
배경: linear-gradient(180deg, rgba(30,30,30,0.97), rgba(18,18,18,0.99))
border-radius: 20px 20px 0 0
border-top: 1px solid rgba(255,255,255,0.06)
box-shadow: 0 -4px 30px rgba(0,0,0,0.6)
padding: 12px 16px 0
margin: 좌우 -16px (부모 패딩 상쇄, 풀 너비)
min-height: 40vh, max-height: 50vh
layout: display flex, flex-direction column
```

**핸들**
```
width: 40px, height: 4px
border-radius: 2px
background: var(--fill-normal)
margin: 10px auto
동작: 터치 시 closeGeneralChat()
```

**채팅 영역 (expanded 상태)**
```
메시지 배경: 투명 (background:none, padding:4px 0)
후원 메시지만: 보라색 카드 배경 유지
mask-image: none (페이드 제거)
스크롤: overflow-y auto, 스크롤바 숨김
```

### 14.5 랭킹 바

```
배경: rgba(0,0,0,0.5)
border-radius: 12px
padding: 6px 12px
font-size: 11px
display: flex (확장 시만), gap 12px
터치 시 랭킹 시트 열림
```

각 엔트리:
```
[메달 아이콘 18px] [닉네임: white, bold, max-width 60px ellipsis] [금액: white, bold]
```

### 14.6 인풋바

```
input-bar-row: display flex, align-items center, gap 4px
input-bar: border-radius 16px, bg rgba(0,0,0,0.35), backdrop-filter blur(8px)
  — AI 모드: border-color rgba(60,200,168,0.3), box-shadow 0 0 12px rgba(60,200,168,0.15)
input-row2: height 38px, padding 5px 8px, gap 8px
textarea: font-size 13px, max-height 32px
placeholder: color var(--label-alternative)
```

**말풍선/X 토글 버튼** (`.chat-toggle-btn`)
```
width/height: 32px
border-radius: 8px
color: var(--label-neutral)
active 상태: color var(--primary-normal)
아이콘: 말풍선 SVG ↔ X SVG 전환
```

**전송 버튼** (`.send-btn`)
```
width/height: 28px
border-radius: 14px (원형)
background: linear-gradient(105deg, var(--primary-normal), var(--primary-strong))
disabled: opacity 0.4 (AI 모드에서 텍스트 없을 때)
```

**선물 아이콘** (`.donate-trigger-btn`)
```
width/height: 38px
border-radius: 10px
SVG 내부: 24x24px
그라디언트: 하늘색→보라 (giftBow/giftBox/giftStripe)
input-bar 외부 오른쪽 배치
```

### 14.7 헤더

```
position: absolute, top 0, z-index 50
padding: 8px 12px, padding-top max(8px, safe-area)
```

**타이머** (`.header-timer`)
```
height: 32px, padding 0 10px, border-radius 16px
background: rgba(0,0,0,0.25)
font-size: 12px
라벨: "다음턴까지" — 11px, weight 400, var(--label-neutral)
시간: font-weight 700, tabular-nums
danger (≤5초): color var(--status-negative) #F87171
```

**뷰어 수** (`.header-stat`)
```
height: 32px, padding 0 10px, border-radius 16px
background: rgba(0,0,0,0.25)
아이콘: 👁, font-size 12px
값: font-weight 600, var(--label-normal)
```

### 14.8 대사 영역

```
padding-top: max(56px, safe-area + 48px) — 헤더와 겹침 방지
min-height: 260px
justify-content: flex-end
```

**캐릭터 이름** (`.dlg-name-text`)
```
font-size: 17px, weight 600
color: var(--label-strong)
하단 border-bottom: 1px solid rgba(250,250,250,0.08)
채팅 열릴 때: display:none
```

**대사 텍스트** (`.dlg-text`)
```
font-size: 15px, weight 400, line-height 1.65
dialogue: color #e5dba0 (골드)
narration: color #fff
타이핑: 35ms/글자, 골드 커서 블링크
```

**로딩**
```
아바타: 32px, border 1.5px var(--accent-gold)
점 3개: 6px 원, 바운스 애니메이션
텍스트: "셀린이 답변을 생각하고 있어요..."
  — color var(--label-normal)
```

### 14.9 토스트

```
position: fixed, top 60px, 중앙 정렬
background: rgba(10,10,14,0.75)
backdrop-filter: blur(16px)
border: 1px solid rgba(255,255,255,0.06)
border-radius: 12px
padding: 10px 28px
font-size: 13px, weight 500
white-space: nowrap
auto-dismiss: 2초
```

### 14.10 가이드 모달

```
overlay: rgba(0,0,0,0.6), z-index 500
card: width 340px, border-radius 16px
  — gradient bg: rgba(42,42,42,0.98) → rgba(26,26,26,0.98)
  — padding: 24px 20px
타이틀: 16px, bold
모드 카드: padding 14px 16px, border-radius 12px, bg rgba(255,255,255,0.04)
확인 버튼: width 100%, height 48px, border-radius 10px, bg var(--primary-normal)
```

### 14.11 그라디언트 오버레이

**상단** (`.top-gradient`)
```
height: 200px
linear-gradient: rgba(0,0,0,0.7) → transparent
```

**하단** (`.bottom-gradient`)
```
height: 520px
linear-gradient: rgba(0,0,0,0.85) 0% → transparent 100%
```

**대사 모드** (`.dlg-gradient::after`)
```
height: 100%, z-index 6
linear-gradient to top: rgba(0,0,0,0.7) → rgba(0,0,0,0.3) → transparent
```

**데스크탑 블러 배경** (`.screen::before`, 768px+)
```
position: fixed, inset 0
background-image: char-full.webp
filter: blur(30px) brightness(0.4)
transform: scale(1.15)
```
