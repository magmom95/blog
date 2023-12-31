---
title: 자바스크립트 엔진 런타임 비동기 작동 방식, 동작 원리
date: '2023-12-12'
tags: ['study']
draft: false
summary: 자바스크립트 엔진 런타임 비동기 작동 방식, 동작 원리를 공부하여 정리한 글 입니다.
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

[6. 결론](#결론)

## 개요

<img
  src="https://velog.velcdn.com/images%2Fchoijw1116%2Fpost%2Fcec958c0-57ba-48f4-bcc7-a12f9efa1e08%2Fjavascript_runtime.png"
  width="100%"
  height="300"
/>
&uarr; [자바스크립트 런타임](https://development-mark.vercel.app/blog/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%20%EC%97%94%EC%A7%84)

그전 포스팅 이후 자바스크립트 런타임에 대해 보다 자세히 다룰 예정

## 자바스크립트 런타임

- JavaScript가 구동되는 환경

- 종류로는 웹 브라우저(크롬, 파이어폭스) 와 NodeJs라는 프로그램이 존재 함

#### ✨V8을 더 이해하기 위한 필요한 지식

- 자바스크립트는 싱글 스레드(single thread = 한 가닥)
  <img
    src="https://d2.naver.com/content/images/2015/06/helloworld-59361-1.png"
    width="100%"
    height="300"
  />

  - 바늘 구멍에 실을 꿰는 것 처럼 한 가지 작업을 실행하기 위해 순차적으로 진행 시켜야 함 (실제로 코드를 실처럼 이어 놓았다고 해서 유래된 이름)

  - 즉 하나의 프로그램에서 동시에 하나의 코드만 실행할 수 있다는 뜻

#### 콜 스택(Call Stack)과 메모리 힙(Memory Heap)

<img
  src="public\static\images\자바스크립트 엔진.png"
  width="100%"
  height="300"
/>

- **콜 스택** : 코드 실행에 따라 호출 스택이 쌓이는 곳. 자바스크립트 엔진은 단 하나의 호출 스택을 사용

- 메모리 힙 : 자바스크립트에서 사용되는 메모리 공간

- 콜 스택은 코드를 읽고 함수가 실행되는 순서를 기억

- 자바스크립트는 싱글 스레드 프로그래밍 언어이며 하나의 힙 영역과 하나의 콜 스택을 가지므로 한 가지 일 밖에 하지 못함

- 콜 스택은 실행 컨텍스트 과정과 매우 유사

#### 📌 자바스크립트 런타임에서 비동기 처리 방법

<img
  src="public\static\images\자바스크립트 런타임.png"
  width="100%"
  height="300"
/>

- 이벤트 루프는 이 전체 시스템에서 아주 단순한 일을 하는 작은 파트 (그러나 매우 중요)

- 이벤트 루프의 역할은 콜 스택과 콜백 큐를 주시

- 콜 스택이 비어있으면 큐의 첫 번째 콜백을 스택에 쌓아 효과적으로 실행할 수 있게 함

- 이벤트 루프는 콜 스택이 비어질 때까지 기다린 후 콜백 큐에 있는 콜백을 콜 스택에 넣어주는 역할 (싱글 스레드)

- Web API의 콜백이 완료되었다면 콜백은 큐에 쌓이게 되고, 이벤트 루프에 의해서 실행

✨이미지 처리나 애니메이션이 너무 잦아지거나 스택에 필요없이 느린 코드가 쌓이면 실행 속도의 영향이 감
