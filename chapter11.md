## 들어가기 전에

이 글은 [Next.js - Chapter 11 : Adding Search and Pagination
](https://nextjs.org/learn/dashboard-app/adding-search-and-pagination) 강의를 번역한 글입니다.

# 검색과 페이지네이션 추가하기

이전 챕터에서, 스트리밍으로 대시보드의 초기 로딩 성능을 향상시켰습니다. 이제 `/invoices` 페이지로 넘어가서 검색기능과 페이지네이션 기능을 추가하는 방법을 배워봅시다.

## 이번 챕터에서...

여기 우리가 다룰 주제들이 있습니다.
- 다음과 같은 Next.js API 다루는 법을 배웁니다: `searchParams`, `usePathname`, `useRouter`
- URL 파라미터로 검색과 페이지네이션을 구현하는 법을 배웁니다. 

## 시작하는 코드

`/dashboard/invoices/page.tsx` 내에 다음의 코드를 붙여넣으세요.

<div class="code-with-file">
/app/dashboard/invoices/page.tsx

```
import Pagination from '@/app/ui/invoices/pagination';
import Search from '@/app/ui/search';
import Table from '@/app/ui/invoices/table';
import { CreateInvoice } from '@/app/ui/invoices/buttons';
import { lusitana } from '@/app/ui/fonts';
import { InvoicesTableSkeleton } from '@/app/ui/skeletons';
import { Suspense } from 'react';
 
export default async function Page() {
  return (
    <div className="w-full">
      <div className="flex w-full items-center justify-between">
        <h1 className={`${lusitana.className} text-2xl`}>Invoices</h1>
      </div>
      <div className="mt-4 flex items-center justify-between gap-2 md:mt-8">
        <Search placeholder="Search invoices..." />
        <CreateInvoice />
      </div>
      {/*  <Suspense key={query + currentPage} fallback={<InvoicesTableSkeleton />}>
        <Table query={query} currentPage={currentPage} />
      </Suspense> */}
      <div className="mt-5 flex w-full justify-center">
        {/* <Pagination totalPages={totalPages} /> */}
      </div>
    </div>
  );
}
```
</div>

잠깐 우리가 작업할 페이지와 컴퍼넌트를 알아보는 시간을 갖읍시다.

1. `<Search/>` 유저가 특정 송장을 검색하게 해줍니다.
2. `<Pagination/>` 유저가 송장 페이지간의 이동하게 해줍니다.
3. `<Table/>` 송장을 보여줍니다.

검색기능은 클라이언트와 서버 모두 걸쳐있습니다. 유저가 클라이언트에서 송장을 검색할때, URL 파라미터는 업데이트되고, 서버에서 데이터를 가져옵니다. 그리고 테이블은 새로운 데이터로 다시 렌더링 합니다.

## 왜 URL 검색 파라미터를 쓰나요?

위에 말했듯, 우리는 검색 상태를 관리하기 위해 URL 검색 파라미터를 사용하고 있습니다. 이 패턴은 만약 당신이 클라이언트 스테이트를 사용하는데 익숙하다면 낯선 방법일 겁니다.

- **공유가능하고 저장 가능한 URL**: 검색 파라미터는 URL안에 있기 때문에, 유저는 검색 쿼리와 필터까지 포함한 어플리케이션의 현 상태를 저장 가능하고 후에 공유 및 재참조도 가능합니다.
- **서버 사이드 렌더링과 초기 불러오기**: URL 파라미터는 초기 상태를 렌더링 하기 위해 서버에서 직접 사용되고 이것은 서버 사이드 렌더링을 쉽게 합니다.
**분석과 추적**: URL에 쿼리와 필터를 갖고 있는 것은 추가적인 클라이언트 사이드의 로직 없이 유저의 행동을 추적하기 용이하게 만듭니다.

## 검색 기능 추가하기

아래 검색 기능을 구현할 다양한 Next.js의 클라이언트 훅입니다.

- **`useSearchParams`**: 우리가 현재 URL 파라미터의 접근할 수 있게 해줍니다. 예를 들어 `/dashboard/invoices?page=1&query=pending`의 파라미터가 `{page: '1', query: 'pending'}` 처럼 나오게 될겁니다.
- **`usePathname`**: 현재 URL의 경로를 가져옵니다. 예를 들어 URL `/dashboard/invoices` 경로의 `usePathname`도 `'/dashboard/invoices'`를 리턴할겁니다.
- **`useRouter`**: 클라이언트 컴퍼넌트간의 경로를 이동하는걸 가능하게 해줍니다. 사용할 수 있는 [다양한 메소드](https://nextjs.org/docs/app/api-reference/functions/use-router#userouter)들이 있습니다.

진행 순서를 빠르게 훑어보면,

1. 유저의 입력 받기
2. 검색 파라미터로 URL 업데이트
3. URL을 유저입력필드와 동기화 유지
4. 검색 쿼리 결과를 반영해 테이블을 업데이트

### 1. 유저 입력 받기
`/app/ui/search.tsx`경로에 `<Search>` 컴퍼넌트로 가면 다음을 볼 수 있습니다.

- `"use client"` - 이것은 이벤트리스너와 훅을 사용하는 클라이언트 컴퍼넌트라는 선언입니다.
- `<input>` - 이것은 검색 입력창입니다.

새로운 `handleSearch` 함수를 만듭시다. 그리고 `onChange` 리스터를 `<input>` 에 추가하세요. `onChange`는 입력된 값이 바뀔때마다 `handleSearch`를 실행할겁니다.

<div class="code-with-file">
/app/ui/search.tsx

```
'use client';
 
import { MagnifyingGlassIcon } from '@heroicons/react/24/outline';
 
export default function Search({ placeholder }: { placeholder: string }) {
  function handleSearch(term: string) {
    console.log(term);
  }
 
  return (
    <div className="relative flex flex-1 flex-shrink-0">
      <label htmlFor="search" className="sr-only">
        Search
      </label>
      <input
        className="peer block w-full rounded-md border border-gray-200 py-[9px] pl-10 text-sm outline-2 placeholder:text-gray-500"
        placeholder={placeholder}
        onChange={(e) => {
          handleSearch(e.target.value);
        }}
      />
      <MagnifyingGlassIcon className="absolute left-3 top-1/2 h-[18px] w-[18px] -translate-y-1/2 text-gray-500 peer-focus:text-gray-900" />
    </div>
  );
}
```
</div>

개발툴(Developer Tools) 콘솔에서 `input`에 아무 검색어를 넣어서 잘 동작하는지 확인해봅시다. `input`에 입력한 검색어가 기록될 겁니다.

훌륭합니다! 유저의 입력을 받았어요. 이제 검색어로 URL을 업데이트 합시다.

## 검색 파라미터로 URL 업데이트 하기

`useSearchParams`를 `'next/navigation'`에서 불러(import)오세요. 그리고 변수에 할당합니다.

<div class="code-with-file">
/app/ui/search.tsx

```
'use client';
 
import { MagnifyingGlassIcon } from '@heroicons/react/24/outline';
import { useSearchParams } from 'next/navigation';
 
export default function Search() {
  const searchParams = useSearchParams();
 
  function handleSearch(term: string) {
    console.log(term);
  }
  // ...
}
```
</div>

`handleSearch`내에, `searchParams` 변수를 인자로 받는 [`URLSearchParams`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)을 생성하세요.

<div class="code-with-file">
/app/ui/search.tsx

```
'use client';
 
import { MagnifyingGlassIcon } from '@heroicons/react/24/outline';
import { useSearchParams } from 'next/navigation';
 
export default function Search() {
  const searchParams = useSearchParams();
 
  function handleSearch(term: string) {
    const params = new URLSearchParams(searchParams);
  }
  // ...
}
```
</div>











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
