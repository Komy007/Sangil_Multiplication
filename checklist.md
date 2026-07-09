# Sangil's Times Table Quest 확장 체크리스트

<!-- 기존 구구단 앱에 영어·크메르어 학습과 신규 게임을 통합하는 작업 진행 상황 추적 -->

## 방향 수정 (2026-07-08)
- [x] 사용자 피드백 반영: 별도 앱이 아니라 **기존 앱(gugudan.html) 자체를 확장**하는 것으로 재설계
- [x] 기존 앱의 보상 시스템 구조 조사 (`renderResult`/`getAndIncrementPerfectCount`/`minutesForCount`/`showScreen("gameselect")`)
- [x] 정답 소리 버그 원인 확인 (`getCtx()`의 `resume()` fire-and-forget 레이스 컨디션)
- [x] 사용자와 범위 재확인: 신규 게임 1개(Word-Rocket Command, AI 추천), 구구단·나눗셈도 함께 개선

## 파일 구조
- [x] `index.html`(Learning Quest 독립 앱) 제거
- [x] `gugudan.html` → `index.html`로 복귀, 그 안에서 직접 기능 추가

## 메인 메뉴 확장
- [x] `screen-home`에 "English 📖", "Khmer 🛕" 버튼 추가

## 영어 학습
- [x] Level 1/Level 2 선택 (기존 나눗셈의 `.levelToggle` 패턴 재사용)
- [x] Cloze 문장 완성 + 단어 매칭
- [x] 기존 `renderResult`/보상 시스템에 연결 (mode: "eng")
- [x] 난이도 한 단계 상향: Level 1="Intermediate"(구 L2 내용), Level 2="Advanced"(수동태/관계대명사/관용구 신규 세트)

## 크메르어 학습 (초등 2~3학년 수준)
- [x] 레슨 5개 × 단어 5개 (검수된 데이터 재사용)
- [x] 플래시카드 학습 → 매칭 → 리콜 퀴즈
- [x] 기존 `renderResult`/보상 시스템에 연결 (mode: "khmer")

## 구구단 개선
- [x] "basic" 포맷 문제에 시각적 배열(점 그리드) 힌트 토글 추가

## 나눗셈 개선
- [x] 기존 "몫+나머지 한번에 입력" → 단계별 가이드 입력(자릿수별 몫 → 뺄셈 결과)으로 교체
- [x] 기존 유럽식 표기(divisor 우측) 레이아웃 유지
- [x] 사용자 제공 캄보디아 교과서 사진 기준 계단식(staircase) 풀이 과정 표시로 보정
- [x] 재요청 반영: 몫 자릿수만 입력, 뺄셈/내려받기는 자동 표시로 재설계 (Fable 직접 수정)
- [x] 검증 중 발견한 버그 수정: 1회 오답 후 재시도 성공 시 스트릭이 안 끊기던 문제
- [x] 재요청 반영: 계단식 풀이가 배당수와 같은 열에서 오른쪽 정렬로 바로 이어지도록 레이아웃 수정 (실측 좌표로 검증)
- [x] 손그림 참고자료 반영: 세로 구분선이 몫 자릿수가 늘어날수록 계단식 전체 높이만큼 자라도록 수정 (실측 좌표로 검증)
- [x] 참고 이미지 반영: 배당수의 현재 작업 자릿수에서 계단식으로 내려오는 점선+화살표 추가 (CSS만으로 구현, 겹침 없음 확인)
- [x] 후속 요청 반영: 완료된 단계의 화살표도 사라지지 않고 계속 남아있도록 수정 (현재/과거 색상 구분)

## 신규 게임 (Word-Rocket Command)
- [x] `screen-gameselect`에 3번째 버튼 추가
- [x] `screen-rocket` + `startRocket(seconds)`/`endRocket()` 추가 (`startTetris`/`endTetris` 패턴 그대로 모방)
- [x] 기존 테트리스/벽돌깨기 코드는 절대 수정하지 않음

## 버그 수정
- [x] `getCtx()` resume 레이스 컨디션 수정 (정답 소리 안 나는 문제)

## 모바일 터치 타겟 감사
- [x] 이번 세션에 추가한 모든 인터랙티브 요소(`.chipBtn`/`.matchItem`/`.lessonCard`/`.listenBtn`/`.hintToggleBtn`) 최소 48px 높이 확보
- [x] 로켓 게임 ◀▶ 컨트롤 60px 이상 확인 (실측 166.7×63.3px)

