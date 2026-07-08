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
