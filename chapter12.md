## 들어가기 전에

이 글은 [Next.js - Chapter 12 : Mutating Data
](https://nextjs.org/learn/dashboard-app/mutating-data) 강의를 번역한 글입니다.

# 데이터 변형(Mutating)

이전 챕터에서 우리는 URL 검색 파라미터와 Next.js API를 이용해 검색과 페이지네이션(Pagination) 기능을 구현했습니다. 이제 invoices 페이지에 송장을 추가,업데이트, 삭제하는 기능을 추가해봅시다.

## 이번 챕터에서...

여기 우리가 다룰 주제들이 있습니다.
- React Server Action이란 무엇이고 데이터를 변형(mutate)하는데 어떻게 사용하는지
- 폼(form)과 Server Components로 작업하는 방법
- 타입 유효성 검사와 네이티브 `formData` 객체를 사용하는 좋은 방법
- `revalidatePath` API를 사용한 클라이언트 캐시 리밸리데이션(revalidate) 방법
- 특정 ID값들을 사용한 동적 라우트 경로 생성 방법

## Server Actions이란 무엇인가요?

React Server Actions은 비동기 코드를 직접 서버에서 실행할 수 있습니다. 이것들을 사용하면 데이터를 변형(mutate)하기위해 API endpoints를 생성할 필요가 없습니다. 대신 서버에서 실행하고 클라이언트 컴퍼넌트나 서버 컴퍼넌트로에서 호출될 비동기 함수를 작성합니다.

웹 어플리케이션은 다양한 위협에 취약할 수 있으므로 보안이 가장 중요한 부분입니다.
여기가 Server Actions이 필요한 부분입니다.

이것은 데이터를 지키고 승인된 접근을 확인하며 다양한 타입의 공격으로부터 보호하면서 효과적인 보안 솔루션을 제공합니다. Server Actions은 POST 요청과, 암호화된 클로져, 엄격한 입력 확인, 에러메시지 해싱, 호스트 제한 등 여러 기술을 사용하였고 이것이 한 대 어우러져 어플리케이션의 안정성을 향상시켰습니다.


## Server Actions을 적용한 폼(form) 사용하기

React에서 `<form>`의 액션을 지정하기 위해 `action` 속성을 쓸 수 있었습니다. 액션은 자동적으로 입력된 데이터를 담고 있는 네이티브 [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData) 객체를 받게 됩니다.

<div class="code-with-file">

**예를 들면**
```
// Server Component
export default function Page() {
  // Action
  async function create(formData: FormData) {
    'use server';
 
    // Logic to mutate data...
  }
 
  // Invoke the action using the "action" attribute
  return <form action={create}>...</form>;
}
```
</div>

서버 컴퍼넌트 내에 Server Action을 호출하는 장점은 [점진적 향상](https://developer.mozilla.org/ko/docs/Glossary/Progressive_Enhancement)입니다. - 폼(form)은 클라이언트에서 자바스크립트를 사용 안함으로 하여도 동작합니다.

## Server Action Next.js

Server Action은 Next.js의 [캐싱](https://nextjs.org/docs/app/building-your-application/caching)과 깊게 통합되어 있습니다. 폼(form)이 Server Action으로 전송될때, 데이터를 변경(mutate)하기위해 action을 쓸 수 있는것 뿐 아니라, `revalidatePath`와 `revalidateTag` 같은 API들을 통해 관련된 캐시를 revalidate 할 수 있습니다.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">Server Action을 사용하는 하나의 장점은 무엇인가요?</p>
    <div class="option-list">
      <div class="option" data-question-number="01">
        <span class="option__number">A</span>
        <span class="option__desc">SEO 향상</span>
      </div>
      <div class="option" data-question-number="01" data-answer="true">
        <span class="option__number">B</span>
        <span class="option__desc">점진적 향상</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">C</span>
        <span class="option__desc">웹 사이트를 빠르게 해줌</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">D</span>
        <span class="option__desc">데이터 암호화</span>
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

<br>
이제 이것이 어떻게 동작하는지 봅시다.










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

### 2. 검색 파라미터로 URL 업데이트 하기

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

`URLSearchParam`은 URL 쿼리 파라미터 관련해서 여러 유틸리티 메소드들을 제공하는 웹 API입니다. 복잡한 문자열 리터럴을 사용하는 것 대신, 이것을 사용해서 `?page=1&query=a`같은 문자열 파라미터를 얻을 수 있습니다.

다음으로, 유저 입력에 따라 파라미터 값을 `set` 하세요. 만약 유저 입력이 없다면, 파라미터를 지웁니다.

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
    if (term) {
      params.set('query', term);
    } else {
      params.delete('query');
    }
  }
  // ...
}
```
</div>

이제 우리는 쿼리 스트링을 갖게 되었으니, Next.js의 `useRouter`과 `usePathname` 훅을 써서 URL을 업데이트 할 수 있습니다.

`next/navigation`에서 `useRouter`과 `usePathname`을 불러(import)오세요. 그리고 `handleSearch`내에 `useRouter()`에서 `replace` 메소드를 사용합니다.

<div class="/app/ui/search.tsx">
/app/ui/search.tsx

```
'use client';
 
import { MagnifyingGlassIcon } from '@heroicons/react/24/outline';
import { useSearchParams, usePathname, useRouter } from 'next/navigation';
 
export default function Search() {
  const searchParams = useSearchParams();
  const pathname = usePathname();
  const { replace } = useRouter();
 
  function handleSearch(term: string) {
    const params = new URLSearchParams(searchParams);
    if (term) {
      params.set('query', term);
    } else {
      params.delete('query');
    }
    replace(`${pathname}?${params.toString()}`);
  }
}
```
</div>

무엇이 일어나고 있는지 설명을 하자면,
-  `${pathname}` 은 현재 경로입니다, 지금의 경우에는 `"/dashboard/invoices"` 입니다.
-  유저가 검색창에 입력을 할때, `params.toString()`은 유저의 입력을 URL에 적합한 형식으로 바꿔줍니다.
-  `replace(${pathname}?${params.toString()})`은 유저의 입력 데이터로 URL을 업데이트 해줍니다. 예를들어, 만약 유저가 `"Lee"`를 검색 했다면 `/dashboard/invoices?query=lee`가 됩니다.
- Next.js의 클라이언트 사이드 페이지 이동(우리가 챕터 5 - [페이지간 이동](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-5-페이지간-이동)에서 배운겁니다.) 덕분에 URL은 페이지 리로딩되는 것 없이 업데이트 됩니다.

### 3. URL과 입력 동기화하기

입력필드가 URL과 동기화되고 공유할때 채워지도록 하기 위해 `searchParams`에서 `defaultValue`를 가져와 input에 전달할 수 있습니다.

<div class="code-with-file">
/app/ui/search.tsx


```
<input
  className="peer block w-full rounded-md border border-gray-200 py-[9px] pl-10 text-sm outline-2 placeholder:text-gray-500"
  placeholder={placeholder}
  onChange={(e) => {
    handleSearch(e.target.value);
  }}
  defaultValue={searchParams.get('query')?.toString()}
/>
```
</div>

<div class="hint">

**`defaultValue` vs `value` / Controlled vs Uncontrolled**

state를 사용하여 입력값을 관리하면, `value` 속성을 사용해 controlled compononent를 만들 것이고, 이는 리액트가 입력 상태를 관리한다는 의미입니다.

그러나 state를 사용하고 있지 않기 때문에, `defaultValue`를 사용할 수 있습니다. 이것은 input이 그 자신의 state를 관리할거란 의미이고, 우리가 state대신 URL에 검색 쿼리를 저장하고 있기 때문에 괜찮습니다.

</div>

### 4. 테이블 업데이트 하기

이제 검색 쿼리를 반영해서 테이블 컴퍼넌트를 업데이트 해야합니다.

invoices 페이지로 돌아갑시다.

Page 컴퍼넌트는  [`searchParams`이라는 prop을 받습니다.](https://nextjs.org/docs/app/api-reference/file-conventions/page), 그래서 현재 URL 파라미터를 `<Table>` 컴퍼넌트로 보낼 수 있습니다.

<div class="code-with-file">
/app/dashboard/invoices/page.tsx

```
import Pagination from '@/app/ui/invoices/pagination';
import Search from '@/app/ui/search';
import Table from '@/app/ui/invoices/table';
import { CreateInvoice } from '@/app/ui/invoices/buttons';
import { lusitana } from '@/app/ui/fonts';
import { Suspense } from 'react';
import { InvoicesTableSkeleton } from '@/app/ui/skeletons';
 
export default async function Page({
  searchParams,
}: {
  searchParams?: {
    query?: string;
    page?: string;
  };
}) {
  const query = searchParams?.query || '';
  const currentPage = Number(searchParams?.page) || 1;
 
  return (
    <div className="w-full">
      <div className="flex w-full items-center justify-between">
        <h1 className={`${lusitana.className} text-2xl`}>Invoices</h1>
      </div>
      <div className="mt-4 flex items-center justify-between gap-2 md:mt-8">
        <Search placeholder="Search invoices..." />
        <CreateInvoice />
      </div>
      <Suspense key={query + currentPage} fallback={<InvoicesTableSkeleton />}>
        <Table query={query} currentPage={currentPage} />
      </Suspense>
      <div className="mt-5 flex w-full justify-center">
        {/* <Pagination totalPages={totalPages} /> */}
      </div>
    </div>
  );
}
```
</div>

`<Table>` 컴퍼넌트를 보면, `query`, `currentPage` 2개의 prop이 해당 쿼리랑 일치하는 송장을 리턴하는 `fetchFilteredInvoices()`의 함수 인자로 들어가는걸 볼겁니다.

<div class="code-with-file">
/app/ui/invoices/table.tsx

```
// ...
export default async function InvoicesTable({
  query,
  currentPage,
}: {
  query: string;
  currentPage: number;
}) {
  const invoices = await fetchFilteredInvoices(query, currentPage);
  // ...
}
```
</div>

이 변경사항이 적용되면 테스트를 시작해봅시다. 검색어를 입력하면, URL이 업데이트 될 것이고, 서버로부터 데이터를 가져오라는 명령을 보냅니다. 그리고 쿼리랑 매치되는 송장들만 리턴될 겁니다.

<div class="hint">

**언제 `useSearchParams()` 훅을쓰고, 언제 `searchParams` prop을 쓰나요?**

이 강의를 진행하면서, 검색 파라미터를 가져오는 두가지 방법이 있는걸 알았을 겁니다. 각각을 쓰는 것은 현재 client에서 작업하는지 server에서 작업하는지에 달려있습니다.

- `<Search>`는 클라이언트 컴퍼넌트입니다. 클라이언트에서 파라미터에 접근하려면 `useSearchParams()` 훅을 써야합니다.
- `<Table>`은 서버 컴퍼넌트입니다. 페이지로부터 `searchParams` prop을 사용해 해당 컴퍼넌트에 전달할 수 있습니다.

</div>

### 좋은 방법: 디바운싱(Debouncing)
축하합니다! Next.js로 검색을 구현했습니다! 그런데 이것을 최적화 할 방법이 더 있습니다.

`handleSearch` 함수 내에, `console.log`를 추가해 봅시다.

<div class="code-with-file">
/app/ui/search.tsx

```
function handleSearch(term: string) {
  console.log(`Searching... ${term}`);
 
  const params = new URLSearchParams(searchParams);
  if (term) {
    params.set('query', term);
  } else {
    params.delete('query');
  }
  replace(`${pathname}?${params.toString()}`);
}
```
</div>

그리고 검색바에 "Emil" 을 입력하고, 개발툴 콘솔을 확인해봅시다. 어떤가요?

<div class=code-with-file>
Dev Tools Console

```
Searching... E
Searching... Em
Searching... Emi
Searching... Emil
```
</div>

모든 키 입력에 따라 URL을 업데이트 하고 있습니다. 즉 매번 키 입력마다 데이터베이스에 질의가 되고 있는거죠! 이것은 우리의 어플리케이션이 작을때는 문제가 아니겠지만, 어플리케이션이 수천명의 유저를 가지고 있고, 각각의 유저가 모든 키 입력마다 데이터베이스에 질의를 요청한다 생각해보세요.

**디바운싱(Debouncing)** 은 특정 함수가 실행되는 것에 제한을 두는 프로그래밍 방법입니다. 우리의 경우에는 유저가 입력을 멈췄을 때에만 질의를 해야 합니다.

<div class="hint">

**디바운스가 동작하는 방법**

1. **이벤트 발생**: 디바운스가 되어야할 이벤트(검색박스에 입력 같은)가 발생될때, 타이머가 시작됩니다.
2. **Wait**: 새로운 이벤트가 발생되면, 타이머는 리셋됩니다.
3. **실행**: 타이머가 시간이 되면, 디바운스된 함수가 실행됩니다. 

</div>

디바운싱(Debouncing)을 구현 하는데는, 스스로 그 함수를 구현하는걸 포함해서 몇가지 방법이 있습니다. 우리는 간단히 하기 위해서, [`use-debounce`](https://www.npmjs.com/package/use-debounce)라는 라이브러리를 사용하겠습니다.

<div class="code-with-file">
Terminal

```
npm i use-debounce
```
</div>

이제 `<Search>` 컴퍼넌트에서, `useDebouncedCallback` 함수를 불러(import)오세요.

<div class="code-with-file">
/app/ui/search.tsx

```
// ...
import { useDebouncedCallback } from 'use-debounce';
 
// Inside the Search Component...
const handleSearch = useDebouncedCallback((term) => {
  console.log(`Searching... ${term}`);
 
  const params = new URLSearchParams(searchParams);
  if (term) {
    params.set('query', term);
  } else {
    params.delete('query');
  }
  replace(`${pathname}?${params.toString()}`);
}, 300);
```
</div>

이 함수는 handleSearch의 컨텐츠를 감쌉니다. 그리고 유저가 입력을 끝내면(300ms) 해당 코드를 동작시킵니다.

이제 검색바에 입력을 해보세요, 그리고 개발툴의 콘솔을 열어보세요. 다음과 같이 잘 작동하는걸 확인할 수 있습니다.

<div class="code-with-file">
Dev Tools Console

```
Searching... Emil
```
</div>

디바운싱(Debouncing)으로, 데이터베이스에 보내는 요청을 줄일 수 있고, 결국 리소스를 아낄 수 있습니다.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">디바운싱이 검색기능에 있어서 해결해준건 무엇인가요?</p>
    <div class="option-list">
      <div class="option" data-question-number="01">
        <span class="option__number">A</span>
        <span class="option__desc">데이터베이스 질의 속도 향상</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">B</span>
        <span class="option__desc">URL을 북마크 할 수 있도록 해줌</span>
      </div>
      <div class="option" data-question-number="01"
      data-answer="true">
        <span class="option__number">C</span>
        <span class="option__desc">모든 키 입력마다 데이터베이스 요청을 막아줌</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">D</span>
        <span class="option__desc">SEO최적화를 도와줌</span>
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

## 페이지네이션(Pagination) 추가

검색기능을 추가한 후, 테이블에 한번에 6개까지만 보이는걸 알았나요? `data.ts`에 있는 `fetchFilteredInvoices()` 함수가 페이지당 최대 6개까지만 리턴하기 때문입니다.

페이지네이션을 추가해서 유저들이 다른 페이지를 이동해 모든 송장들을 보는것이 가능해집니다. URL 파라미터를 통해서 어떻게 페이지네이션을 구현하는지 알아봅시다.

`<Pagination/>` 컴퍼넌트로 이동하면 이것은 클라이언트 컴퍼넌트라는 걸 알 수 있습니다. 데이터 베이스의 비밀 값들을 노출 시킬 수 있는 데이터 가져오기를 클라이언트에서 하고 싶지 않을겁니다.(우리는 API 계층을 안 쓰고 있다는걸 기억하세요.)
대신, 서버에서 데이터를 가져와 prop으로 컴퍼넌트에 전달할 수 있습니다.

`/dashboard/invoices/page.tsx`에서, `fetchInvoicesPages`라는 새로운 함수를 불러(import)오세요. `searchParams`로부터 `query`를 함수 인자로 전달합니다.

<div class="code-with-file">
/app/dashboard/invoices/page.tsx

```
// ...
import { fetchInvoicesPages } from '@/app/lib/data';
 
export default async function Page({
  searchParams,
}: {
  searchParams?: {
    query?: string,
    page?: string,
  },
}) {
  const query = searchParams?.query || '';
  const currentPage = Number(searchParams?.page) || 1;
 
  const totalPages = await fetchInvoicesPages(query);
 
  return (
    // ...
  );
}
```
</div>

`fetchInvoicesPages`는 검색 쿼리에 따라 총 페이지의 갯수를 반홥합니다. 예를들어 12개의 송장이 검색결과로 매칭된다면, 각 페이지는 6개의 송장씩 총 페이지는 2 페이지가 됩니다.

다음으로, `totalPages` prop을 `<Pagination />` 컴퍼넌트에 전달하세요.

<div class="code-with-file">
/app/dashboard/invoices/page.tsx

```
// ...
 
export default async function Page({
  searchParams,
}: {
  searchParams?: {
    query?: string;
    page?: string;
  };
}) {
  const query = searchParams?.query || '';
  const currentPage = Number(searchParams?.page) || 1;
 
  const totalPages = await fetchInvoicesPages(query);
 
  return (
    <div className="w-full">
      <div className="flex w-full items-center justify-between">
        <h1 className={`${lusitana.className} text-2xl`}>Invoices</h1>
      </div>
      <div className="mt-4 flex items-center justify-between gap-2 md:mt-8">
        <Search placeholder="Search invoices..." />
        <CreateInvoice />
      </div>
      <Suspense key={query + currentPage} fallback={<InvoicesTableSkeleton />}>
        <Table query={query} currentPage={currentPage} />
      </Suspense>
      <div className="mt-5 flex w-full justify-center">
        <Pagination totalPages={totalPages} />
      </div>
    </div>
  );
}
```
</div>

`<Pagination />` 컴퍼넌트로 이동해 `usePathname`과 `useSearchParams` 훅을 불러(import)오세요. 이것들로 현재페이지를 가져오고, 새로운 페이지를 세팅할 겁니다. 또 이 컴퍼넌트의 주석들을 모두 지우세요. 우리 어플리케이션은 `<Pagination />`을 아직 구현하지 않았으므로, 일시적으로 동작하지 않습니다.

<div class="code-with-file">
/app/ui/invoices/pagination.tsx

```
'use client';
 
import { ArrowLeftIcon, ArrowRightIcon } from '@heroicons/react/24/outline';
import clsx from 'clsx';
import Link from 'next/link';
import { generatePagination } from '@/app/lib/utils';
import { usePathname, useSearchParams } from 'next/navigation';
 
export default function Pagination({ totalPages }: { totalPages: number }) {
  const pathname = usePathname();
  const searchParams = useSearchParams();
  const currentPage = Number(searchParams.get('page')) || 1;
 
  // ...
}
```
</div>

다음으로 `<Pagination>` 컴퍼넌트 내에 `createPageURL`이라는 함수를 생성합시다. 검색과 비슷하게, 새로운 페이지 넘버를 세팅하기 위해 `URLSearchParams`을, URL 문자열을 생성하기 위해 `pathName`을 사용합니다.

<div class="code-with-file">
/app/ui/invoices/pagination.tsx

```
'use client';
 
import { ArrowLeftIcon, ArrowRightIcon } from '@heroicons/react/24/outline';
import clsx from 'clsx';
import Link from 'next/link';
import { generatePagination } from '@/app/lib/utils';
import { usePathname, useSearchParams } from 'next/navigation';
 
export default function Pagination({ totalPages }: { totalPages: number }) {
  const pathname = usePathname();
  const searchParams = useSearchParams();
  const currentPage = Number(searchParams.get('page')) || 1;
 
  const createPageURL = (pageNumber: number | string) => {
    const params = new URLSearchParams(searchParams);
    params.set('page', pageNumber.toString());
    return `${pathname}?${params.toString()}`;
  };
 
  // ...
}
```
</div>

무엇이 일어나고 있는지 확인해보세요:

- `createPageURL`는 현재 검색 파라미터의 URL 인스턴스를 생성합니다.
- 그리고 "페이지" 파라미터를 함수의 인자로 받은 페이지 넘버로 업데이트 합니다.
- 마지막으로, 업데이트된 검색 파라미터와, 경로로 된 최종 URL로 페이지를 구축합니다.

`<Pagination>`컴퍼넌트의 나머지 부분은 스타일링과, 다른 상태들(첫번째 페이지, 마지막 페이지, 활성화된 페이지, 불가능한 상태 등)에 대한 것들을 다루고 있습니다. 우리는 이 강의에서 그러한 디테일을 다루지 않을 것이지만, 자유롭게 createPageURL이 호출되는 부분을 보아도 됩니다.

마지막으로 유저가 검색어를 입력하면, 페이지 넘버를 1로 다시 리셋하고 싶을 겁니다. `<Search>`컴퍼넌트의 `handleSearch` 함수를 업데이트 하는 것으로 할 수 있습니다.

<div class="code-with-file">
/app/ui/search.tsx

```
'use client';
 
import { MagnifyingGlassIcon } from '@heroicons/react/24/outline';
import { usePathname, useRouter, useSearchParams } from 'next/navigation';
import { useDebouncedCallback } from 'use-debounce';
 
export default function Search({ placeholder }: { placeholder: string }) {
  const searchParams = useSearchParams();
  const { replace } = useRouter();
  const pathname = usePathname();
 
  const handleSearch = useDebouncedCallback((term) => {
    const params = new URLSearchParams(searchParams);
    params.set('page', '1');
    if (term) {
      params.set('query', term);
    } else {
      params.delete('query');
    }
    replace(`${pathname}?${params.toString()}`);
  }, 300);

```
</div>


## 요약

축하합니다! Next.js API와 URL 파라미터를 이용한 검색과 페이지네이션(pagination)을 구현했습니다.

이번 챕터를 요약하면:

- client의 state를 이용하는 것 대신, URL 검색 파라미터를 이용해 검색과 페이지네이션을 구현했습니다.
- 서버로부터 데이터를 가져왔습니다.
- `useRouter` 훅을 사용해 부드러운 클라이언트 사이드 전환을 했습니다.

이러한 방식은 우리가 React로 작업을 했을때와 다를 겁니다. 하지만 다행히 이제 우리는 URL 검색 파라미터를 이용했을때의 장점과, 상태를 서버로 올리는것에 장점을 알게 되었습니다.


<div class="finish">
  <p class="finish__title">챕터 11을 완료했습니다.</p>
  <p>이제 우리의 대시보드가 검색과 페이지네이션 기능을 갖췄습니다.</p>
  <div class="next-box">
    <p class="next">다음</p>    
    <p class="next__title">데이터 변형(Mutating)</p>
    <p>Server Action으로 데이터를 변형하는 방법을 알아봅시다.</p>
    <a id="next__btn" href="https://nextjs.org/learn/dashboard-app/mutating-data">챕터 12 시작하기
    <svg data-testid="geist-icon" height="16" stroke-linejoin="round" viewBox="0 0 16 16" width="16" style="color: currentcolor;"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.53033 2.21968L9 1.68935L7.93934 2.75001L8.46967 3.28034L12.4393 7.25001H1.75H1V8.75001H1.75H12.4393L8.46967 12.7197L7.93934 13.25L9 14.3107L9.53033 13.7803L14.6036 8.70711C14.9941 8.31659 14.9941 7.68342 14.6036 7.2929L9.53033 2.21968Z" fill="currentColor"></path></svg>
    </a>
  </div>
</div>

## Ref
- [https://nextjs.org/learn/dashboard-app/adding-search-and-pagination](https://nextjs.org/learn/dashboard-app/adding-search-and-pagination)


<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/next-js-tutorial/css.css">
<script type="text/javascript" src="https://eso0117.github.io/web-practice/public/next-js-tutorial/js.js"></script>
