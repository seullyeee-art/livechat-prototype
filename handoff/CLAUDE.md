# Donation Prototype — AI Context Document

> 이 문서는 AI(Claude)가 프로젝트 작업 시 반드시 따라야 하는 규칙과 컨텍스트입니다.

## 언어 규칙
모든 답변과 설명은 반드시 한국어로 작성할 것.

---

## 프로젝트 개요

| 항목 | 내용 |
|------|------|
| **타입** | 단일 HTML 파일 (standalone) |
| **파일** | `donation-prototype.html` |
| **폰트** | Pretendard Variable JP (CDN) |
| **스타일링** | 인라인 CSS (`:root` 토큰 기반) |
| **이미지 경로** | `./images/` |
| **디자인 시스템** | MIRAI Design System (Figma) |

---

## 이미지 에셋

| 파일 | 용도 |
|------|------|
| `char-full.webp` | 채팅 배경 캐릭터 전신 |
| `char-profile.png` | 헤더/후원반응 프로필 아바타 |
| `rank-1.png` | 랭킹 1위 프로필 |
| `rank-2.webp` | 랭킹 2위 프로필 |
| `rank-3.webp` | 랭킹 3위 프로필 |
| `rank-4.webp` | 랭킹 4위 프로필 |
| `rank-5.webp` | 랭킹 5위 프로필 |
| `rank-me.webp` | 내 프로필 |

---

## MIRAI 디자인 토큰 (이 프로젝트에서 사용)

### 색상 (`:root` 변수)

**Primary (Mojito 계열)**
| 변수 | 값 | 용도 |
|------|-----|------|
| `--primary-normal` | `#13bb8f` (mojito-50) | CTA 버튼, 강조 |
| `--primary-strong` | `#12b18b` (mojito-45) | 버튼 hover |
| `--primary-heavy` | `#10a782` (mojito-40) | 버튼 active |

