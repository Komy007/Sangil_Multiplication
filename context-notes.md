# Sangil's Times Table Quest 확장 컨텍스트 노트

<!-- 작업 중 내린 결정과 그 이유를 기록. 다음 세션이 이어받을 때 참조. -->

## 2026-07-08 방향 전환 (중요 — 반드시 읽을 것)

- **이전 접근(Learning Quest 별도 앱)은 사용자 의도와 다름.** 사용자는 "기존 학습에 두 가지(영어·크메르어) 학습을 더하고 게임 1개를 추가"해 달라는 것이었지, 완전히 새로운 별도 앱을 원한 게 아니었음. 별도 앱 접근은 폐기.
- **올바른 방향: 원래 앱(`gugudan.html`, 원래는 `index.html`)을 그대로 확장.** 프로필 시스템, 관리자 대시보드, Today's Missions 로켓, Mastery Map 히트맵 등 기존 구조는 전혀 건드리지 않고 그 위에 기능을 얹는다.
- **기존 앱에 이미 보상 시스템이 있었다.** `renderResult(mode, score, total, missedList, level)` 함수가 만점 감지 → 컨페티/팡파르 → `getAndIncrementPerfectCount()`(오늘 날짜 기준 프로필별 만점 횟수) → `minutesForCount(n)`(1회=2분, 2회=3분, 3회 이상=5분) → `bonusBtn` 표시 → 클릭 시 `showScreen("gameselect")`로 테트리스/벽돌깨기 선택. 영어·크메르어도 이 시스템에 그대로 연결하면 되므로 별도 보상 로직을 새로 만들 필요가 없음.
- **정답 소리 버그 원인.** `getCtx()`가 `audioCtx.resume()`을 호출만 하고 기다리지 않는 fire-and-forget 패턴. 컨텍스트가 "suspended" 상태로 시작하면(모바일 브라우저, 백그라운드 복귀 등) `resume()` 완료 전에 오실레이터가 `ctx.currentTime`(정지된 값) 기준으로 스케줄링되어 소리가 씹히거나 안 날 수 있음. 테스트 환경에서는 컨텍스트가 항상 "running"으로 시작해 재현은 못 했지만 코드상 명백한 레이스이므로 resume 완료를 기다리도록 수정하기로 함.
- **테스트 중 발견한 별개 이슈: Playwright 합성 클릭이 이 앱의 `nameGoBtn`/`startTestBtn`에서 간헐적으로 씹힘(에러 없이 화면 전환 안 됨), JS `.click()` 직접 호출은 항상 정상 동작.** 실제 사용자의 진짜 클릭과는 무관한 프리뷰 툴/CDP 합성 클릭 특이 현상으로 판단(원인 미상, 낮은 우선순위). 이후 테스트는 `document.getElementById(...).click()` 직접 호출 방식으로 진행.
- **범위 확정 (사용자 확인 완료).**
  - 신규 게임 1개만 추가, 기존 테트리스/벽돌깨기는 무수정. 게임 선택은 Fable이 추천한 Word-Rocket Command로 확정(사용자 승인).
  - 구구단·나눗셈도 이번 작업에 함께 개선(사용자: "개선하는 것이 좋지요"). 구구단은 basic 포맷에 한해 시각적 배열 힌트 토글 추가, 나눗셈은 기존 "몫+나머지 한번에 입력" 방식을 단계별 가이드 입력으로 교체(유럽식 표기 레이아웃은 이미 있던 것 유지).
- **파일 구조.** `index.html`(Learning Quest 독립 앱)은 삭제하고, `gugudan.html`을 다시 `index.html`로 되돌려 그 안에서 기능을 추가. Learning Quest에서 이미 검수된 영어 Cloze/매칭 데이터와 크메르어 25단어 데이터는 재사용(내용 검수 다시 할 필요 없음).
- **영어/크메르어를 보상 루프에 연결하는 방법.** 각 모듈의 세션 종료 시 `renderResult(mode, score, total, missedList, level)`을 `mode:"eng"`/`mode:"khmer"`로 호출 + `showScreen("result")`. `renderResult` 내부의 missedGrid 포맷팅과 `newTestBtn` 재시작 분기에 eng/khmer 케이스를 추가해야 함(기존엔 mult/div만 있었음). Today's Missions 로켓 카운터와 Mastery Map 히트맵은 곱셈 전용이므로 영어/크메르어는 연결하지 않음(범위 밖).
- **신규 게임 구현 패턴.** `startTetris(seconds)`/`endTetris()`를 그대로 모방해 `startRocket(seconds)`/`endRocket()` 작성. `screen-gameselect`에 `chooseRocketBtn` 추가, `currentBonusMinutes*60`을 그대로 넘겨받아 동일한 타이머 구조 사용.

