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

[3. Promise](#Promise)

[4. Async/Await](#Async/Await)

[5. Axios](#Axios)

[6. 결론](#결론)

## 개요

Promise, Async/Await의 사용법, 그리고 Axios를 통한 API 호출과 응답 처리에 대한 모범 사례에 대한 내용 과 개념을 정리 함

## 비동기

- 비동기는 요청과 결과가 동시에 일어나지 않는다를 의미 (Asynchronous: 동시에 일어나지 않는)

- **작업이 독립적으로 실행되며, 완료를 기다리지 않고 다음 작업으로 진행되는 프로그래밍 패턴**


