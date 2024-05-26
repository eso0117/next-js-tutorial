## 들어가기 전에

이 글은 [Next.js - Chapter 10 : Partial Prerendering (Optional)](https://nextjs.org/learn/dashboard-app/partial-prerendering) 강의를 번역한 글입니다.

# 부분 미리 렌더링

```
부분 미리 렌더링(Partial prerendering)은 Next.js 14에서 소개된 실험적인 기능입니다. 이 페이지의 컨텐츠는 기능이 안정화되면서 변경될 수 있습니다. 만약 실험적인 기능을 사용하고 싶지 않다면 이번 챕터는 건너 뛰어도 됩니다. 이번 챕터는 전체 강의를 완료하기 위한 필수는 아닙니다.
```

## 이번 챕터에서...

여기 우리가 다룰 주제들이 있습니다.
- 부분 미리 렌더링이 무엇인지
- 부분 미리 렌더링이 어떻게 동작하는지

## 정적인 컨텐츠와 동적인 컨텐츠의 결합

만약 라우트 내에서 [동적인 함수](https://nextjs.org/docs/app/building-your-application/routing/route-handlers#dynamic-functions)를 부르고 있다면(예를 들어, noStore(), cookies(), 기타 등등) 해당 라우트는 동적 라우트가 됩니다.

이것이 대부분의 웹 앱들이 구축되는 방식입니다. 우리는 **전체 앱** 혹은 **특정 라우트**에서 정적 혹은 동적 렌더링 방식 중에 선택해야합니다.

그러나 대부분의 라우트는 ***전체가 다 정적이지도 동적이지도 않습니다.***
아마 라우트는 정적인 컨텐츠와 동적인 컨텐츠 둘 다 가지고 있을겁니다. 예를 들어, 이 [이커머스](https://partialprerendering.com/) 사이트를 봅시다. 제품 페이지의 대부분은 미리 렌더링 할 수 있지만, 유저의 장바구니 정보나 추천 상품은 동적으로 가져오고 싶을겁니다.

대시보드 페이지로 돌아가서, 어떤 컴퍼넌트들이 동적 vs 정적이 되어야할까요?

준비가 되었다면 버튼을 눌러 대시보드 라우트를 어떻게 쪼갤수 있는지 확인해 봅시다.

<button class="show-and-hide-btn--hidden show-and-hide-btn btn" data-target="practice-01">
  <span data-btn-content>정답 보이기</span>
</button>

<div class="is-inactive" data-hide-target="practice-01">
<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fdashboard-static-dynamic-components.png&w=3840&q=75" alt="대시보드의 동적, 정적 컨텐츠 구분">

- `<SideNav>` 컴퍼넌트는 데이터나 유저 개인화에 따라 달라지지 않습니다. 따라서 정적이어야 합니다.
- `<Page>` 내에 컴퍼넌트는 데이터나 유저에 맞춰 정보가 바뀝니다. 따라서 동적이어야 합니다.

</div>

## 부분 미리 렌더링이란?

Next.js 14는 부분 미리 렌더링이 미리보기가 있습니다 -- 동적인 부분은 유지하면서 정적인 로딩 쉘로 렌더링 해주는 실험적인 기능입니다. 다시 말해서, 동적인 부분을 분리할 수 있습니다. 
예를 들어
<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fthinking-in-ppr.png&w=3840&q=75" alt="Nextjs 14 부분 미리 렌더링 예시">

유저가 해당 라우트를 방문할때

- 정적 라우트 쉘로 빠른 초기 로드를 유지합니다
- 쉘은 동적 컨텐츠를 비어놓고, 비동기적으로 컨텐츠를 불러옵니다
- 비동기 빈 컨텐츠는 전체적인 페이지 로드 타임을 줄이며 병렬로 스트림됩니다.

이것은 전체 라우트가 완전히 정적이거나 동적인 오늘날 어플리케이션이 동작하는 방식과 다릅니다.

부분 미리 렌더링은 굉장히 빠른 정적 컨텐츠의 표시와 동적 컨텐츠 제공 능력의 결합이고, 우리는 이것이 잠재적으로 [웹 어플리케이션에서 기본적인 렌더링 모델](https://vercel.com/blog/partial-prerendering-with-next-js-creating-a-new-default-rendering-model)이 될거라고 생각합니다.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">부분 미리 렌더링 내에 구멍은 무엇인가요?</p>
    <div class="option-list">
      <div class="option" data-question-number="01">
        <span class="option__number">A</span>
        <span class="option__desc">자바스크립트가 불가능한 부분</span>
      </div>
      <div class="option" data-question-number="01" data-answer="true">
        <span class="option__number">B</span>
        <span class="option__desc">동적 컨텐츠가 비동기적으로 불러와질 부분</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">C</span>
        <span class="option__desc">서드 파티 스크립트가 불러와질 부분.</span>
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

## 부분 미리 렌더링은 어떻게 동적하나요?

부분 미리 렌더링은 리액트의 [Concurrent APIs](https://react.dev/blog/2021/12/17/react-conf-2021-recap#react-18-and-concurrent-features)와 [Suspense](https://react.dev/reference/react/Suspense)를 사용해서 특정 조건이 충족 될때까지(예를 들어 데이터 가져오기) 우리 어플리케이션의 일부 렌더링을 미룹니다.

초기에 정적 컨텐츠로 이루어진 정적 파일이 대체화면으로 내제됩니다. 빌드 시간(혹은 revalidation) 동안, 라우트의 정적 부분들은 *미리 렌더링 됩니다*. 그리고 나머지 부분은 라우트에 유저가 요청할때까지 *미뤄집니다*.

컴퍼넌트를 Suspense 안으로 래핑하는것은 그 자체로는 컴퍼넌트를 동적으로 만드는게 아닙니다.(우리가 동적으로 만들기 위해 `unstable_noStore`를 썼던것을 기억하세요) 그보다는 Suspense는 라우트의 정적과 동적 부분 사이의 경계로서 쓰여졌습니다.

부분 미리 렌더링에서 가장 훌륭한 점은 이것을 쓰기 위해 코드를 바꿀 필요가 없다는 겁니다. 우리 라우트의 동적인 부분을 Suspense를 사용해 감싸는걸로, Next.js는 어떤 부분이 동적이고 어떤 부분이 정적인지 알게됩니다.

<div class="hint">

**노트**: 부분 미리 렌더링이 어떻게 설정되는지 알고 싶다면, [부분 미리 렌더링(실험적)](https://nextjs.org/docs/app/api-reference/next-config-js/partial-prerendering) 또는 [부분 미리 렌더링 템플릿과 데모](https://vercel.com/templates/next.js/partial-prerendering-nextjs)를 시작해보세요. 이것은 아직 **실험적**이고 **아직 실제 프로덕션에 배포하기엔 준비가 안 되었다**걸 명심하세요.
</div>

### 요약

오약하면, 우리는 어플리케이션에서 데이터 가져오는데 최적화를 하기 위해 몇가지 것들을 해왔습니다.

1. 서버와 데이터베이스간 지연을 줄이기 위해 데이터베이스를 어플리케이션 코드와 같은 지역에 생성했습니다.
2. 리액트 서버 컴퍼넌트로 서버에서 데이터를 가져왔습니다. 이것은 비용이 드는 데이터 연산이나 가져오는 부분을 서버에서 처리하면서, 클라이언트 사이드의 자바스크립트의 일을 줄이고 클라이언트에 데이터베이스 비밀값들이 노출되는걸 막습니다.
3. 오직 필요한 데이터를 가져오기 위해 SQL을 사용했습니다. 이것은 각각의 요청에 전달되는 데이터의 양과 메모리 영역에 전환이 필요한 데이터의 양을 줄입니다.
4. 필요하다고 판단되는 부분에 자바스크립트로 데이터를 병렬로 가져왔습니다.
5. 느린 요청이 페이지 전체를 블럭하는것을 막기 위해 스트리밍을 사용해서 유저가 모든것이 불러와지기 전에 UI와 상호작용 할 수 있도록 했습니다.
6. 데이터를 가져오는 부분을 그것이 필요한 컴퍼넌트로 옮겨서 부분 미리 렌더링을 준비할때, 동적으로 되어야할 부분들이 분리되었습니다.

다음 챕터에서, 데이터를 가져올때 쓰는 검색과 페이지네이션, 두가지 흔한 패턴을 볼겁니다.


<div class="finish">
  <p class="finish__title">챕터 10를 완료했습니다.</p>
  <p>Next.js 14에 소개되어진, 빠른 렌더링 모델인 부분 미리 렌더링에 대해 배웠습니다.</p>
  <div class="next-box">
    <p class="next">다음</p>    
    <p class="next__title">11: 검색과 페이지네이션 추가하기</p>
    <p>Next.js API로 검색과 페이지네이션을 어떻게 하는지 배웁니다.</p>
    <a id="next__btn" href="https://nextjs.org/learn/dashboard-app/adding-search-and-pagination">챕터 11 시작하기
    <svg data-testid="geist-icon" height="16" stroke-linejoin="round" viewBox="0 0 16 16" width="16" style="color: currentcolor;"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.53033 2.21968L9 1.68935L7.93934 2.75001L8.46967 3.28034L12.4393 7.25001H1.75H1V8.75001H1.75H12.4393L8.46967 12.7197L7.93934 13.25L9 14.3107L9.53033 13.7803L14.6036 8.70711C14.9941 8.31659 14.9941 7.68342 14.6036 7.2929L9.53033 2.21968Z" fill="currentColor"></path></svg>
    </a>
  </div>
</div>

## Ref
- [https://nextjs.org/learn/dashboard-app/partial-prerendering](https://nextjs.org/learn/dashboard-app/partial-prerendering)


<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/next-js-tutorial/css.css">
<script type="text/javascript" src="https://eso0117.github.io/web-practice/public/next-js-tutorial/js.js"></script>
