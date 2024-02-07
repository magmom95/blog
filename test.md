---
title: 자바스크립트 엔진 런타임 비동기 작동 방식, 동작 원리 및 블로킹
date: "2024-01-25"
tags: ["study"]
draft: false
summary: 자바스크립트 엔진 런타임 비동기 작동 방식, 동작 원리 및 블로킹를 공부하여 정리한 글 입니다.
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

[3. 논 블로킹 I/O](#논-블로킹-I/O)

[4. 싱글 스레드](#싱글-스레드)

[5. 자바스크립트 런타임](#자바스크립트-런타임)

[6. 결론](#결론)

## 개요

자바스크립트 런타임에 대한 추가적인 내용 및 개념에 대하여 자세히 다룰 예정

## 자바스크립트 런타임 (V8)

- JavaScript가 구동되는 환경

- 종류로는 웹 브라우저(크롬, 파이어폭스) 와 NodeJs라는 프로그램이 존재 함

- 비동기 처리를 하며 싱글 스레드 및 논 블로킹 처리 함

## 비동기

- 비동기는 요청과 결과가 동시에 일어나지 않는다를 의미 (Asynchronous: 동시에 일어나지 않는)

- 이와 반대로 요청을 보낸 후 해당 요청의 응답을 받아야 다음 동작을 실행

```javascript
console.log("일을 분배 해야겠다");
console.log("A: 할당 받았습니다.");
console.log("B: 할당 받았습니다.");
console.log("C: 할당 받았습니다.");
console.log("D: 할당 받았습니다.");

setTimeout(() => {
  console.log("A: 완료");
}, 2000);

setTimeout(() => {
  console.log("B: 완료");
}, 1000);

setTimeout(() => {
  console.log("C: 완료");
}, 1500);

console.log("할당 완료!");
console.log("D: 완료");
```

```javascript

비동기 과정을 통한 콘솔 결과 값

일을 분배 해야겠다.
A: 할당 받았습니다.
B: 할당 받았습니다.
C: 할당 받았습니다.
D: 할당 받았습니다.
할당 완료!
D: 완료
B: 완료
C: 완료
A: 완료

```

- setTimeout 함수 자체의 실행은 즉시 실행되고 리턴

- '일을 분배 해야겠다.' 메세지가 출력 되므로 코드가 아래까지 실행 된걸 확인가능

- 실행컨텍스트를 통해 setTimeout 함수가 즉시 종료 (예약하는일이 전부)

<img src="public\static\images\비동기.png" width="100%" height="300" />

&uarr; 시간의 흐름에 따라 작업 완료 순서

- setTimeout 에 의해 기다리는 동작은 본래의 코드 흐름과는 상관 없이 따로따로 독립적으로 돌아감 (비동기 작업)

- 비동기 방식은 순서의 따른 것이 아닌 위 그림과 같이 무작위 순서로 실행

### 비동기 성능의 장점

- 요청한 작업에 대하여 완료까지 기다리지 않고 그다음 작업을 수행 함

- 즉 멀티작업을 진행 시켜 시스템 성능 향상에 도움이 됨

### 비동기 작업이 필요한 경우

- 사용자 이벤트 처리 ex) 버튼, 스크롤 등으로 발생하는 특정 이벤트

- 네트워크 응답 처리 ex) 화면에서 서버에게 요청을 보냈을 때

- 의도적으로 시간 지연을 사용하는 기능 ex) 알람, 구글 캘린더

## 논 블로킹 I/O

- 현재 작업에 대한 결과를 어떻게 다룰것이냐에 따라 블로킹[^1] or 논 블로킹[^2]으로 나눔

- **특정 작업의 완료를 기다리는 동안(block) 프로그램의 제어권이 해당 지점에서 잠시 중단되는 상태를 누가 주체가 될것인지를 의미 함**

### 비동기? 논 블로킹 I/O?

```javascript
// 비동기
import React, { useState, useEffect } from "react";

const getData = () => {
  const [userData, setUserData] = useState(null);

  useEffect(() => {
    const fetchUserData = async () => {
      try {
        // 비동기적으로 API에 데이터를 요청
        const response = await fetch(
          "https://https://development-mark.vercel.app/user"
        );
        const userData = await response.json();
        // 상태 업데이트
        setUserData(userData);
      } catch (error) {
        console.error(error);
      }
    };
    // 컴포넌트가 마운트되면 API 요청 수행
    fetchUserData();
  }, []);

  return (
    <div>
      {userData ? (
        <p>사용자 정보: {JSON.stringify(userData)}</p>
      ) : (
        <p>데이터를 불러오는 중...</p>
      )}
    </div>
  );
};

export default getData;
```

```javascript
//논 블로킹 I/O
import React, { useState, useEffect } from "react";
import fs from "fs/promises"; // Node.js의 promise 기반 파일 시스템 모듈

const FileReadExample = () => {
  const [fileContent, setFileContent] = useState(null);

  useEffect(() => {
    const readFile = async () => {
      try {
        // 비동기적으로 API에 데이터를 요청
        const content = await fs.readFile("example.txt", "utf-8");
        // 상태 업데이트
        setFileContent(content);
      } catch (error) {
        console.error("파일을 읽는 중 에러 발생:", error);
      }
    };

    readFile();
  }, []);

  return (
    <div>
      {fileContent ? <p>파일 내용: {fileContent}</p> : <p>파일을 읽는 중...</p>}
    </div>
  );
};

export default FileReadExample;
```

- 비동기와 논 블록 I/O의 차이점을 파악하기 어려움 따라서 아래와 같이 정리 할 수 있음

<img src="public\static\images\차이점.png" width="100%" height="300" />

&uarr; 비동기와 논 블록 I/O 차이점

```javascript
//논 블로킹 과 비동기 관점에서 바라보기

import React, { useState } from "react";

const NonBlockingTimerExample = () => {
  const [message, setMessage] = useState("");

  const startTimer = () => {
    // 비동기적으로 타이머 설정
    setTimeout(() => {
      // 타이머 콜백에서 메시지 업데이트
      setMessage("타이머 완료!");
    }, 3000);

    // 다른 작업 수행 (논 블로킹)
    console.log("타이머 시작");
  };

  return (
    <div>
      <button onClick={startTimer}>타이머 시작 (논블로킹)</button>
      <p>{message}</p>
    </div>
  );
};

export default NonBlockingTimerExample;
```

- setTimeout(() => { setMessage("타이머 완료!"); }, 3000);: 이 부분은 비동기적으로 동작하는 부분

- console.log("타이머 시작");: 이 부분은 setTimeout 함수 이후에 동기적으로 실행되는 코드

- setTimeout 함수가 호출되면서 타이머가 백그라운드에서 설정되고, 동시에 다음 코드가 실행

- 입출력 작업이 논블로킹인 경우에도 일반적으로 비동기적으로 처리 (거의 혼용으로 사용)

⚠ 파일 입출력이 비동기일 때는 비동기 호출이라고 할 수 있으며, 이러한 경우에는 주로 논블로킹 입출력 I/O 모델을 사용 (시점과 관련된 이론적인 개념이라 애매..함) 대부분 비동기 처리가 이러한 구조

#### 즉 자바스크립트는 비동기적으로 동작하면서 논블로킹(Non-blocking) I/O 모델을 채택

## 싱글 스레드

- 자바스크립트는 싱글 스레드(single thread = 한 가닥) 기반 언어

  <img src="public\static\images\싱글 스레드.png" width="100%" height="300" />

  - 바늘 구멍에 실을 꿰는 것 처럼 한 가지 작업을 실행하기 위해 순차적으로 진행 시켜야 함 (실제로 코드를 실처럼 이어 놓았다고 해서 유래된 이름)

#### 싱글 스레드인데 비동기 처리를 어떻게 할까?

- 싱글 스레드는 한가지 일밖에 못하지만 그러나 비동기 처리는 동시에 일을 처리함

- 구조적으로 자바스크립트는 싱글 스레드인데 비동기 처리를 할 수 없음

- 그러나 **자바스크립트 런타임(실행환경)으로 인하여 자바스크립트는 싱글 스레드지만 비동기처리가 가능 하게 됨**

- 자바스크립트 런타임에 구성요소로 크게 V8엔진(콜 스택(Call Stack)과 메모리 힙(Memory Heap)), 웹 API, 콜백 큐 또는 테스크 큐 (Callback Queue 또는 Task Queue) 및 이벤트 루프 (Event Loop) 로 구성 됨

#### 콜 스택(Call Stack)과 메모리 힙(Memory Heap)

<img src="public\static\images\비동기 처리 방법.png" width="100%" height="300" />

&uarr; [자바스크립트 런타임](https://development-mark.vercel.app/blog/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%20%EC%97%94%EC%A7%84)

- 하나의 힙 영역과 하나의 콜 스택을 가짐 , 즉 **동적으로 할당되는 데이터와 함수 호출의 실행 흐름을 각각 담당하는 중요한 개념**

- **콜 스택** : 코드 실행의 흐름을 추적하고 관리하는 공간으로, 함수 호출이 스택에 쌓이고 순서대로 실행

- 메모리 힙 : 동적으로 할당되는 메모리의 공간으로, 체, 배열, 함수 등이 저장되며, 런타임 중에 크기가 동적으로 조정 함

  - 메모리 힙은 가비지 컬렉션과 같은 메모리 관리 작업을 수행

- 콜 스택은 실행 컨텍스트를 푸시하고 팝하여 코드의 실행을 제어함

  - 실행 컨텍스트는 함수가 실행될 때 생성되며, 해당 함수의 환경 및 변수 정보가 있어 코드의 실행을 협력 및 관리 함

- 콜 스택과 메모리 힙으로 인하여 동시에 여러작업이 아닌 싱글 스레드로 동작 하게 됨

  - 만약 싱글 스레드로 동작 하지 않게 된다면 여러 개의 작업에서 메모리 힙에 동시에 접근하여 데이터를 수정하려고 할 수 있으며 이는 경쟁 조건(Race Condition)[^4] ,데드락(Deadlock)[^5] , 데이터 일관성(Data Consistency Issues)[^6] 등의 문제가 발생 할 가능성이 있음

#### 웹 API

- 웹 브라우저 환경에서 동작하는 자바스크립트은 브라우저가 제공하는 다양한 API들과 함께 동작하여 웹 애플리케이션을 구현

- 비동기적인 작업이나 브라우저 기능에 접근하기 위해 사용되며, 이러한 API들은 브라우저 환경에서 제공하는 다양한 기능들을 활용하는 데 중요한 역할을 함

<details markdown="1">
<summary>웹 API 종류</summary>
<br>

- DOM API는 문서의 구조를 조작

- XMLHttpRequest 및 Fetch API는 서버와의 통신을 담당

- Web Storage API는 클라이언트 측 데이터를 저장

- Web Workers API는 백그라운드에서 병렬로 작업을 수행할 수 있게 함

- Canvas 및 WebGL API는 그래픽 작업을 다루는 데 사용 됨

- Geolocation API는 사용자의 위치 정보에 접근하여 위치 기반 서비스를 제공

</details>

#### 콜백 큐(Callback Queue) 또는 테스크 큐(Task Queue)

- **콜백 큐(Callback Queue)** : 비동기 작업의 결과 또는 완료된 이벤트를 임시로 저장하는 자료구조로, 웹 브라우저 환경에서 자바스크립트 비동기 처리의 핵심 부분

- 비동기 작업 (Promise, async/await)이 완료되면 해당 작업의 콜백 함수 또는 이벤트가 테스크 큐에 추가

- 이벤트 루프와 상홍작용하여 콜백 큐(Callback Queue) 또는 테스크 큐(Task Queue)에 추가된 작업들은 이벤트 루프를 통해 콜 스택으로 이동하여 실행

  - 콜 스택이 비어질 때마다 테스크 큐에서 다음 작업이 콜 스택으로 이동되어 실행 됨

- 자바스크립트 엔진이 비동기 작업을 효과적으로 처리할 수 있도록 돕는 중요한 메커니즘

  - 비동기 작업의 완료와 관련된 콜백 함수들을 순서에 맞게 저장하고 실행함으로써 웹 애플리케이션은 더 매끄럽게 동작시키게 도와줌

#### 🚩 이벤트 루프 (Event Loop)

- **이벤트 루프 (Event Loop)** : 자바스크립트의 비동기 작업 및 이벤트 처리를 관리하는 핵심 메커니즘 (제일 중요한 역할)

  - 현재 실행 중인 코드를 추적하는 콜 스택을 관리하고 함수 호출 시 스택에 쌓이고, 함수가 반환되면 스택에서 제거 이를 통해 비동기 작업과 이벤트 처리가 조절

  - 비동기 작업이나 이벤트 발생 시 실행될 콜백 함수들을 저장하는 큐백 큐를 관리 즉 비동기 작업이 완료되면 해당 작업에 등록된 콜백 함수를 저장하는데 사용

  - 이벤트 루프는 계속해서 콜 스택이 비어질 때까지 대기하며 비어지면 콜백 큐에 있는 콜백을 콜 스택에 추가하여 실행

  - 타이머 함수(setTimeout), 이벤트 핸들러, HTTP 요청 등과 같은 비동기 작업이 완료되면 해당 작업에 등록된 콜백 함수를 콜백 큐에 추가하여 이후 콜 스택이 비어질 때까지 기다렸다가 콜백을 콜 스택에 추가

## 자바스크립트 런타임

- **자바스크립트 런타임에서 비동기 처리는 이벤트 루프를 중심으로 이루어 짐**

  - 코드 실행 중에 함수가 호출되면 해당 함수의 프레임이 콜 스택에 추가 후 함수가 완료되면 프레임이 스택에서 제거

  - 브라우저 환경에서 비동기 함수가 호출되면 해당 작업은 Web API에서 백그라운드에서 수행

  - 비동기 작업이 완료되면 해당 작업에 등록된 콜백 함수가 콜백 큐에 추가

  - 이벤트 루프는 주기적으로 콜 스택과 콜백 큐를 확인하고 콜 스택이 비어지면 이벤트 루프가 콜백 큐에서 콜백 함수를 가져와 콜 스택에 추가하여 실행

  - 메모리 힙은 객체와 변수가 할당되는 공간이며 비동기 작업에서 필요한 값이나 콜백 함수도 메모리 힙에 저장

<img src="https://simonzhlx.github.io/images/async-code-exe.gif" width="100%" height="300" />

&uarr; [자바스크립트 런타임 비동기 처리 방법](https://simonzhlx.github.io/js-engine/)

<details markdown="1">
<summary>🔁 자바스크립트 비동기 처리 예시 </summary>  
<br>

```javascript
// 콘솔에 "Start"를 출력
console.log("Start");

// fetchData 함수는 2초 뒤에 "데이터 형성"을 반환하는 Promise를 생성
const fetchData = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      // Promise가 성공적으로 처리되면 resolve를 호출하여 결과값을 반환
      resolve("데이터 형성");
    }, 2000);
  });
};

// fetchData 함수를 호출하고, Promise의 결과를 처리
fetchData()
  // 첫 번째 then 블록은 Promise가 성공적으로 처리되면 실행
  .then((data) => {
    // "데이터 형성"을 출력
    console.log(data);
    // "성공"를 반환하여 다음 then 블록으로 값을 전달
    return "성공";
  })
  // 두 번째 then 블록은 첫 번째 then 블록에서 반환된 값으로 실행
  .then((additionalData) => {
    // "결과: 성공 " 출력
    console.log("결과:", additionalData);
  });

// 콘솔에 "End"를 출력
console.log("End");
```

- console.log("Start")이 콜 스택에 추가되고 실행

- fetchData() 함수가 호출되면, Promise를 반환하고 해당 함수는 콜 스택에서 제거됩니다. 비동기적으로 2초 후에 resolve가 호출

- console.log("End")가 콜 스택에 추가되고 실행

- 2초 후, Promise의 작업이 완료되고, 콜백 함수가 콜백 큐에 추가

- 이벤트 루프가 콜 스택이 비어질 때까지 기다렸다가, 콜백 큐의 첫 번째 콜백 함수를 콜 스택에 추가하여 실행

- 첫 번째 then 블록이 실행되어 "데이터 형성"이 출력되고, 두 번째 then 블록으로 넘어 감

- 두 번째 then 블록이 실행되어 "결과: 성공"가 출력 됨

</details>

✨ 자바스크립트는 기본적으로 싱글 스레드로 동작하지만, 이벤트 루프를 통해 비동기 작업을 효과적으로 처리할 수 있음

✨ **이벤트 루프는 하나의 스레드에서 콜 스택을 처리하면서 동시에 비동기 작업을 관리하여 멀티스레드처럼 동작하는 것처럼 보이게 만듭**

#### "매크로태스크 큐(Macro Task Queue)"와 "마이크로태스크 큐(Microtask Queue)"

- 실제론 태스크큐 (콜백 큐)는 "매크로태스크 큐(Macro Task Queue)"와 "마이크로태스크 큐(Microtask Queue)"로 나뉨

- 매크로태스크 큐(Macro Task Queue)

  - 주로 일반적인 비동기 작업들이 해당 큐에 추가 됨 ex) 이벤트 핸들러, HTTP 요청,

  - 이벤트 루프에서 콜 스택이 완전히 비워질 때마다 처리

- 마이크로태스크 큐(Microtask Queue)

  - 주로 Promise와 관련된 작업들이나 Async/Await을 사용한 작업들이 해당 큐에 추가 됨 ex) Promise가 resolve 또는 reject 되었을 때의 콜백 함수 등

  - 마이크로태스크 큐의 작업들은 매크로태스크 큐의 작업보다 더 높은 우선순위를 갖으며 콜 스택이 비어있을 때마다 먼저 실행 즉 우선순위가 높아 먼저 실행  

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

[^3]: `스레드`란 하나의 프로세스 안에서 작업을 담당하는 최소 실행 단위 ex) 크롬 브라우저(=프로세스)에서 유투브 음악듣기(스레드1), 코딩 짜기(스레드2)
[^4]: `경쟁 조건`란 여러 스레드가 동시에 하나의 자원에 접근하여 값을 변경하려고 할 때, 어떤 스레드가 먼저 자원에 접근하여 변경할지 확실하지 않은 상태. 이로 인해 예측할 수 없는 결과가 발생할 수 있음
[^5]: `데드락`란 여러 스레드가 서로의 자원을 기다리며 상호적으로 블록되어 있는 상태. 이는 더 이상 진행이 불가능하고 해결하기 어려운 상황을 의미 함
[^6]: `데이터 일관성 문제`란 여러 스레드에서 동시에 메모리를 수정할 경우, 각 스레드 간의 데이터 일관성이 깨질 수 있음