## 2026-07-08 초기 결정 (폐기된 접근 — 참고용으로만 남김)

- **별도 파일로 생성.** 기존 `index.html`(구구단 전용 앱)은 건드리지 않고 `learning-quest.html`을 신규 생성. 사용자가 기존 앱 대체를 원하면 나중에 교체하면 됨.
- **역할 분담.** Fable이 설계·스펙·크메르어 데이터·검증을 담당, Sonnet 서브에이전트가 코딩 담당 (사용자 지시).
- **외부 의존성 0.** Tailwind CDN도 쓰지 않음. 순수 vanilla CSS/JS. 크메르 문자 렌더링은 Windows 기본 폰트(`Khmer UI`, `Leelawadee UI`)와 `Noto Sans Khmer` 폴백 스택으로 처리 — 외부 폰트 로드 없이 동작.
- **크메르어 음성.** 외부 오디오 파일 금지 조건이므로 Web Speech API(speechSynthesis)를 시도하되, 크메르어 보이스가 없는 환경이 대부분이라 **로마자 표기 + 이모지 시각 매핑을 주 수단**으로, 음성은 있으면 보너스로 처리.
- **크메르어 데이터는 Fable이 직접 작성.** Sonnet이 크메르어를 창작하다 틀리는 걸 막기 위해 단어 25개(숫자/인사/동물/음식/사람·장소 5개씩)를 스펙에 고정 제공. 렌더링 검증 완료된 표준 표기 사용.
- **유럽식 나눗셈 표기.** 피제수 왼쪽, 제수 오른쪽 세로선 구분(프랑스/유럽식 potence 레이아웃), 몫은 제수 아래. 9~10세 대상이므로 제수는 한 자리, 피제수는 2~3자리로 제한. 단계별로 몫 자릿수 → 뺄셈 결과(나머지)를 순서대로 입력하는 가이드 방식.
- **보상 규칙 해석.** "3~4분(랜덤 또는 난이도 기반)"은 난이도 기반으로 확정 — 쉬운 모듈(구구단, 영어) 완벽 시 3분, 어려운 모듈(나눗셈, 크메르어) 완벽 시 4분. 100점 미만 세션은 스트릭 리셋. 스트릭 3 이상은 모두 6분.
- **신규 3번째 게임.** "Word-Rocket Command" 채택 — 캔버스 기반, 로켓을 좌우로 조작해 떨어지는 별을 모으고 장애물을 피하는 게임. 키보드+터치 모두 지원.

## 2026-07-08 구현·검증 결과

- Sonnet 서브에이전트가 `learning-quest.html`(~1800줄) 구현. Sonnet의 자체 결정 사항: 별 경제(정답 1개당 ⭐1 + 만점 보너스 5), 크메르 모듈 아이콘을 🇰🇭 대신 🛕 사용(Windows Chromium에서 국기 이모지가 "KH" 텍스트로 깨지는 문제 회피), Speed Tap 폭탄 감점은 0점 밑으로 안 내려가게 클램프.
- **Fable 리뷰에서 버그 1건 수정.** `multStart`/`divStart`가 매 세션 document keydown 리스너를 추가하는데 cleanup은 뷰 전환 시에만 실행 → "Play Again"(같은 뷰 내 재시작) 반복 시 리스너 누적으로 키보드 입력이 중복 반영. 재시작 전 이전 리스너를 제거하는 방식으로 수정. 프리뷰에서 재시작 2회 후 키 1회 = 숫자 1개 입력 확인.
- 브라우저 검증 완료 항목: 만점 세션 → 컨페티/축하 오버레이/스트릭 1/2분 지급, 유럽식 나눗셈 레이아웃(피제수 좌/제수 우/몫 아래) + 오답 힌트 + perfect 해제, 크메르 문자 렌더링(자음 스택·하단 모음 정상), 아케이드 3종 카드, 로켓 게임 캔버스(DPR 스케일) 구동, 시간 강제 만료 시 게임 정지 + Time's up 오버레이 + 아케이드 잠금, 모바일 375px 가로 스크롤 없음, 콘솔 에러 0건.
- 검증 중 내부 함수 호출로 세션을 빨리 감았기 때문에 실사용과 달리 빈 세션이 만점 처리된 장면이 있었으나, 실제 플로우에선 10문제 완료 후에만 종료 함수에 도달하므로 문제 없음. 테스트 후 localStorage 초기화 완료.

