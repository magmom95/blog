---
title: '프론트의 기본 by 김태곤'
date: '2023-11-14'
lastmod: '2023-11-14'
tags: ['study']
draft: false
summary: '"The RED : 프론트엔드 Back to the Basics : 지속 가능한 코드작성과 성능 향상법 by 김태곤" 강의를 듣고 정리한 글 입니다.'
---

![header](https://capsule-render.vercel.app/api?type=rect&color=auto&text=%20%20STUDY%20%20&fontAlign=30&fontSize=15&textBg=true&desc=강의%20내용을%20바탕으로%20나의%20경험,생각을%20작성&descAlign=60&descAlignY=50&animation=twinkling)

### ⚠ 참고 사항

```javascript

 import { useState } from "react";

export default function blogPage() {
 const [강의 내용, 내가 적은 내용] = useState('나의 주관적 생각 + 잘못 이해한 내용')
  return (
    <article>
      {강의 내용}
      // 내돈 내산 강의!!
      // 주관적 생각과 나의 이해를 바탕으로 적은 글이라 강의랑 내용이 다를 수 있음
      // 추후의 내용이 추가 되거나 수정 될 수 있음
    </article>
  );
}

```

📌 강의에 나온 웹페이지 로드 과정 및 렌더링 엔진 동작 과정 자바스크립트 관련 된 내용은 다음 포스팅에 작성 할 예정

## 본 이야기에 들어가기 전에

어쩌다 시간이 흘러 2년차 개발자가 된 지금  
지금이라도 공부를 해야 겠다는 마음을 담아 블로그를 만들었으며  
공부한 내용은 STUDY 에 정리 할 예정

## 정체되지 않는 프론트엔드 개발자의 일하는 방식

<img
  src="https://storage.googleapis.com/static.fastcampus.co.kr/prod/uploads/202307/102502-1040/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C-%EB%A1%9C%EB%93%9C%EB%A7%B5-%EA%B9%83%ED%97%88%EB%B8%8C.png"
  width="100%"
  height="300"
/>

&uarr; [karanahmedse의 프론트엔드 로드맵](https://github.com/Han-Kyeol/developer-roadmap-kr-)

개발자가 되기 이전에 개발자가 되려면 어떻게 해야하지? 에 대한 의문과 호기심을 갖고 검색을 하였을 때 한번 씩은 봤을 로드맵이다.  
그러나 저 로드맵에 나온 기술을 모든 사람들이 다 알고 있을 수는 없다. (실제로 나도 1,2개 밖에...)  
여튼 김태곤 강사님도 이렇게 많은 범위에 공부를 하기 위해서는  
**"넓게 배우고 필요한 것은 깊게 공부해라"** 라고 말씀 하셨고 이를 정리 하면

- 공부할 범위를 축소하라.

- 공식 문서를 읽어라.

- 한 번에 완벽하게 다 읽기보다는 자주 읽어라.

- 새로 만들고 싶은 프로젝트 주제를 평소에 미리미리 담아두고 새로운 기술이 나오면 하나씩 적용해서 써보자.

- 어느정도 알고 있는 기술은 글로 남겨두어라.

- 코드를 복붙을 하더라도 그 안에서 원리를 이해하고 조금씩 부숴보면서 내 것으로 만들 것.

#### ✨ 내 생각

- 코드를 많이 봐라. 아마 회사에 처음 입사를 하게 된다면 회사 코드를 많이 봐야 금방 적응을 할 수 있을 것 이며 나중에 왜 코드를 많이 보라하는지 이유를 알 게 될 것이다.

- 룰을 지켜라! 합의되지 않은 건 쓰지도 마라. 회사에 있는 프로젝트는 개인 프로젝트가 아니다! 만약 새로운 기술 및 방법을 도입하고 싶다면 팀원들간의 협의 및 설득을 해야하고 그게 어렵다면 개인프로젝트를 통해 사용하는 것을 추천한다.

- 회사는 결국엔 **돈을 주고 나의 능력과 경험을 구매한 것이다.** 심지어 새로운 기술 사용 했을 때 워낙 프로젝트 사이즈가 커서 어디의 영향이 가는지 파악을 못하여 `해피 패스`[^1]로 이슈를 놓지는 경우도 너무 많다. 그렇기에 회사는 보수적일 수 밖에 없다.

- 잘 아는 기술이면 블로그나 말로 설명해라! 내가 100% 이해를 했다면 블로그나 남에게 설명할 수 있어야 진정으로 이해한 것이라 본다. **짧고 정확하게 누구나 알수있게!!** 라는 마음을 갖자.

## Back to the Basics : 프레임워크보다 기본기

**명확한 근거 : 모든 결정에는 구체적인 이유를 두자**

- 왜 그 기술을 사용했는지에 대하여 알고있어야 함

- 또한 코딩도 왜 이렇게 구조를 했는지에 대한 근거가 있어야함

- 동작 과정 알면 좋다

#### ✨ 내 생각

- 강의에 나온 웹페이지 로드 과정 및 렌더링 엔진 동작 과정 자바스크립트 문법은 다음 포스팅에 작성 할 예정

- 위에서 말했듯이 회사는 보수적 일 수 밖에 없는 구조이므로 모든 사항에 대해서는 논리적으로 말 할 수 있어야 함

- 요즘에 ChatGPT가 코드도 짜주고 설계도 해주지만 ChatGPT보고 만들어줘 보단 자신의 코드를 보여줘서 보강을 하는것을 추천함  
  **최소한 ChatGPT가 만들어 준 코드는 이해하고 써라**

## 10년 이상 쓸 수 있는 지속 가능한 코드 작성

##### 🚩 좋은 코드란 무엇일까?

- 간결하고 이해되는 코드가 좋음 ( 주석다는 코드도 좋기는 하나 코드만 읽고도 이해 되는 코드를 만들어야 함)

- 의존성이 적은 코드 ex) [leftpad](https://www.bloter.net/news/articleView.html?idxno=22900) [^2]

- 하나의 함수, 클래스, 메소드는 명확한 하나의 책임만 지게 해야 함

- 접속사 없이 하나의 문장으로 설명할 수 있는가?

##### 🚩 디버깅

- 정상 시스템의 동작, 기대하는 동작을 정의

- 근본적인 원인을 알 때까지 충분한 데이터 수집. 증상이 아니라 원인을 해결

- 시스템마다 미묘하게 다른 차이가 버그를 발생할 수 있다.  
  ex) 웹 브라우저 환경의 차이, 안 좋은 인터넷 환경

##### 🚩 리팩토링

- 비슷한 작업을 세 번째 할 때 리팩토링을 해야 함

- 리팩토링을 하면서 기능이 그대로 유지되지 않은 경우 이는 리팩토링이라고 볼 수 없음

- 테스트가 없는 리팩토링은 끔찍 함

#### ✨ 내 생각

- 강의에 나온 웹페이지 로드 과정 및 렌더링 엔진 동작 과정 자바스크립트 문법은 다음 포스팅에 작성 할 예정

## Pliny

A large reason for the popularity of the template was its customizability and integration with other services from analytics providers to commenting solutions. However, this means that a lot of boilerplate code has to be co-located within the template even if the user does not use the feature. Updates and bug fixes had to be copied manually to the user's codebase.

To solve this, I have abstracted the logic to a separate repository - [Pliny](https://github.com/timlrx/pliny). Pliny provides out of the box Next.js components to enhance static sites:

- Analytics
  - Google Analytics
  - Plausible Analytics
  - Simple Analytics
  - Umami Analytics
  - Posthog
- Comments
  - Disqus
  - Giscus
  - Utterances
- Newsletter (uses Next 13 API Routes)
  - Buttondown
  - Convertkit
  - Email Octopus
  - Klaviyo
  - Mailchimp
  - Revue
- Command palette search with tailwind style sheet
  - Algolia
  - Kbar (local search)
- UI utility components
  - Bleed
  - Newsletter / Blog Newsletter
  - Pre / Code block
  - Table of Contents

Choose your preferred service by modifying `siteMetadata.js` and changing the appropriate fields. For example to change from Umami Analytics to Plausible, we can change the following fields:

```diff-js:siteMetadata.js
analytics: {
-   umamiAnalytics: {
-     // We use an env variable for this site to avoid other users cloning our analytics ID
-     umamiWebsiteId: process.env.NEXT_UMAMI_ID, // e.g. 123e4567-e89b-12d3-a456-426614174000
-   },
+    plausibleAnalytics: {
+      plausibleDataDomain: '', // e.g. tailwind-nextjs-starter-blog.vercel.app
+    },
},
```

Changes in the configuration file gets propagated to the components automatically. No modification to the template is required.

Under the hood, Pliny exports high level components such as `<Analytics analyticsConfig={analyticsConfig}/>` and `<Comments commentsConfig={commentsConfig}/>` which takes in a configuration object and renders the appropriate component. Since the layouts are defined on the server side, Next.js is able to use the configuration object to determine which component to render and send only the required component bundle to the client.

## New Search Component

What's a blog in 2023 without a command palette search bar?

One of the most highly requested features have been added 🎉! The search component supports 2 search providers - Algolia and Kbar local search.

### Algolia

[Algolia Docsearch](https://docsearch.algolia.com/) is popular free service used across many documentation websites. It automatically scrapes the website that has is submitted for indexing and makes the search result available via a beautiful dialog modal. The pliny component is greatly inspired by the Docusaurus implementation and comes with a stylesheet that is compatible with the Tailwind CSS theme.

### Kbar

[Kbar](https://github.com/timc1/kbar) is a fast, portable, and extensible cmd+k interface. The pliny implementation uses kbar to create a local search dialog box. The component loads a JSON file, default `search.json`, that was created in the contentlayer build process. Try pressing ⌘-k or ctrl-k to see the search bar in action!

## Styling and Layout Updates

### Theming

`tailwind.config.js` has been updated to use tailwind typography defaults where possible and to use the built-in support for dark mode via the `prose-invert` class. This replaces the previous `prose-dark` class and configuration.

The primary theme color is updated from `teal` to `pink` and the primary gray theme from `neutral` to `gray`.

Inter is now replaced with Space Grotesk as the default font.

### New Layouts

Layout components available in the `layouts` directory, provide a simple way to customize the look and feel of the blog.

The downside of building a popular template is that you start seeing multiple similar sites everywhere 😆. While users are encouraged to customized the layouts to their liking, having more layout options that are easily switchable promotes diversity and perhaps can be a good starting point for further customizations.

In v2, I added a new post layout - `PostBanner`. It features a large banner image and a centered content container. Check out "[Pictures of Canada](/blog/pictures-of-canada)" blog post which has been updated to use the new layout.

The default blog listing layout has also been updated to include a side bar with blog tags. The search bar in the previous layout has been replace with the new command palette search. To switch back to the old layout, simply change the pages that use the `ListLayoutWithTags` component back to the original `ListLayout`.

## Migration Recommendations

Due to the large changes in directory structure, setup and tooling, I recommend starting from a fresh template and copying existing content, followed by incrementally migrating changes over to the new template.

Styling changes should be relatively minor and can be copied over from the old `tailwind.config.js` to the new one. If copying over, you might need to add back the `prose-dark` class to components that opt into tailwind typography styling. Do modify the font import in the root layout component to use the desired font of choice.

Changes to the MDX processing pipeline and schema can be easily ported to the new Contentlayer setup. If there are changes to the frontmatter fields, you can modify the document type in `contentlayer.config.ts` to include the new fields. Custom plugins can be added to the `remarkPlugins` and `rehypePlugins` properties in the `makeSource` export of `contentlayer.config.ts`.

Markdown layouts are no longer sourced automatically from the `layouts` directory. Instead, they have to be specified in the `layouts` object defined in `blog/[...slug]/page.tsx`.[^3]

To port over larger components or pages, I recommend first specificing it as a client component by using the `"use client"` directive. Once it renders correctly, you can split the interactive components (parts that rely on `use` hooks) as a client component and keep the remaining code as a server component. Consult the comprehensive Next.js [migration guide](https://nextjs.org/docs/app/building-your-application/upgrading/app-router-migration#migrating-from-pages-to-app) for more details.

## Conclusion

I hope you enjoy the new features and improvements in V2. If you have any feedback or suggestions, feel free to open an issue or reach out to me on [Twitter](https://twitter.com/timlrx).

## Support

Using the template? Support this effort by giving a star on GitHub, sharing your own blog and giving a shoutout on Twitter or be a project [sponsor](https://github.com/sponsors/timlrx).

## Licence

[MIT](https://github.com/timlrx/tailwind-nextjs-starter-blog/blob/main/LICENSE) © [Timothy Lin](https://www.timrlx.com)

[^1]:
    `해피 패스`는 '아무것도 잘못되지 않는 사용자 시나리오'를 의미한다. 이는 사람의 실수, 엣지 케이스, 의도를 벗어난 행동을 포함하지 않기 때문에 이 시나리오 대로 테스트를 수행하면 이슈나 버그가 발생할 가능성이 현저히 낮아진다.  
    예를 들어, 사용자 로그인을 다루는 행복한 경로 시나리오는 잘못된 입력, 네트워크 연결 문제, 서버 오류가 발생할 가능성을 무시하기 때문에 시나리오나 케이스를 벗어난 오류에서 이슈를 찾을 수 없다.

[^2]:
    <img
    src="https://i2.ruliweb.com/img/20/08/21/174108cf3701255f9.jpeg"
    width="100%"
    height="300"
    />

[^3]: This takes advantage of Server Components by making it simple to specify the layout of choice in the markdown file and match against the `layouts` object which is then used to render the appropriate layout component.