**Label**
| 변수 | 용도 |
|------|------|
| `--label-normal` | 기본 텍스트 (rgba 226,232,240,0.99) |
| `--label-strong` | 강조 텍스트 (#fff) |
| `--label-neutral` | 보조 텍스트 (rgba 186,196,210,0.88) |
| `--label-alternative` | 캡션/힌트 (rgba 148,163,184,0.61) |
| `--label-assistive` | 플레이스홀더 (rgba 148,163,184,0.28) |
| `--label-disable` | 비활성 (rgba 113,128,150,0.16) |

**Background**
| 변수 | 용도 |
|------|------|
| `--bg-normal` | 기본 배경 (rgba 20,25,35) |
| `--bg-alternative` | 대체 배경 (rgba 13,16,23) |
| `--bg-elevated` | 상승 배경 (rgba 18,18,24) |

**Line / Fill**
| 변수 | 용도 |
|------|------|
| `--line-normal` | 일반 테두리 (rgba 148,163,184,0.32) |
| `--line-neutral` | 중립 테두리 (rgba 148,163,184,0.28) |
| `--line-alternative` | 약한 테두리 (rgba 148,163,184,0.22) |
| `--fill-normal` | 일반 채움 (rgba 148,163,184,0.22) |
| `--fill-strong` | 강한 채움 (rgba 148,163,184,0.28) |
| `--fill-alternative` | 약한 채움 (rgba 148,163,184,0.12) |

**Accent**
| 변수 | 값 | 용도 |
|------|-----|------|
| `--accent-gold` | `#e8c87a` | 골드 강조, 후원 |
| `--accent-gold-strong` | `#c9a04a` | 골드 active |

**기타**
| 변수 | 용도 |
|------|------|
| `--material-dimmer` | 오버레이 배경 (rgba 0,0,0,0.78) |
| `--status-positive` | 성공 (#34D399) |
| `--status-cautionary` | 경고 (#FB923C) |
| `--status-negative` | 에러 (#F87171) |

---

### 후원 티어 매핑

| 금액 | 이모지 | 라벨 |
|------|--------|------|
| 10 | 💧 | 응원 |
| 50 | ☕ | 간식 |
| 100 | 💎 | 선물 |
| 300 | ⭐ | 스페셜 |
| 500 | 💝 | 프리미엄 |
| 1000 | 👑 | 전설 |

---

## 금지사항

| # | 금지 | 올바른 방법 |
|---|------|-------------|
| 1 | 헥스 코드 직접 사용 | `var(--*)` CSS 변수 사용 |
| 2 | 토큰에 없는 색상 추가 | 사용자에게 먼저 확인 |
| 3 | 허락 없이 새 파일 생성 | 사용자에게 먼저 제안 |
| 4 | 기존 구조 임의 변경 | 변경 필요 시 이유와 함께 제안 |
| 5 | 추측을 사실처럼 말하기 | 가설은 검증 후 결론 |
| 6 | 폰트를 Pretendard JP 외 사용 | 항상 Pretendard Variable JP |

---

## 작업 프로세스

1. **문제/요청 이해** — 불분명하면 사용자에게 질문
2. **원인 분석** — 가설 → 검증 → 확정
3. **해결책 제시** — 2~3개 방안, 영향 분석
4. **작업 계획 보고** — 코드 작성 전 필수 승인
5. **작업 실행** — 승인 후 진행
6. **결과 검증** — 빌드 에러, 토큰 준수 확인
7. **완료 보고** — 변경 사항 요약

---

## MIRAI 컴포넌트 라이브러리

> 출처: Figma `tdemkIRFUwsxyCBaHSK0sX` (MIRAI Design System / 3 Component 페이지)
> 상세 디자인 필요 시 해당 node-id로 Figma MCP 호출

### 1 Layout (`16215:17599`)
| 컴포넌트 | 설명 | 주요 스펙 |
|----------|------|-----------|
| **Bottom Sheet** | 하단 슬라이드 시트 | radius-top:20px, bg:bg-elevated, handle:40×4px fill-normal |
| **Modal** | 중앙 다이얼로그 | radius:16px, padding:24px, dimmer:material-dimmer |
| **Image** | 이미지 컨테이너 | aspect-ratio 유지, object-fit:cover |
| **Divider** | 구분선 | variant: normal(1px) / thick, color: line-alternative |

### 2 Action (`16215:35516`)
| 컴포넌트 | 설명 | 주요 스펙 |
|----------|------|-----------|
| **Button** | Primary/Secondary/Tertiary | height:48px, radius:12px, primary-bg:primary-normal, font:semibold-16 |
| **Icon Button** | 아이콘 전용 버튼 | size: 32/40/48px, radius:50%, variant: filled/outlined/ghost |
| **Chip** | 선택 칩 | height:32px, radius:full, border:line-alternative, font:regular-14 |
| **Action Area** | 하단 버튼 영역 | safe-area 포함, padding:16px, 1~2 버튼 레이아웃 |

### 3 Selection & Input (`16215:30100`)
| 컴포넌트 | 설명 | 주요 스펙 |
|----------|------|-----------|
| **TextField** | 텍스트 입력 | height:48px, radius:12px, bg:fill-alternative, border:line-alternative, focus:primary-normal |
| **Checkbox** | 체크박스 | size:20px, checked-bg:primary-normal, radius:4px |
| **Radio** | 라디오 버튼 | size:20px, selected-border:primary-normal |
| **Toggle** | 토글 스위치 | width:48px, height:28px, active:primary-normal |
| **Dropdown** | 드롭다운 셀렉트 | height:48px, chevron 아이콘, radius:12px |

### 4 Content (`16215:25192`)
| 컴포넌트 | 설명 | 주요 스펙 |
|----------|------|-----------|
| **Card** | 카드 컨테이너 | radius:16px, bg:fill-alternative, border:line-alternative, padding:16px |
| **List** | 리스트 아이템 | height:56px, padding:16px, divider 포함 가능 |
| **Tab** | 탭 네비게이션 | active:primary-normal + underline 2px, inactive:label-alternative |
| **Badge** | 뱃지 | height:20px, radius:full, bg:primary-normal or fill-alternative |
| **Tooltip** | 툴팁 | radius:8px, bg:fill-strong, arrow 포함, font:regular-14 |
| **Accordion** | 아코디언 | chevron rotate 180deg on open |

### 5 Loading (`16215:24867`)
| 컴포넌트 | 설명 | 주요 스펙 |
|----------|------|-----------|
| **Circular** | 원형 스피너 | size:28px, stroke:primary-normal, animation:spin |
| **Skeleton** | 스켈레톤 | bg:fill-alternative, shimmer animation, variant: text/rect/circle |
| **Pull to Refresh** | 당겨서 새로고침 | Mirai 브랜드 로더 포함 |

### 6 Navigation (`16215:20255`)
| 컴포넌트 | 설명 | 주요 스펙 |
|----------|------|-----------|
| **Header** | 상단 네비게이션 바 | height:56px, back 아이콘 + 타이틀 + 우측 아이콘들 |
| **Tab Bar** | 하단 탭 바 | height:56px + safe-area, 5탭 기준 |
| **Segmented Control** | 세그먼트 토글 | radius:full, bg:fill-alternative, active-bg:fill-strong |

### 7 Feedback (`16215:19283`)
| 컴포넌트 | 설명 | 주요 스펙 |
|----------|------|-----------|
| **Toast** | 토스트 메시지 | radius:12px, bg:rgba(0,0,0,0.75), blur:12px, auto-dismiss 2.5s |
| **Snackbar** | 스낵바 | 하단 고정, action 버튼 포함 가능 |
| **Alert** | 알럿 다이얼로그 | Modal 기반, 1~2 버튼, 타이틀+설명 |
| **Push Badge** | 알림 뱃지 | size:min 8px, bg:status-negative, 숫자 표시 가능 |
| **Empty State** | 빈 화면 상태 | 일러스트 + 타이틀 + 설명 + CTA |
| **Bottom Message** | 시스템 메시지 | 하단 배너 스타일 |

### 8 Layout Essential (`16215:43042`)
| 컴포넌트 | 설명 | 주요 스펙 |
|----------|------|-----------|
| **Essential** | Status Bar + Home Bar | platform: ios/android |
| **Divider** | 구분선 | variant: normal/thick, vertical: true/false |

---

## MIRAI Figma 참조

- **Design System 파일**: `figma.com/design/tdemkIRFUwsxyCBaHSK0sX` (3 Component 페이지)
- **Play Screen 파일**: `figma.com/design/2KQOnDQY2caWGgXodTlYAt`
- Figma MCP로 읽기 가능 (get_design_context, get_screenshot)
- **토큰과 컴포넌트 스펙은 이 문서에 캐시 → 매번 Figma 호출 불필요**
- 상세 디자인이 필요한 경우에만 node-id로 개별 호출

---

**Last Updated**: 2026-03-25