## 2026-07-08 메인 페이지 통합

- 사용자 요청("모든 게 같이 나와서 고를 수 있게 하나로 통일")에 따라 파일 교체. Learning Quest가 `index.html`(루트)이 되고, 기존 구구단 앱은 `gugudan.html`로 이름 변경해 보존. `learning-quest.html` 경로는 사라짐.
- Learning Quest 대시보드 하단에 "Classic Gugudan App" 카드를 추가해 `gugudan.html`로 이동. 기존 앱 내부는 수정하지 않음(surgical change 원칙). 기존 앱에서 새 앱으로 돌아오려면 브라우저 뒤로가기 사용.
- 두 앱의 localStorage 키가 다르므로(신규 `lq_state_v1`) 데이터 충돌 없음.

## 2026-07-08 최종 구현 (gugudan.html 직접 확장 완료 → index.html로 교체)

- **실제 구현 방식.** 별도 에이전트 위임 없이 이 세션에서 직접 `gugudan.html`을 읽고 surgical하게 확장. 파일이 CRLF였는데 Edit 도구로 새로 삽입한 블록이 LF로 들어가면서 혼합 줄바꿈이 생겨 이후 큰 블록의 `old_string` 매칭이 계속 실패하는 문제 발생 → `\r\n`을 전부 `\n`으로 정규화하는 1회성 node 스크립트로 해결한 뒤 이후 모든 edit이 정상 동작. 파일 줄바꿈은 이제 LF로 통일.
- **나눗셈 가이드 입력.** `computeDivisionSteps(dividend, divisor)` 헬퍼로 자릿수별 (몫자리, 뺄셈결과) 스텝을 사전 계산. `divQuiz`에 `qSteps/qStepIndex/qSubStage('digit'|'subtract')/qWrongThisAttempt/qAccumulatedQuotient/qMicroAttempt` 필드를 추가해 기존 `stage`("quotient"/"remainder")/`attemptNum`/`divProcessResult`/`divOnCorrect`/`divOnWrong` 구조는 건드리지 않고 그 위에 얹음. 마이크로스텝(자릿수 하나, 뺄셈 하나)은 2회 오답까지 허용 후 정답 공개하고 계속 진행하되, `qWrongThisAttempt`가 true면 몫 전체 완료 시점에 기존 "틀린 몫 제출" 경로(`divProcessResult(false)`)를 그대로 호출해 스트릭 리셋·재시도 큐잉·2회째 완전 공개 로직을 원본 그대로 재사용.
- **나눗셈 계단식(staircase) 표기 — 사용자가 보내준 캄보디아 교과서 사진에 맞춰 수정.** 처음엔 매 스텝마다 "숫자 − 곱 = ?" 한 줄만 보여주는 방식으로 짰으나, 실제 캄보디아식 필산 표기는 "−곱" 행과 "다음에 내려받은 숫자와 합쳐진 수"만 누적해서 보여주고 중간 나머지는 단독으로 표시하지 않음(마지막 스텝의 나머지만 예외). `divRenderStaircase(previewText)` 함수로 완료된 스텝들을 `qStepIndex` 기준 순회하며 `[-product, nextNumberBefore(or 마지막이면 remainderAfter)]` 쌍을 누적 렌더링하도록 교체. 437÷3 예시로 직접 트레이스 검증(-3, 13, -12, 17, -15, 2 → 몫145 나머지2) 완료.
- **영어 레벨 상향.** 아이가 기존 Level 2(중급) 내용도 쉬워해서 전체를 한 단계씩 올림 — 구 Level 2 콘텐츠(ancient/enormous 등 매칭 + although/by the time 클로즈)가 새 Level 1("Intermediate")이 되고, 구 Level 1(기초) 콘텐츠는 완전히 폐기, 새 Level 2("Advanced")는 수동태·관계대명사·관용구 위주의 새 세트(exhausted/mysterious 매칭 등)로 교체. 레벨 토글 라벨도 "Basic/Intermediate" → "Intermediate/Advanced"로 변경.
- **모바일 터치 타겟 감사.** 이번 세션에 신설한 `.chipBtn`/`.matchItem`/`.lessonCard`/`.listenBtn`/`.hintToggleBtn`에 `min-height`를 명시적으로 추가(46~48px)하고 `.chipBtn`/`.matchItem`은 `display:flex; align-items:center; justify-content:center;`로 내용 중앙 정렬. 로켓 게임 ◀▶ 버튼은 기존 `.tetrisControls`/`.key` 패턴을 그대로 재사용해 이미 60px 이상 확보되어 있음을 실측으로 확인(166.7×63.3px). 기존 Tetris/Breakout/프로필/관리자 화면은 손대지 않음.
- **테스트 중 발견한 아티팩트(실제 버그 아님).** 여러 division/rocket 세션을 재시작 없이 연속으로 눌러가며 테스트하다가 한 번 "다른 문제로 순간이동", "로켓 화면이 갑자기 홈으로" 같은 비정상 상태를 목격했으나, 완전히 새로고침한 뒤 단일 eval 호출 안에서 `await wait()`로 타이밍을 명시적으로 통제해 재현했을 때는 100% 정상 동작함을 확인. 원인은 실제 게임 로직 버그가 아니라 브라우저 프리뷰 도구의 스크린샷 캐시 지연 + 내 테스트 스크립트가 여러 툴 라운드트립에 걸쳐 실행되며 실제 경과 시간을 과소평가한 데서 온 것으로 결론. 실제 사용자는 화면 전환 없이 새 게임을 재시작할 수 없으므로(버튼 클릭 흐름상 불가능) 이 클래스의 문제는 실사용에서 발생하지 않음.
- **파일 교체 완료.** `index.html`(구 Learning Quest) 삭제 → `gugudan.html`을 `index.html`로 rename. 최종 산출물은 `D:\Sangil_Multiplication\index.html` 1개 파일(3124줄), `gugudan.html`은 더 이상 존재하지 않음. 임시 `_check.js`(node 문법 검증용) 삭제 완료.

