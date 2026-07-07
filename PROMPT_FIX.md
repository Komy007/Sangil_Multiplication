# 수정 지시서: index.html 사소한 결함 3건 수정

`D:\Sangil_Multiplication\index.html` (단일 파일 웹앱, 순수 HTML/CSS/Vanilla JS)에서
아래 3건만 수정하라. **다른 코드는 절대 건드리지 말 것. 최소 diff 원칙.**

수정 전 반드시 해당 함수 전체를 Read로 읽고 정확한 위치를 확인한 뒤 편집하라.

## 1. `startQuiz` 상태 객체 초기화 보완 (약 1061행)

현재:
```js
queue: facts.map(function(f){ return {a:f.a, b:f.b}; }),
```
수정:
```js
queue: facts.map(function(f){ return {a:f.a, b:f.b, forceBasic:false}; }),
```

그리고 같은 객체 리터럴의 `practiceMode: !!practiceMode` 뒤에 `currentQ: null` 필드를 추가하라:
```js
practiceMode: !!practiceMode,
currentQ: null
```

이유: `onWrongAnswer`가 재출제 시 `{a, b, forceBasic:true}`를 push하므로 queue 항목 형태를
통일하고, `quiz.currentQ`를 명시적으로 초기화하기 위함. 동작 변경 없음.

## 2. 연습 모드(Practice Missed Ones)는 기본형으로만 출제

`nextQuestion` 함수(약 1141행)에서 `buildQuestion` 호출부:

현재:
```js
quiz.currentQ = buildQuestion({a:item.a, b:item.b}, item.forceBasic);
```
수정:
```js
quiz.currentQ = buildQuestion({a:item.a, b:item.b}, item.forceBasic || quiz.practiceMode);
```

이유: 오답 복습(연습 모드)은 암기 확정이 목적이므로 `7 × 8 = ?` 기본형으로만 낸다.
일반 시험 20문제의 형식 랜덤 출제는 그대로 유지된다.

## 3. 나눗셈 화면 이탈 시 상태 정리

`divBackBtn` 클릭 핸들러(약 1807행):

현재:
```js
document.getElementById("divBackBtn").addEventListener("click", function(){
  showScreen("home");
  renderHome();
});
```
수정:
```js
document.getElementById("divBackBtn").addEventListener("click", function(){
  divQuiz = null;
  showScreen("home");
  renderHome();
});
```

이유: 진행 중이던 나눗셈 세션 상태가 잔존하지 않도록 명시적으로 버린다.

## 완료 기준
1. 수정 후 JS 문법 검증:
   ```
   node -e "const fs=require('fs');const m=fs.readFileSync('index.html','utf8').match(/<script>([\s\S]*)<\/script>/);new Function(m[1]);console.log('SYNTAX OK')"
   ```
2. 세 군데 외에 diff가 없는지 `git diff`로 확인하고 결과를 보고하라.
3. 커밋하지 말 것.