## 검증
- [x] 브라우저 프리뷰: 프로필 생성 → 각 메뉴 진입 → 만점 시 보너스 게임 잠금 해제 → 신규 게임 플레이
- [x] 나눗셈 단계별 가이드: L1/L2, 정답/오답(1차 힌트→2차 리빌), 몫→나머지 전환, 스트릭/통계 정합성 확인
- [x] 로켓 게임: 화면 전환, 타이머, 좌우 컨트롤(마우스/터치/키보드), 콘솔 에러 0건 확인
- [x] 콘솔 에러 0건
- [x] Fable 재검증: 나눗셈 1단계(88÷9)·2단계(76÷6) 계단식 표기 실제 조작으로 직접 확인, 영어 레벨 라벨, 크메르어 화면, 로켓 화면, 모바일 375px 레이아웃(스크린샷 아티팩트를 실측 DOM 좌표로 교차검증) 모두 정상
- [x] 커밋 (푸시는 사용자 지시 후)

## 2026-07-09 확장 2차 — 크메르 자모, 숫자 상향, 영어 3단계, 나눗셈 3자리

- [x] 나눗셈: `genDivFactsL2`를 2~3자리 배당수 혼합 출제로 확장 (Fable 직접 수정, 255÷6 실제 조작 검증)
- [x] 크메르어: 자모(자음+모음) 학습 서브모듈 신규 추가 — 자음 12개(기존 학습한 단어와 연결) + 모음 부호 예시 1개
- [x] 크메르어: 숫자 레슨 1~5 → 1~10 확장, 크메르 숫자 기호(០-៩)도 함께 표시
- [x] 영어: Level 3("Expert") 신규 추가 (Level 1/2는 그대로 유지)
- [x] Fable 재검증: 자모 학습(12개 순서+모음 데모 카드+매칭), 숫자 10단어 학습+분모(0/10) 정정 확인, 기존 5단어 레슨(인사말) 회귀 없음(0/5 유지), 영어 Level 3 콘텐츠, 3단계 토글 모바일 터치 크기(97×58px), 자모 레슨 카드 크기(343×66px), 콘솔 에러 0건 — 모두 직접 조작으로 확인
- [x] 재요청 반영: 계단식 자릿수를 실제 배당수 컬럼에 픽셀 단위로 정확히 정렬 (291÷4로 소수점까지 일치 검증)
- [x] 재요청 반영: 문제 정답 확인 후 자동 진행 대신 "Next Problem →" 버튼 클릭 시 다음 문제로 전환
- [x] 재요청 반영: 내려오는 화살표가 계단식 숫자 행까지 실제로 이어지도록 SVG 연결선으로 교체 (714÷8 스크린샷으로 검증)
- [x] 커밋 및 푸시

## 2026-07-09 확장 3차 — 파스텔 리스킨 + 신규 게임 2종 + 연속 스트릭 보상

### Task 1: 파스텔 컬러 팔레트 리스킨 (시각 전용)
- [x] `:root` 변수 전면 교체 — `--cream/--coral/--teal/--sunny/--lavender/--mint/--ink` + 텍스트용 어두운 파생색(`*-text`) 추가
- [x] 기존 변수명(`--bg1/--card/--accent/--green/--text` 등)은 유지하되 파스텔 값으로 재매핑 → 대부분의 규칙이 자동으로 재색상화되도록 설계
- [x] 모듈별 색상 스코프: `#screen-division`(teal), `#screen-english`(sunny), `#screen-khmer`(lavender), 아케이드 화면들(mint) — CSS 변수 스코핑으로 버튼/텍스트가 자동 전환
- [x] 카드/버튼을 "팝된 카드" 스타일로 통일 (`border-radius:24px`, `box-shadow:var(--card-shadow)`, 버튼 `0 4px 0 rgba(0,0,0,.15)`)
- [x] 가독성 감사: 흰 배경 위 밝은 파스텔 텍스트(코랄/틸/써니/라벤더/민트)는 전부 대비가 부족함을 WCAG 계산으로 확인 → 채도 유지한 어두운 `-text` 변형을 텍스트 전용으로 분리, 배경 채움에는 원래 스펙 색상 유지
- [x] 버튼 텍스트는 흰색 대신 `var(--ink)`로 통일 (모든 모듈의 밝은 배경 채움 위에서 4.4~8.9:1 대비 확보, sunny 포함)
- [x] 하드코딩된 다크테마 색상 전부 교체: `.nameInput`/`.progressWrap`/`.switchBtn`/`.ldLeft`/`.ldLine`(구분선, 흰색→ink) 등
- [x] `.tetrisOverOverlay`(타임업 오버레이)는 의도적으로 어두운 배경 유지 + `color:#fff` 명시 추가 (조상 텍스트색이 ink로 바뀌어 안 보이던 문제 방지)
- [x] 나눗셈 SVG 내려오는 화살표 stroke 색상을 JS에서 밝은 배경용으로 교체 (`divRenderBringDownArrows`)
- [x] `body`에 Comic Sans 폰트 스택 추가, 나눗셈 세로셈 숫자는 기존 Courier New 그대로 유지
- [x] 별표/streak/perfectBanner/comboPopup 등 sunny 계열 텍스트는 전부 `--sunny-text`로 교체 (원본은 흰 배경 위 대비 1.4:1이라 사실상 안 보였음)