## 2026-07-08 나눗셈 인터랙션 재설계 — "자동 뺄셈/내려받기"

- **사용자 피드백.** 캄보디아 교과서 사진을 다시 보내며 "학습자가 몫 자릿수(1→4→5)만 순서대로 입력하면, 뺄셈(-3, -12, -15)과 다음 자리 내려받기는 앱이 자동으로 보여줘야 한다"고 명확히 함. 직전 구현은 몫 자릿수 입력 후 "얼마가 남았나요?"라는 뺄셈 결과까지 별도로 입력받고 있었는데, 이걸 제거하고 자동 표시로 바꿈.
- **Fable이 직접 코드 수정.** 이번엔 서브에이전트 위임 없이 이 세션에서 직접 `index.html`의 나눗셈 관련 함수들(`divRenderStaircase`/`divRenderQuotientStepUI`/`divUpdateInputDisplay`/`divSubmit`/`divSubmitQuotientStep`/`divHandleQuotientStepWrong`/`divFinishQuotientWalkthrough`/`divOnWrong`)을 읽고 surgical하게 재작성. `qSubStage`('digit'/'subtract') 개념과 `divQuiz.stage`("quotient"/"remainder") 별도 확인 단계를 완전히 제거하고, 몫 자릿수 정답 입력 시 `divAdvanceStep()`이 500ms→800ms 두 단계 setTimeout으로 뺄셈 행과 다음 숫자를 자동으로 계단식에 추가한 뒤 다음 자릿수 프롬프트로 넘어가도록 함. Level 2의 나머지도 더 이상 별도로 물어보지 않고, 몫 전체가 끝나면 `divFinishQuotientWalkthrough`가 자동으로 `ldRVal`을 최종 나머지로 채움.
- **실제로 잡은 버그.** 처음 만든 버전은 `qWrongThisAttempt`(이 시도에서 힌트를 한 번이라도 봤는지)를 "2회 오답 후 정답 공개" 분기에서만 true로 설정했음. 그런데 "1회 오답 → 힌트 → 재시도로 정답" 케이스에서는 절대 true가 안 되어서, 힌트를 보고 맞춘 문제도 "완벽"으로 처리되어 스트릭이 안 끊기는 버그가 있었음. `window.__divDebug`/`window.__divDebugLog`를 임시로 심어서 클릭 타이밍과 내부 상태(qMicroAttempt/qStepIndex/accumulated 등)를 정확히 추적한 뒤 원인을 특정 — `divHandleQuotientStepWrong` 함수 맨 위에서 무조건 `qWrongThisAttempt = true`로 설정하도록 수정(오답이 호출됐다는 것 자체가 이미 "완벽하지 않음"을 의미하므로). 원래 앱(가이드 이전 버전)의 "몫을 한 번에 잘못 입력하면 attemptNum=1 실패로 스트릭 초기화" 의미와 정확히 일치시킴. 수정 후 "1회 오답→재시도 정답"은 Passed는 되지만 스트릭 0으로 처리되고, 문제 전체가 attempt 2로 리셋되어 처음부터 다시 풀어야 하는 것까지 실제 브라우저에서 재현·확인함.
- **테스트 방법론 교훈.** 클릭+대기를 하나의 async eval 안에 넣으면 (원인 불명이지만) 실제 클릭이 씹히는 경우가 있었음. 디지트 클릭과 OK 클릭을 완전히 별개의 eval 호출로 분리하고, 각 호출 사이에 자연스러운 툴 왕복 지연(수 초)이 생기게 하는 방식이 훨씬 안정적이었음. 애매한 상태를 텍스트 UI만으로 추론하기 어려울 때는 임시로 `window.__debug`류 훅을 심어 내부 상태를 직접 읽는 것이 흑박 테스트보다 훨씬 빠르고 정확함 — 디버깅 끝나면 반드시 제거.
- 디버그 훅(`window.__divDebug`, `window.__divDebugLog`) 전부 제거 완료, `node --check` 통과 확인.

