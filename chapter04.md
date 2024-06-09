## 들어가기 전에

이 글은 [Next.js - Chapter 4 : Creating Layouts and Pages](https://nextjs.org/learn/dashboard-app/creating-layouts-and-pages)를 번역한 글입니다.

# 레이아웃과 페이지 생성

지금까지 만든 어플리케이션은 오직 홈페이지만 가지고 있습니다. 이제 **페이지**와 **레이아웃**을 이용해 더 많은 라우트를 생성하는 방법을 배워봅시다.

## 이번 챕터에서...

여기 우리가 다룰 주제들이 있습니다.
- 파일시스템 라우팅 방법을 사용해 대시보드 라우트를 만들겁니다.
- 새로운 라우트 경로를 만들때 폴더와 파일의 역할을 이해합니다.
- 여러 대시보드 페이지 간에 공통 사용될 중첩된 레이아웃을 생성합니다.
- 같이 있기(colocation), 부분 렌더링, 루트(root) 레이아웃을 이해합니다.

## 중첩 라우팅

Next.js는 **폴더**들로 라우트를 생성하는 파일 시스템 라우팅을 사용합니다. 각 폴더는 **URL 경로**와 매치되는 **라우트 경로**를 나타냅니다.
<img src="https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Ffolders-to-url-segments.png&w=3840&q=75" alt="중첩 라우팅 설명">


각 라우트마다 `layout.tsx`와 `page.tsx` 파일을 이용하여 별도의 UI를 만들 수 있습니다.

`page.tsx`는 리액트 컴퍼넌트를 내보내고(export), 라우트 경로로 접근되어질 수 있도록 만들어진 특별한 Next.js 파일입니다. 지금까지 실습한 어플리케이션에서, 우리는 이미 `/app/page.tsx`파일을 가지고 있고 -- 라우트 `/`에서 접근가능한 홈페이지입니다.

중첩 라우트 경로의 페이지를 생성하기 위해, 각각의 다른 폴더안에 다른 폴더를 중첩시키고 그안에 `page.tsx`파일을 넣습니다. 예를들어:

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fdashboard-route.png&w=3840&q=75" alt="중첩된 폴더와 페이지 예시">

`/app/dashboard/page.tsx` 파일은 `/dashboard` 경로와 매치됩니다. 어떻게 작동하는지 보기 위해 페이지를 생성해봅시다!

## 대시보드 페이지 생성하기

`app`내에 `dashboard`라고 이름 지어진 폴더를 생성하세요. 그리고, 다음과 같은 내용으로 `page.tsx`파일을 `대시보드` 폴더 내에 생성하세요.

<div class="code-with-file">
/app/dashboard/page.tsx

```
export default function Page() {
  return <p>Dashboard Page</p>;
}
```
</div>

이제, 개발서버가 동작하고 있는지 확인하고 [http://localhost:3000/dashboard](http://localhost:3000/dashboard)에 방문해보세요. "Dashboard Page"라는 글자를 볼 수 있을겁니다.

폴더를 이용해 새로운 라우트 경로를 만들고, 그곳에 `page`파일을 추가하는 것 이것이 Next.js에서 다른 페이지를 만드는 방법입니다.

 Next.js에서 `page` 파일에 특별함 이름을 주는것으로 UI 컴퍼넌트나, 테스트 파일들, 라우트와 관련된 다른 코드들이 [`같이 있을(colocate)`](https://nextjs.org/docs/app/building-your-application/routing#colocation) 수 있게됩니다. 오직 `page`파일의 내용만이 공개적으로 접근가능합니다. 예를들어, `/ui`와 `/lib` 폴더들은 `/app`폴더 내에 당신의 경로를 따라 같이 있습니다.

## 실습해보기: 대시보드 페이지 생성하기

더 많은 라우트 만들기 실습을 해봅시다. 대시보드에서 2개의 페이지를 더 생성하세요.

1. **Customer 페이지**: 이 페이지는 [http://localhost:3000/dashboard/customers](http://localhost:3000/dashboard/customers)에서 접근 가능해야 합니다. 지금은 `<p>Customers Page</p>` 만 리턴해야 합니다.
2. **Invoices 페이지**: 이 페이지는 [http://localhost:3000/dashboard/invoices](http://localhost:3000/dashboard/invoices)에서 접근 가능해야 합니다. 지금은 마찬가지로 `<p>Invoices Page</p>`를 리턴하도록 합시다.

이 실습을 해결하기 위해 고민과 시간을 들인 후에, 준비가 되면 아래 버튼을 눌러 해답을 보세요.

<button class="show-and-hide-btn--hidden show-and-hide-btn btn" data-target="practice-01">
  <span data-btn-content>정답 보이기</span>
</button>

<div class="is-inactive" data-hide-target="practice-01">
다음과 같은 폴더 구조를 가져야 합니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Frouting-solution.png&w=3840&q=75" alt="실습 01 해답파일"/>
Customers 페이지:
<div class="code-with-file">
/app/dashboard/customers/page.tsx

```
export default function Page() {
  return <p>Customers Page</p>;
}
```
</div>
Invoices 페이지:
<div class="code-with-file">
/app/dashboard/invoices/page.tsx

```
export default function Page() {
  return <p>Invoices Page</p>;
}
```
</div>
</div>

## 대시보드 레이아웃 생성하기
대시보드는 여러페이지에서 공유되는 네비게이션 메뉴가 있습니다. Next.js에서는 이렇게 여러 페이지에서 공유되어지는 UI를 생성하기 위한 `layout.tsx`를 사용할 수 있습니다. 대시보드 페이지를 위한 레이아웃을 만들어 봅시다!

`/dashboard` 폴더 안에, `layout.tsx`파일을 새로 추가하고 아래 코드를 붙여넣읍시다.

<div class="code-with-file">
/app/dashboard/layout.tsx

```
import SideNav from '@/app/ui/dashboard/sidenav';
 
export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <div className="flex h-screen flex-col md:flex-row md:overflow-hidden">
      <div className="w-full flex-none md:w-64">
        <SideNav />
      </div>
      <div className="flex-grow p-6 md:overflow-y-auto md:p-12">{children}</div>
    </div>
  );
}
```
</div>

이 코드에서는 몇가지가 진행중입니다. 하나씩 살펴봅시다.

첫번째로, `<SideNav />` 컴퍼넌트를 레이아웃에 불러오고(import) 있습니다. 이 파일에 불러오는 어떤 컴퍼넌트도 레이아웃의 일부가 됩니다.

`<Layout />` 컴퍼넌트는 `children` prop을 받습니다. 이 것은(children) 페이지가 될 수 있고 또 다른 레이아웃이 될 수 있습니다. 지금은 아래와 같이 `/dashboard` 안에 페이지들은 자동으로 `<layout />` 내부로 중첩 될 겁니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fshared-layout.png&w=3840&q=75" alt="dashboard아래에 layout">

변경사항을 저장하고, 로컬호스트에서 모든게 정확히 돌아가는지 확인해봅시다. 다음과 같은 화면을 보아야 합니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fshared-layout-page.png&w=1920&q=75" alt="dashboard 레이아웃 추가 결과화면">

Next.js에서 레이아웃을 사용했을때 한가지 장점은, 페이지 이동에서 레이아웃은 렌더링이 되지 않지만, 오직 페이지 컴퍼넌트만 변경됩니다. 이것을 [부분 렌더링](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#3-partial-rendering)이라 부릅니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fpartial-rendering-dashboard.png&w=3840&q=75" alt="부분 렌더링 설명 이미지">

## 루트(Root) 레이아웃
챕터 3에서, 우리는 아래와 같이 `Inter` 폰트를 다른 레이아웃인 `/app/layout.tsx` 에 불러(import)왔습니다.

<div class="code-with-file">
/app/layout.tsx

```
import '@/app/ui/global.css';
import { inter } from '@/app/ui/fonts';
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className={`${inter.className} antialiased`}>{children}</body>
    </html>
  );
}
```
</div>

이것은 `루트 레이아웃(root layout)`이라고 부르고, 필수인 파일입니다. 우리가 루트 레이아웃(root layout)에 추가하는 어느 UI도 어플리케이션 내 모든 페이지에서 공유됩니다. 만약 `<html>` 그리고 `<body>`를 변경하고 싶다거나 metadata를 추가하고 싶다면 이 루트 레이아웃(root layout)을 이용할 수 있습니다.(metadata에 대해 [추후 챕터](https://nextjs.org/learn/dashboard-app/adding-metadata)에서 배울겁니다.)


<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">Next.js에서 레이아웃을 사용하는 목적은 무엇일까요?</p>
    <div class="option-list">
      <div class="option" data-question-number="01">
        <span class="option__number">A</span>
        <span class="option__desc">전역 에러 핸들러로서 동작하기 위해</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">B</span>
        <span class="option__desc">앱 전반에 걸쳐 데이터를 가져오고, 상태값을 관리하기 위해</span>
      </div>
      <div class="option" data-question-number="01" data-answer="true">
        <span class="option__number">C</span>
        <span class="option__desc">여러 페이지들 간 UI를 공유하기 위해</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">D</span>
        <span class="option__desc">전체 어플리케이션에서 시작위치로 동작하기 위해</span>
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


<div class="finish">
  <p class="finish__title">챕터 4를 완료했습니다.</p>
  <p>훌륭합니다, 대시보드 앱은 이제 서서히 완성되고 있습니다.</p>
  <div class="next-box">
    <p class="next">다음</p>    
    <p class="next__title">5: 페이지간 이동</p>
    <p>`&lt;Link \ &gt;` 컴퍼넌트를 사용하여 대시보드 페이지 간의 이동을 하는 방법을 배웁시다.</p>
    <a id="next__btn" href="https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-5-페이지간-이동">챕터 5 시작하기
    <svg data-testid="geist-icon" height="16" stroke-linejoin="round" viewBox="0 0 16 16" width="16" style="color: currentcolor;"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.53033 2.21968L9 1.68935L7.93934 2.75001L8.46967 3.28034L12.4393 7.25001H1.75H1V8.75001H1.75H12.4393L8.46967 12.7197L7.93934 13.25L9 14.3107L9.53033 13.7803L14.6036 8.70711C14.9941 8.31659 14.9941 7.68342 14.6036 7.2929L9.53033 2.21968Z" fill="currentColor"></path></svg>
    </a>
  </div>
</div>

## Ref
- [https://nextjs.org/learn/dashboard-app/creating-layouts-and-pages#creating-the-dashboard-page]


<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/next-js-tutorial/css.css">
<script type="text/javascript" src="https://eso0117.github.io/web-practice/public/next-js-tutorial/js.js"></script>