### Task 2: 신규 아케이드 게임 2종
- [x] Memory Match: 4×4 카드(8쌍 이모지), 이동 횟수 카운트, 프로필별 최고 기록(`localStorage` `nsKey` 패턴 재사용), 승리 배너 + "Play Again"
- [x] Speed Tap: 30초 라운드(외부 보너스 타이머와 별개 카운터를 같은 setInterval에서 함께 감소시키는 방식으로 구현), 폭탄(-2, 0 미만 방지), 프로필별 최고 점수
- [x] 두 게임 모두 Rocket 게임의 `startX(seconds)`/`endX()` 패턴 그대로 모방(자체 타이머, `showScreen`, overlay)
- [x] `#screen-gameselect`에 버튼 2개 추가 (5개 카드 375px 모바일에서도 줄바꿈 없이 세로 스택으로 정상 표시)
- [x] 기존 Tetris/Breakout/Rocket 코드는 전혀 수정하지 않음 (라이브 재생 테스트로 회귀 없음 확인)

### Task 3: 보상 시스템 — 날짜 기준 → 연속 스트릭 기준 전환
- [x] `getAndIncrementPerfectCount`/`minutesForCount` 제거 (다른 참조 없음을 grep으로 확인 후 삭제) → `getAndIncrementStreak`/`resetStreak`/`computeMinutes`/`currentStreak`로 교체
- [x] `renderResult`: 만점 시 스트릭 증가 + 모듈 난이도(mult/eng=easy, div/khmer=hard)로 분당 보너스 계산, 비만점 시 `resetStreak()` 호출
- [x] 홈 화면 "Today's Missions" 카드에 "🔥 Perfect Streak: N" 표시 추가
- [x] 라이브 검증: division(hard) 연속 2회 만점 → 4분 확인, 중간에 비만점 세션 삽입 → 스트릭 0 리셋 확인 → 다음 만점이 다시 2분으로 시작함을 확인

### 검증
- [x] `node --check` 통과 (최종 3838줄)
- [x] 브라우저 프리뷰: 프로필 생성, 홈, 나눗셈 가이드(계단식+SVG 연결선 가독성), 영어, 크메르(단어+자모), 아케이드 5종 선택 화면(375px 포함), Memory Match 완주(승리 배너+베스트 기록 확인), Speed Tap 라운드 종료(베스트 점수 확인), Tetris/Breakout 회귀 없음 확인
- [x] 콘솔 에러 0건 (전체 세션 동안)
- [x] Fable 재검증(Sonnet 보고 그대로 안 믿고 직접 확인): 프로필/홈/나눗셈(556÷9 실제 조작, SVG 연결선 가독성)/영어 3단계/크메르 자모 카드/아케이드 5종 스크린샷 확인. 구구단 만점(20/20) 실제 플레이 → 보너스 2분 확인 → Memory Match 카드 뒤집기 정상 동작 확인. 두 번째 연속 만점 → 3분(easy 난이도) 확인. `sangil_perfectStreak__Sangil` localStorage 값으로 스트릭 영속 확인. 모바일 375px 가로 스크롤 없음.
- [x] 테스트 중 발견한 아티팩트(실제 버그 아님): Sonnet의 이전 테스트 세션에서 Memory Match를 자동 플레이하던 백그라운드 프로세스가 남아있어 카드가 저절로 뒤집히는 것처럼 보였음 — 페이지 완전 새로고침 후 재현 안 됨, 실제 앱 코드와 무관.
