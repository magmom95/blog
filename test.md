---
title: 비동기 작업 마스터하기: Promise, Async/Await, Axios의 효과적 활용
date: "2024-02-22"
tags: ["study"]
draft: false
summary:  다양한 비동기 프로그래밍 기법과 Axios를 활용한 효과적인 백엔드 통신에 대한 내용을 공부하여 정리한 글 입니다.
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

[2. 비동기](#비동기)

[6. 결론](#결론)

## 개요

Promise, Async/Await의 사용법, 그리고 Axios를 통한 API 호출과 응답 처리에 대한 모범 사례에 대한 내용 과 개념을 정리 함

## 비동기

- 비동기는 요청과 결과가 동시에 일어나지 않는다를 의미 (Asynchronous: 동시에 일어나지 않는)

- **작업이 독립적으로 실행되며, 완료를 기다리지 않고 다음 작업으로 진행되는 프로그래밍 패턴**

<img src="public\static\images\마이크로태스크 큐.gif" width="100%" height="300" />

&uarr; [마이크로태스크 큐](https://dev.to/lydiahallie/javascript-visualized-promises-async-await-5gke#syntax)

<details markdown="1">
<summary>🔄 마이크로태스크 큐 비동기 처리 예시 </summary>
<br>

```javascript
// 콘솔에 "Start"를 출력
console.log("Start");

const promiseExample = () => {
  return new Promise((resolve, reject) => {
    console.log("Promise started");
    // promise started 출력
    // 2초 후에 Promise가 resolve 됨.
    setTimeout(() => {
      resolve("Promise resolved!");
    }, 2000);
  });
};

// Promise를 이용한 비동기 처리
promiseExample()
  .then((result) => {
    console.log(result); // Promise가 resolve된 결과 출력
    console.log("마이크로태스크큐!"); // 마이크로태스크 큐에서 실행되는 작업 출력
  })
  .catch((error) => {
    console.error(error); // Promise에서 발생한 에러 처리
  })
  .finally(() => {
    console.log("무조건 출력"); // Promise가 성공하든 실패하든 마지막에 실행 됨 (finally 여서)
  });

// 콘솔에 "End"를 출력
console.log("End");
```

- console.log("Start")가 실행되고, promiseExample 함수가 호출

- promiseExample 함수 내에서 Promise가 생성되고, 해당 Promise는 2초 뒤에 resolve되도록 설정

- promiseExample()이 호출되면서 Promise가 생성되고, 해당 Promise는 마이크로태스크 큐에 등록

- 콜 스택이 비어지면 이벤트 루프가 마이크로태스크 큐를 확인하고, Promise의 콜백 함수가 콜 스택에 올라가 실행

- then 블록이 실행되어 Promise가 resolve된 결과 및 "마이크로태스크큐!"를 출력

- catch 블록과 finally 블록은 마이크로태스크 큐에 등록되지 않으므로 현재 단계에서 실행되지 않음

- 마이크로태스크 큐의 작업이 완료되면 이벤트 루프가 메크로 큐를 확인하고, 메크로 큐에 등록된 작업들이 콜 스택에 올라가 실행

- console.log("End")가 출력되고 프로그램이 종료

<img src="public\static\images\과정1.gif" width="100%" height="300" />

<img src="public\static\images\과정2.gif" width="100%" height="300" />

<img src="public\static\images\과정3.gif" width="100%" height="300" />

&uarr; [마이크로태스크 큐 비동기 처리 예시](https://dev.to/lydiahallie/javascript-visualized-promises-async-await-5gke#syntax)

</details>

[^1]: `블로킹`란 요청에 대한 결과를 바로 줄 수 없는 경우 그 결과를 기다리도록 하는것을 의미

<img src="public\static\images\블로킹.png" width="100%" height="300" />

[^2]: `논 블로킹`란 요청에 대한 결과를 바로 줄 수 없는 경우 그 결과를 기다리지 않음

<img src="public\static\images\논 블로킹.png" width="100%" height="300" />
