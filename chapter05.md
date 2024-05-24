## 들어가기 전에

이 글은 [Next.js - Chapter 5 : Navigating Between Pages](https://nextjs.org/learn/dashboard-app/navigating-between-pages)강의를 번역한 글입니다.

# 페이지간 이동

이전 챕터에서, 대시보드 레이아웃과 페이지들을 만들었습니다. 이제 유저가 대시보드 라우트간 이동을 할 수 있도록 링크를 추가해봅시다.

## 이번 챕터에서...

여기 우리가 다룰 주제들이 있습니다.
- `next/link`를 사용하는 방법
- `usePathname()` 후크로 액티브 링크를 생성하는 방법
- Next.js에서 내비게이션이 동작하는 원리

## 왜 내비게이션을 최적화 하나요?
페이지간 링크를 생성하기 위해서, 우리는 전통적인 방법인 `<a>` HTML 태그를 사용해 왔습니다. 사이드바 링크는 `<a>`태그를 사용하고, 브라우저에서 home, invoices 그리고 customer페이지간 이동을 할 때 어떤일이 일어나는지 주목해보세요.

보셨나요?

각각의 이동마다 전체 페이지가 리프레쉬 됩니다.

## <Link> 컴퍼넌트
Next.js에서는 어플리케이션에서 페이지간 링크를 걸때 `<Link />` 컴퍼넌트를 사용할 수 있습니다. `<Link>`는 Javascript를 통해 [client-side navigation](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#how-routing-and-navigation-works)을 할 수 있게 해줍니다.

`<Link />` 컴퍼넌트를 사용하기 위해, `/app/ui/dashboard/nav-links.tsx`를 열고 [`next/link`](https://nextjs.org/docs/app/api-reference/components/link)에서 `Link` 컴퍼넌트를 불러(import)와주세요. 그리고 나서, `<a>` 태그를 `<Link>`로 바꿉시다.

<div class="code-with-file">
/app/ui/dashboard/nav-links.tsx

```
import {
  UserGroupIcon,
  HomeIcon,
  DocumentDuplicateIcon,
} from '@heroicons/react/24/outline';
import Link from 'next/link';
 
// ...
 
export default function NavLinks() {
  return (
    <>
      {links.map((link) => {
        const LinkIcon = link.icon;
        return (
          <Link
            key={link.name}
            href={link.href}
            className="flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3"
          >
            <LinkIcon className="w-6" />
            <p className="hidden md:block">{link.name}</p>
          </Link>
        );
      })}
    </>
  );
}
```
</div>

여기서 볼 수 있듯, `Link` 컴퍼넌트는 `<a>` 태그와 굉장히 유사합니다.
단지 `<a href="...">` 대신 `<Link href="...">`을 사용하는것 뿐입니다.

변경사항을 저장하고, 로컬호스트에서 동작을 확인해봅시다. 
이제 전체 페이지가 리프레쉬 되는것 없이 페이지간 이동이 가능한걸 볼 수 있습니다. 어플리케이션의 일부는 서버쪽에서 렌더링 되지만, 전체 페이지의 리프레쉬는 없어서 이게 마치 웹 앱처럼 느껴집니다.

## 자동 코드 분할과 미리 가져오기
페이지 이동 경험을 좋게 만들기위해서, Next.js는 자동적ㅛ으로 route 경로에 따른 코드를 쪼갭니다. 이것은 초기에 불러올때 모든 코드를 불러오는 전통적인 React의 [SPA](https://developer.mozilla.org/en-US/docs/Glossary/SPA)와는 다른 부분입니다.

경로에 따라 코드를 쪼갠다는 것은 페이지가 별도로 구성되어 있다는것 입니다. 그래서 만약 특정 페이지가 에러가 난다해도, 어플리케이션의 나머지 부분은 여전히 동작합니다.

더욱이, 프로덕션 환경에서는 [`<Link>`](https://nextjs.org/docs/api-reference/next/link) 브라우저 화면상에 컴퍼넌트를 사용하는때마다 Next.js는 백그라운드에서 자동으로 링크된 경로의 코드를 미리 불러옵니다. 유저가 클릭하는 시간에, 목적지 페이지의 코드는 백그라운드에서 이미 불러와져 있고, 이것이 페이지간 전환을 거의-즉시 이뤄지게 합니다!

[내비게이션이 동작하는 방법](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#how-routing-and-navigation-works)에서 더 배워보세요.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">프로덕션 환경에서 &lt;Link&gt;컴퍼넌트가 브라우저의 화면상에 나타날때, Next.js는 무엇을 하나요?</p>
    <div class="option-list">
      <div class="option" data-question-number="01">
        <span class="option__number">A</span>
        <span class="option__desc">추가적인 CSS 다운로드</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">B</span>
        <span class="option__desc">이미지 미리 불러오기</span>
      </div>
      <div class="option" data-question-number="01" data-answer="true">
        <span class="option__number">C</span>
        <span class="option__desc">링크가 걸린 경로의 코드를 미리 가져오기</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">D</span>
        <span class="option__desc">링크된 경로에 대해 lazy loading을 가능하게 하기</span>
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

## 패턴: 활성화된 링크 보여주기

유저가 현재 어느 페이지에 있는지 나타내기 위해 활성화 된 링크(active link)를 보여주는 것은 일반적인 UI 패턴입니다. 이것을 하기 위해서는 URL에서 현재 어느 경로에 있는지 받을 필요가 있습니다. Next.js는 경로를 확인하고, 위의 UI패턴을 실행할 수 있는 [`usePathname()`](https://nextjs.org/docs/app/api-reference/functions/use-pathname)라 불리는 훅을 제공하고 있습니다.

[`usePathname()`](https://nextjs.org/docs/app/api-reference/functions/use-pathname)는 훅이므로, `nav-links.tsx`를 클라이언트 컴퍼넌트로 바꿀 필요가 있습니다. 파일 최상단에 `"use client"` 표기를 추가하고, `next/navigation`에서 `usePathname()`을 불러(import)옵시다.

<div class="code-with-file">
/app/ui/dashboard/nav-links.tsx

```
'use client';
 
import {
  UserGroupIcon,
  HomeIcon,
  InboxIcon,
} from '@heroicons/react/24/outline';
import Link from 'next/link';
import { usePathname } from 'next/navigation';
 
// ...
```
</div>

다음으로, `<NavLink />` 컴퍼넌트 안 `pathname`이라는 변수에 해당 경로를 할당하세요.

<div class="code-with-file">
/app/ui/dashboard/nav-links.tsx

```
export default function NavLinks() {
  const pathname = usePathname();
  // ...
}
```
</div>

[CSS 스타일하기](https://thewys.tistory.com/entry/NextJS-%ED%8A%9C%ED%86%A0%EB%A6%AC%EC%96%BC-%EC%B1%95%ED%84%B0-2-CSS-%EC%8A%A4%ED%83%80%EC%9D%BC%ED%95%98%EA%B8%B0) 챕터에서 소개되었던 `clsx`라이브러리를 사용해서 링크가 활성화 되었을때를 조건으로 클래스를 적용할 수 있습니다. `link.href`가 `pathname`과 일치할때, 해당 링크는 파란 글자와, 밝은 파란색 배경으로 보여야 합니다.

여기 `nav-links.tsx`의 최종코드가 있습니다.

<div class="code-with-file">
/app/ui/dashboard/nav-links.tsx

```
'use client';
 
import {
  UserGroupIcon,
  HomeIcon,
  DocumentDuplicateIcon,
} from '@heroicons/react/24/outline';
import Link from 'next/link';
import { usePathname } from 'next/navigation';
import clsx from 'clsx';
 
// ...
 
export default function NavLinks() {
  const pathname = usePathname();
 
  return (
    <>
      {links.map((link) => {
        const LinkIcon = link.icon;
        return (
          <Link
            key={link.name}
            href={link.href}
            className={clsx(
              'flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3',
              {
                'bg-sky-100 text-blue-600': pathname === link.href,
              },
            )}
          >
            <LinkIcon className="w-6" />
            <p className="hidden md:block">{link.name}</p>
          </Link>
        );
      })}
    </>
  );
}
```
</div>

이제 저장하고 로컬호스트에서 확인해봅시다. 이제 파란색으로 활성화된 링크를 볼 수 있습니다.

<div class="finish">
  <p class="finish__title">챕터 5를 완료했습니다.</p>
  <p>페이지간 링크를 거는 방법과, Next.js에서 클라이언트 사이드 페이지 이동을 했을때의 장점을 배웠습니다.</p>
  <div class="next-box">
    <p class="next">다음</p>    
    <p class="next__title">6: 데이터 베이스 세팅하기</p>
    <p>이제 진짜 데이터를 가져오기 위해 데이터베이스를 생성합시다!</p>
    <a id="next__btn" href="https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-6-데이터-베이스-세팅하기">챕터 6 시작하기
    <svg data-testid="geist-icon" height="16" stroke-linejoin="round" viewBox="0 0 16 16" width="16" style="color: currentcolor;"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.53033 2.21968L9 1.68935L7.93934 2.75001L8.46967 3.28034L12.4393 7.25001H1.75H1V8.75001H1.75H12.4393L8.46967 12.7197L7.93934 13.25L9 14.3107L9.53033 13.7803L14.6036 8.70711C14.9941 8.31659 14.9941 7.68342 14.6036 7.2929L9.53033 2.21968Z" fill="currentColor"></path></svg>
    </a>
  </div>
</div>

## Ref
- [https://nextjs.org/learn/dashboard-app/navigating-between-pages](https://nextjs.org/learn/dashboard-app/navigating-between-pages)


<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/next-js-tutorial/css.css">
<script type="text/javascript" src="https://eso0117.github.io/web-practice/public/next-js-tutorial/js.js"></script>
