---
title: 자바스크립트 엔진 런타임 비동기 작동 방식, 동작 원리 및 블로킹
date: '2024-01-25'
tags: ['study']
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

[4. 싱글스레드](#싱글스레드)

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
console.log('일을 분배 해야겠다')
console.log('A: 할당 받았습니다.')
console.log('B: 할당 받았습니다.')
console.log('C: 할당 받았습니다.')
console.log('D: 할당 받았습니다.')

setTimeout(() => {
  console.log('A: 완료')
}, 2000)

setTimeout(() => {
  console.log('B: 완료')
}, 1000)

setTimeout(() => {
  console.log('C: 완료')
}, 1500)

console.log('할당 완료!')
console.log('D: 완료')
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
import React, { useState, useEffect } from 'react'

const getData = () => {
  const [userData, setUserData] = useState(null)

  useEffect(() => {
    const fetchUserData = async () => {
      try {
        // 비동기적으로 API에 데이터를 요청
        const response = await fetch('https://https://development-mark.vercel.app/user')
        const userData = await response.json()
        // 상태 업데이트
        setUserData(userData)
      } catch (error) {
        console.error(error)
      }
    }
    // 컴포넌트가 마운트되면 API 요청 수행
    fetchUserData()
  }, [])

  return (
    <div>
      {userData ? <p>사용자 정보: {JSON.stringify(userData)}</p> : <p>데이터를 불러오는 중...</p>}
    </div>
  )
}

export default getData
```

```javascript
//논 블로킹 I/O
import React, { useState, useEffect } from 'react'
import fs from 'fs/promises' // Node.js의 promise 기반 파일 시스템 모듈

const FileReadExample = () => {
  const [fileContent, setFileContent] = useState(null)

  useEffect(() => {
    const readFile = async () => {
      try {
        // 비동기적으로 API에 데이터를 요청
        const content = await fs.readFile('example.txt', 'utf-8')
        // 상태 업데이트
        setFileContent(content)
      } catch (error) {
        console.error('파일을 읽는 중 에러 발생:', error)
      }
    }

    readFile()
  }, [])

  return <div>{fileContent ? <p>파일 내용: {fileContent}</p> : <p>파일을 읽는 중...</p>}</div>
}

export default FileReadExample
```

- 비동기와 논 블록 I/O의 차이점을 파악하기 어려움 따라서 아래와 같이 정리 할 수 있음

<img src="public\static\images\차이점.png" width="100%" height="300" />

&uarr; 비동기와 논 블록 I/O 차이점

```javascript
//논 블로킹 과 비동기 관점에서 바라보기

import React, { useState } from 'react'

const NonBlockingTimerExample = () => {
  const [message, setMessage] = useState('')

  const startTimer = () => {
    // 비동기적으로 타이머 설정
    setTimeout(() => {
      // 타이머 콜백에서 메시지 업데이트
      setMessage('타이머 완료!')
    }, 3000)

    // 다른 작업 수행 (논 블로킹)
    console.log('타이머 시작')
  }

  return (
    <div>
      <button onClick={startTimer}>타이머 시작 (논블로킹)</button>
      <p>{message}</p>
    </div>
  )
}

export default NonBlockingTimerExample
```

- setTimeout(() => { setMessage("타이머 완료!"); }, 3000);: 이 부분은 비동기적으로 동작하는 부분

- console.log("타이머 시작");: 이 부분은 setTimeout 함수 이후에 동기적으로 실행되는 코드

- setTimeout 함수가 호출되면서 타이머가 백그라운드에서 설정되고, 동시에 다음 코드가 실행

- 입출력 작업이 논블로킹인 경우에도 일반적으로 비동기적으로 처리 (거의 혼용으로 사용)

⚠ 파일 입출력이 비동기일 때는 비동기 호출이라고 할 수 있으며, 이러한 경우에는 주로 논블로킹 입출력 I/O 모델을 사용 (시점과 관련된 이론적인 개념이라 애매..함) 대부분 비동기 처리가 이러한 구조

#### 즉 자바스크립트는 비동기적으로 동작하면서 논블로킹(Non-blocking) I/O 모델을 채택

## 싱글스레드

- 자바스크립트는 싱글 스레드(single thread = 한 가닥) 기반 언어

  <img src="public\static\images\싱글 스레드.png" width="100%" height="300" />

  - 바늘 구멍에 실을 꿰는 것 처럼 한 가지 작업을 실행하기 위해 순차적으로 진행 시켜야 함 (실제로 코드를 실처럼 이어 놓았다고 해서 유래된 이름)

#### 싱글스레드인데 비동기 처리를 어떻게 할까?

- 싱글스레드는 한가지 일밖에 못하지만 기본적인 비동기 처리는 동시에 일을 처리 하기때문에 자바스크립트는 이 두가지 성질을 갖는것이 개념적으로는 불가능 함

🚩 **V8엔진으로 인해 싱글스레드지만 비동기처리가 가능 하게 됨**

#### 콜 스택(Call Stack)과 메모리 힙(Memory Heap)

<img src="public\static\images\비동기 처리 방법.png" width="100%" height="300" />

&uarr; [자바스크립트 런타임](https://development-mark.vercel.app/blog/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%20%EC%97%94%EC%A7%84)

- **콜 스택** : 코드 실행에 따라 호출 스택이 쌓이는 곳. 자바스크립트 엔진은 단 하나의 호출 스택을 사용

- 메모리 힙 : 자바스크립트에서 사용되는 메모리 공간

- 콜 스택은 코드를 읽고 함수가 실행되는 순서를 기억

- 자바스크립트는 싱글 스레드 프로그래밍 언어 (한 가지 일 밖에 하지 못함)

- 하나의 힙 영역과 하나의 콜 스택을 가짐

- 콜 스택은 실행 컨텍스트 과정과 매우 유사

#### 📌 자바스크립트 런타임에서 비동기 처리 방법

- 이벤트 루프는 이 전체 시스템에서 아주 단순한 일을 하는 작은 파트 (그러나 매우 중요)

- 이벤트 루프의 역할은 콜 스택과 콜백 큐를 주시

- 콜 스택이 비어있으면 큐의 첫 번째 콜백을 스택에 쌓아 효과적으로 실행할 수 있게 함

- 이벤트 루프는 콜 스택이 비어질 때까지 기다린 후 콜백 큐에 있는 콜백을 콜 스택에 넣어주는 역할 (싱글 스레드)

- Web API의 콜백이 완료되었다면 콜백은 큐에 쌓이게 되고, 이벤트 루프에 의해서 실행

✨이미지 처리나 애니메이션이 너무 잦거나 스택에 영향이 가는 느린 코드가 쌓이면 실행 속도의 영향이 감

[^1]: `블로킹`란 요청에 대한 결과를 바로 줄 수 없는 경우 그 결과를 기다리도록 하는것을 의미

<img src="public\static\images\블로킹.png" width="100%" height="300" />

[^2]: `논 블로킹`란 요청에 대한 결과를 바로 줄 수 없는 경우 그 결과를 기다리지 않음

<img src="public\static\images\논블로킹.png" width="100%" height="300" />

[^3]: `스레드`란 하나의 프로세스 안에서 작업을 담당하는 최소 실행 단위 ex) 크롬 브라우저(=프로세스)에서 유투브 음악듣기(스레드1), 코딩 짜기(스레드2)