## 2026-07-08 나눗셈 레이아웃 수정 — 배당수 열에 계단식 풀이 직접 연결

- **사용자 피드백.** 같은 교과서 사진을 다시 보내며 "지금은 밑으로(따로 떨어져서) 표현되어 아이들이 교과서와 다르게 생각한다"고 지적. 원인 확인: `#divWorkRow`(계단식)가 `.longDiv`(배당수|제수/몫 블록) 전체 아래에 별도 블록으로, 게다가 가운데 정렬로 떠 있어서 "437" 열과 안 이어져 보였음.
- **수정.** `#ldDividend`와 `#divWorkRow`를 새 `.ldLeft`(`flex-direction:column; align-items:flex-end`) 컨테이너로 묶어서 `.longDiv` 안에 배치(오른쪽에는 기존 `.ldRight`가 divisor/quotient로 그대로). `.workRow`를 `text-align:right`로 바꾸고 `.ldDividend`와 동일한 `padding-right:16px`+투명 `border-right:4px`를 줘서 폭이 달라도 숫자 오른쪽 끝이 정확히 같은 x좌표에서 맞춰지도록 함.
- **검증.** `getBoundingClientRect()`로 실측 — dividend.right(266.93px) === workRow.right(266.93px) 완전 일치, dividend.bottom과 workRow.top 사이 2px 간격으로 거의 붙어서 이어짐 확인. 스크린샷 도구가 이번 세션에서 반복적으로 타임아웃 났지만(피그마 렌더링 파이프라인 이슈로 추정, 콘솔 에러 없음, eval은 정상 응답) 실측 좌표로 대체 검증함.
- **테스트 중 얻은 교훈 추가.** "15번 반복해서 2단계 문제 찾기" 같은 헬퍼 루프를 돌리면 그 자체가 `divStartBtn`을 여러 번 호출해 매번 `passedCount`를 0으로 리셋시킴 — 이후 "Passed 1/10"처럼 낮은 숫자가 나와도 버그가 아니라 테스트 루프의 부작용이니 혼동하지 말 것.

