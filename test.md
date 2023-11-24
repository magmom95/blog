---
title: 자바스크립트 엔진 런타임 작동 방식, 비동기 이벤트 루프
date: "2023-11-24"
tags: ["study"]
draft: false
summary: 자바스크립트 엔진 런타임 작동 방식, 비동기 이벤트 루프를 공부하여 정리한 글 입니다.
---

![header](https://capsule-render.vercel.app/api?type=rect&color=auto&text=%20%20STUDY%20%20&fontAlign=30&fontSize=15&textBg=true&desc=강의%20내용을%20바탕으로%20나의%20경험,생각을%20작성&descAlign=60&descAlignY=50&animation=twinkling)

### ⚠ 참고 사항

```javascript

import { useState } from "react";

export default function blogPage() {
 const [블로그 내용, 내가 적은 내용] = useState('나의 주관적 생각 + 잘못 이해한 내용')
  return (
    <article>
      {블로그 내용}
      // 관련 된 내용을 내가 직접 공부하여 정리한 내용!!
      // 주관적 생각과 나의 이해를 바탕으로 적은 글이라 정확하지 않을 수 있음
      // 추후의 내용이 추가 되거나 수정 될 수 있음
    </article>
  );
}

```

# 목차

[1. 개요](#개요)

[2. 자바스크립트 런타임](#자바스크립트-런타임)

## 개요

<img
  src="https://d2.naver.com/content/images/2015/06/helloworld-59361-1.png"
  width="100%"
  height="300"
/>
&uarr; [렌더링 엔진](https://development-mark.vercel.app/blog/%EB%A0%8C%EB%8D%94%EB%A7%81%20%EC%97%94%EC%A7%84)

예전 포스팅을 통해 알게 된 자바스크립트 엔진에 관련 된 내용을 좀더 깊게 다루기 위해

https://github.com/v8/v8
https://evan-moon.github.io/2019/06/28/v8-analysis/
https://medium.com/@vdongbin/node-js-%EB%8F%99%EC%9E%91%EC%9B%90%EB%A6%AC-single-thread-event-driven-non-blocking-i-o-event-loop-ce97e58a8e21
https://hanamon.kr/javascript-%EB%9F%B0%ED%83%80%EC%9E%84-%EC%9E%91%EB%8F%99-%EB%B0%A9%EC%8B%9D-%EB%B9%84%EB%8F%99%EA%B8%B0%EC%99%80-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84/
https://velog.io/@hang_kem_0531/JS-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC

## 자바스크립트 런타임

[^1]: `Record`란 현재 컨텍스트와 관련된 식별자와 식별자에 바인딩된 값이 기록되는 공간
[^2]: `자바스크립트 엔진`란 자바스크립트 코드를 실행하는 프로그램을 말하며 주로 구글의 v8 엔진
[^3]: `선언`란 메모리 공간을 확보하고 식별자와 연결 즉 정체를 알리는 행위
[^4]: `초기화`란 별자에 암묵적으로 undefined 값으로 바인딩하는 것 즉 공간을 확보해서 거기에 undefined라는 임의의에 값을 넣는 행위
[^5]: `할당`란 을 선언된 변수에 개발자가 지정한 값을 넣는 것 즉 임의의 undefined 값을 원하는 값으로 바꾸는 행위
[^6]: `TDZ`란 일시적 사각지대(Temporal Dead Zone)
[^7]: `스코프`란 참조 대상 식별자를 찾아내기 위한 규칙으로 식별자를 구분하고 찾음
[^8]: `전역 스코프(Global scope)`란 스크립트 전체에서 참조되는 것을 의미하며, 어느 곳에서든 참조
[^9]: `지역 스코프(Local scope)`란 함수 코드 블록이 만든 스코프. 함수 자신과 하위 함수에서만 참조 가능. 블록 내부 영역(Block-level Scope)도 지역 스코프
[^10]: `LHS(left-hand side) 참조`란 변수가 대입 연산자(=)의 왼쪽에 있을 때 참조하여 값을 넣을 변수를 찾는 행위 즉 이게 함수인지 변수인지를 찾음
[^11]: `RHS(right-hand side) 참조`란 변수가 대입 연산자(=)의 왼쪽이 아닌 편에 있을 때 수행하는 참조 행위로 즉 여기에 들어 가는 값에 대한 출처를 찾음
[^12]: `중첩 스코프`란 변수를 현재 스코프에서 발견하지 못하면 엔진은 다음 바깥의 스코프로 넘어가는 방식으로 변수를 찾거나 글로벌 스코프로 불리는 가장 바깥 스코프에 도달할 때까지 반복하는 과정
[^13]: `function-level scope`란 함수 코드 블럭 내에서 선언된 변수는 함수 코드 블럭 내에서만 유효하고 함수 외부에서는 유효하지 않음 단 let,const 를 사용하면 block-level 가능
[^14]: `변수명 중복 허용`란 글로벌 영역에 변수를 선언하면 이 변수는 어느 곳에서든지 참조할 수 있는 global scope를 갖는 전역 변수가 가능
[^15]: `암묵적 전역`란 명시적으로 변수 앞에 var를 붙여주지 않으면 암묵적 전역변수가 되어 값을 참조할 수 있음
