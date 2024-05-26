## 들어가기 전에

이 글은 [Next.js - Chapter 8 : Static and Dynamic Rendering](https://nextjs.org/learn/dashboard-app/static-and-dynamic-rendering) 강의를 번역한 글입니다.

# 정적 그리고 동적 렌더링

이전 챕터에서, 대시보드 Overview 페이지를 위한 데이터를 가져왔습니다. 하지만 우리는 현재의 상태에 대해 두가지 한계점을 이야기 했습니다.

1. 데이터 요청은 의도치 않은 워터폴을 생기게 한다.
2. 대시보드는 정적이다, 그래서 어떠한 데이터 업데이트가 있더라도 어플리케이션에 반영되지 않는다.

## 이번 챕터에서...

여기 우리가 다룰 주제들이 있습니다.
- 정적 렌더링이 무엇인지 그리고 이것이 어떻게 어플리케이션 성능을 향상시킬 수 있는지
- 동적 렌더링이 무엇인지 그리고 언제 쓰는지
- 대시보드를 동적으로 만들 다른 방법들
- 데이터를 느리게 가져오도록 시뮬레이션해서 무슨일 일어나는지 보기

## 정적 렌더링이란 무엇인가요?

정적 렌더링에서는, 빌드타임(배포할때) 또는 [revalidation](https://nextjs.org/docs/app/building-your-application/data-fetching/fetching-caching-and-revalidating#revalidating-data) 동안 서버에서 데이터를 가져오고 렌더링하는것이 이뤄집니다. 그 결과는 [Content Delivery Network (CDN)](https://nextjs.org/docs/app/building-your-application/rendering/server-components#static-rendering-default)에 캐시되거나 배포될 수 있습니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fstatic-site-generation.png&w=3840&q=75" alt="정적 렌더링 라우트 안내">

유저가 당신의 어플리케이션을 방문할때마다 저장된 결과가 제공됩니다. 정적 렌더링의 장점으로는

- **더 빠른 웹사이트** - 미리 렌더링된 컨텐츠는 전세계에 캐시되고 배포될 수 있습니다. 이것은 세계에 있는 유저들이 더 빠르고 신뢰성있게 웹사이트 컨텐츠에 접근할 수 있다는 겁니다.
- **서버 부담 감소** - 컨텐츠는 캐시되어있기 때문에, 각 유저의 요청마다 동적으로 컨텐츠를 만들 필요가 없습니다.
- **SEO** - 페이지를 불러올때 컨텐츠가 이미 접근 가능하므로 미리 렌더링된 컨텐츠는 검색엔진 크롤러가 인덱싱 하기에 더 용이합니다. 이것은 검색 엔진의 상위에 노출될 수 있습니다.

정적 렌더링은 블로그의 글이나 상품 페이지 같은 **데이터가 없는** UI 또는 **모든 유저에 대해 공통으로 공유되는 데이터**를 사용하는 UI에 대해 유용합니다. 정기적으로 업데이트되면서, 개인화된 데이터를 제공해야하는 대시보드에는 맞지 않을겁니다.

정적 렌더링의 반대는 바로 동적 렌더링입니다.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">대시보드앱에는 왜 정적 렌더링이 어울리지 않을까요?</p>
    <div class="option-list">
      <div class="option" data-question-number="01">
        <span class="option__number">A</span>
        <span class="option__desc">웹사이트를 느리게 만들기 때문에</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">B</span>
        <span class="option__desc">서버 부담이 증가하므로</span>
      </div>
      <div class="option" data-question-number="01" data-answer="true">
        <span class="option__number">C</span>
        <span class="option__desc">어플리케이션이 최신화된 데이터를 제공하지 않으므로</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">D</span>
        <span class="option__desc">CDN이 필요하므로</span>
      </div>
    </div>
    <div class="quiz__btn-container">
      <button class="quiz__btn"
        data-js-check-answer data-question="01">
        정답 확인
      </button>
    </div>
  </div>  
</div>


## 동적 렌더링이란 무엇인가요?

동적 렌더링에서는, 서버에서 각 유저의 **요청 시간**(유저가 페이지를 방문할때)에 컨텐츠가 렌더링됩니다. 동적 렌더링에도 몇가지 장점이 있습니다.

- **실시간 데이터** - 동적 렌더링은 우리 어플리케이션이 실시간 혹은  업데이트된 데이터로 렌더링되도록 해줍니다. 이것은 데이터가 자주 바뀌는 곳에서 이상적인 렌더링입니다.
- **유저에 맞춰진 컨텐츠** - 대시보드나 유저 프로필 등 개인화된 컨텐츠를 제공하기에 용이합니다. 그리고 유저 상호작용에 의한 데이터를 업데이트 하기에 쉽습니다.
- **요청할 때 정보** - 동적 렌더링은 쿠키나 URL 검색 파라미터 같은 요청시에 알 수 있는 정보에 접근할 수 있게 해줍니다.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">요청시 알 수 있는 정보는 어떤건가요?</p>
    <div class="option-list">
      <div class="option" data-question-number="02">
        <span class="option__number">A</span>
        <span class="option__desc">데이터베이스 스키마</span>
      </div>
      <div class="option" data-question-number="02">
        <span class="option__number">B</span>
        <span class="option__desc">URL 경로</span>
      </div>
      <div class="option" data-question-number="02"  data-answer="true">
        <span class="option__number">C</span>
        <span class="option__desc">쿠키나 URL 검색 파라미터</span>
      </div>
    </div>
    <div class="quiz__btn-container">
      <button class="quiz__btn"
        data-js-check-answer data-question="02">
        정답 확인
      </button>
    </div>
  </div>  
</div>


## 대시보드 동적으로 만들기

기본적으로, `@vercel/postgres`는 캐싱 시맨틱을 설정하지 않습니다. 이것은 프레임워크의 정적 그리고 동적 행동을 세팅할 수 있게 해줍니다.

우리의 서버 컴퍼넌트 안에 `unstable_noStore`라 불리는 Next.js API를 사용하거나 정적 렌더링을 안하도록 데이터 호출 함수를 사용할 수 있습니다. 

`data.ts`에서 `next/cache`로부터 `unstable_noStore`를 불러(import)오세요. 그리고 데이터를 불러오는 함수 맨 위에 호출하세요.

<div class="code-with-file">
/app/lib/data.ts

```
// ...
import { unstable_noStore as noStore } from 'next/cache';
 
export async function fetchRevenue() {
  // Add noStore() here to prevent the response from being cached.
  // This is equivalent to in fetch(..., {cache: 'no-store'}).
  noStore();
 
  // ...
}
 
export async function fetchLatestInvoices() {
  noStore();
  // ...
}
 
export async function fetchCardData() {
  noStore();
  // ...
}
 
export async function fetchFilteredInvoices(
  query: string,
  currentPage: number,
) {
  noStore();
  // ...
}
 
export async function fetchInvoicesPages(query: string) {
  noStore();
  // ...
}
 
export async function fetchFilteredCustomers(query: string) {
  noStore();
  // ...
}
 
export async function fetchInvoiceById(query: string) {
  noStore();
  // ...
}
```
</div>
<br>
<div class="hint">

**주의:**
- `unstable_noStore`는 실험적 API고 후에 변경될 수 있습니다. 자신의 프로젝트에는 좀 더 안정적인 것을 사용하고 싶다면, [라우트 경로 설정 옵션](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config) `export const dynamic = "force-dynamic"`을 사용할 수 있습니다.

</div>

## 데이터를 느리게 가져오도록 시뮬레이션 해보기

대시보드를 동적으로 만드는 것은 좋은 첫걸음입니다. 그러나 이전 챕터에서 말한 문제가 여전히 있습니다. 만약 하나의 데이터 요청이 다른것들보다 느리면 어떻게 될까요?

데이터를 느리게 가져오도록 해봅시다. `data.ts`파일에서, `fetchRevenue()`안에  `console.log`와 `setTimeout`의 주석을 제거합시다

<div class="code-with-file">
/app/lib/data.ts

```
export async function fetchRevenue() {
  try {
    // We artificially delay a response for demo purposes.
    // Don't do this in production :)
    console.log('Fetching revenue data...');
    await new Promise((resolve) => setTimeout(resolve, 3000));
 
    const data = await sql<Revenue>`SELECT * FROM revenue`;
 
    console.log('Data fetch completed after 3 seconds.');
 
    return data.rows;
  } catch (error) {
    console.error('Database Error:', error);
    throw new Error('Failed to fetch revenue data.');
  }
}
```
</div>

이제 [http://localhost:3000/dashboard/](http://localhost:3000/dashboard/)를 열어 페이지를 불러오는데 얼마나 더 걸리는지 보세요, 아래와 같은 메시지를 볼 수 있을겁니다

```
Fetching revenue data...
Data fetch completed after 3 seconds.
```

우리는 인위적으로 데이터를 늦게 가져오기 위해 3초 딜레이를 넣었습니다. 그 결과로 데이터가 가져와지는 동안 전체 페이지가 블럭이됩니다.

그리고 이것은 개발자들에게 해결해야할 공통의 숙제이죠.

동적 렌더링을 적용하면, **우리 어플리케이션은 가장 느린 데이터를 가져와야 하는 만큼 느려집니다.**


<div class="finish">
  <p class="finish__title">챕터 8을 완료했습니다.</p>
  <p>훌륭합니다! Next.js의 정적 렌더링과 동적 렌더링을 배웠습니다.</p>
  <div class="next-box">
    <p class="next">다음</p>    
    <p class="next__title">9: 스트리밍</p>
    <p>스트리밍을 추가함으로서 유저 경험을 향상시키는 방법을 배웁니다.</p>
    <a id="next__btn" href="https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-9-스트리밍">챕터 9 시작하기
    <svg data-testid="geist-icon" height="16" stroke-linejoin="round" viewBox="0 0 16 16" width="16" style="color: currentcolor;"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.53033 2.21968L9 1.68935L7.93934 2.75001L8.46967 3.28034L12.4393 7.25001H1.75H1V8.75001H1.75H12.4393L8.46967 12.7197L7.93934 13.25L9 14.3107L9.53033 13.7803L14.6036 8.70711C14.9941 8.31659 14.9941 7.68342 14.6036 7.2929L9.53033 2.21968Z" fill="currentColor"></path></svg>
    </a>
  </div>
</div>

## Ref
- [https://nextjs.org/learn/dashboard-app/static-and-dynamic-rendering](https://nextjs.org/learn/dashboard-app/static-and-dynamic-rendering)


<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/next-js-tutorial/css.css">
<script type="text/javascript" src="https://eso0117.github.io/web-practice/public/next-js-tutorial/js.js"></script>