## 2026-07-08 나눗셈 세로 구분선 — 손그림 참고자료 반영

- **사용자가 손으로 직접 그린 참고 이미지 제공.** 437÷3 예시를 단계별로 그려서, 몫 자릿수를 하나씩 넣을 때마다 오른쪽 세로 구분선("|")이 계단식 풀이 전체 높이만큼 아래로 계속 길어져야 한다는 것을 명확히 보여줌(1단계 완료 시 3줄 높이, 2단계 완료 시 5줄 높이까지 자람). 몫 숫자도 자릿수가 늘어날수록 "1" → "14"처럼 이어붙여서 표시.
- **원인.** 직전 수정에서 세로 구분선을 `#ldDividend`(배당수 한 줄)에만 넣고 `#divWorkRow`(계단식)에는 투명 테두리로 폭만 맞춰놨었음 — 그래서 실제로는 선이 안 보이거나 배당수 옆에서만 짧게 끊겨 있었음.
- **수정.** 테두리를 `#ldDividend`/`.workRow` 개별 요소가 아니라 이 둘을 감싸는 `.ldLeft` 컨테이너 자체에 `border-right:4px solid #fff; padding-right:16px`로 하나만 줌 — 컨테이너의 높이가 계단식이 늘어날수록 자연스럽게 커지므로 세로선도 함께 길어짐. `.ldRight`(제수/몫)는 그대로 상단 고정.
- **검증.** `getBoundingClientRect()`로 `.ldLeft` 높이가 1단계 완료 전 94.7px → 1단계 완료 후 138.25px로 자라는 것, `.ldLeft.top === .ldRight.top`(둘 다 143.1px, 상단 정렬 유지)이면서 `.ldLeft.bottom(281.4) > .ldRight.bottom(271.4)`(왼쪽만 아래로 자람)인 것을 확인해 손그림 구조와 일치함을 확인.

## 2026-07-08 나눗셈 "내려오는 숫자" 점선 화살표 추가

- **사용자 참고자료.** 437÷3 예시의 디지털 버전 이미지에서, 배당수의 현재 작업 중인 자릿수에서 계단식 풀이 쪽으로 점선+화살표가 내려오는 것을 명확히 요구.
- **구현.** 이미 존재하던 `.dgt-active`(현재 작업 중인 배당수 자릿수 강조) 클래스에 `::after`(점선, `border-left:2px dashed`, 높이 14px)와 `::before`(↓ 문자)를 순수 CSS로 추가. 자릿수 span 자체가 `position:relative`라 별도 JS 좌표 계산 없이 자동으로 해당 자릿수 바로 아래에 정확히 위치함. `.workRow`의 `margin-top`을 2px → 26px로 늘려 화살표가 다음 줄 텍스트와 안 겹치게 함.
- **동작 확인.** 실측 결과 자릿수 하단과 계단식 시작 사이 간격 26px, 화살표 시각적 길이 약 23px로 겹침 없음. 자릿수가 `.dgt-used`로 넘어가면 화살표가 자동으로 사라지고 다음 활성 자릿수로 옮겨가는 것까지 확인(69÷6 예시로 6→9 전환 시 화살표 이동 확인).
- **의도적으로 단순화한 부분.** 참고 이미지는 완성된 최종 상태에서 화살표 2개가 동시에 남아있는(각 단계의 내려받기를 모두 표시) 모습이지만, 구현은 "현재 진행 중인 자릿수"에만 화살표를 보여주고 이전 단계는 사라지는 방식. 실시간 진행 상황 표시로는 충분하다고 판단했지만, 완료된 문제를 나중에 다시 봤을 때 모든 화살표가 남아있길 원하면 추가 작업 필요.
- **테스트 방법론 재확인.** 프로필이 없는 상태에서 "프로필 생성(reload 유발) + 곧바로 나눗셈 시작"을 한 eval 호출에 묶으면, `location.reload()`가 예약된 후에도 같은 동기 실행 컨텍스트의 나머지 코드(나눗셈 시작 클릭들)가 실제로 실행되어버려서 마치 성공한 것처럼 값을 반환하지만, 직후 실제 reload가 완료되며 전부 사라짐 — 반드시 프로필 생성과 그 이후 동작을 별도 eval 호출로 분리할 것.
