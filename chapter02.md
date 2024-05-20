## 들어가기 전에
이 글은 [Next.js 튜토리얼](https://nextjs.org/learn/dashboard-app/css-styling)을 번역한 글입니다.

# CSS 스타일하기
현재, 홈페이지는 어떤 스타일도 갖고 있지 않을겁니다. Next.js 어플리케이션을 꾸밀 다양한 방법을 알아봅시다.

## 이번 챕터에서...
여기 우리가 다룰 주제들이 있습니다.
- 전역 CSS 파일을 어플리케이션에 추가하는 방법
- 두가지 다른 스타일링 방법: Tailwind 그리고 CSS modules
- clsx 유틸리티 패키지로 class 명을 조건부로 넣는 방법

## 전역 스타일
`/app/ui` 폴더를 봅시다, `global.css` 파일을 찾을 수 있을겁니다. 이 파일을 추가해 어플리케이션 내에 모든 라우트에 CSS 초기화 룰이나 link같은 사이드 전역에 쓰일만한 CSS 규칙들을 적용할 수 있습니다.

`global.css`는 앱 내에 어디서든 불러(import)들일 수 있습니다, 그러나 보통 최상위 컴퍼넌트에 추가하는게 좋은 관행입니다. 이것이 [최상위 레이아웃](https://nextjs.org/docs/app/building-your-application/routing/pages-and-layouts#root-layout-required) 입니다.(나중에 다룰거에요)

`/app/layout.tsx`로 이동해서 `global.css` 파일을 불러(import)들여서 전역스타일을 어플리케이션에 추가하세요:

```
import '@/app/ui/global.css';
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

개발서버가 여전히 동작하는 동안, 변경사항을 저장하면 브라우저에서 미리보기를 할 수 있습니다. 홈페이지는 아래와 같은 형태를 가질겁니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fhome-page-with-tailwind.png&w=1920&q=75" alt="NextJS 튜토리얼 chapter 2 글로벌 스타일 적용이미지">

잠깐만요, 당신은 아무 CSS 규칙들을 추가하지 않았습니다. 이 스타일들은 어디서 온 걸까요?

`global.css`을 본다면, @tailwind라는 명령문을 볼 수 있습니다:

```
@tailwind base;
@tailwind components;
@tailwind utilities;
```

## Tailwind
[Tailwind](https://tailwindcss.com/)는 [유틸리티 클래스](https://tailwindcss.com/docs/utility-first)들을 바로 TSX 마크업에 빠르게 쓰면서 개발 프로세스 속도를 향상 시킬 수 있는 CSS프레임 워크 입니다.

Tailwind에서는, 클래스명을 추가해서 요소들을 꾸밀 수 있습니다. 예를들어, `text-blue-500` 클래스를 추가하는 것은 `<h1>` text를 파랗게 만들겁니다.

```
<h1 className="text-blue-500">I'm blue!</h1>
```

비록 CSS 스타일이 전역적으로 공유되지만, 각 클래스는 각 요소에 개별적으로 적용됩니다. 이 말은, 만약 요소를 더하거나 제거해도, 별도의 서식양식 유지나 스타일 충돌, 앱이 커지면서 CSS 번들이 커지는것을 걱정할 필요가 없다는 겁니다.

프로젝트를 시작하기 위해 `create-next-app`을 사용했을때, Next.js는 Tailwind를 사용할지 물을겁니다. `Yes`를 선택한다면, Next.js는 자동으로 필요한 패키지들과 Tailwind 설정을 할겁니다.

`/app/page.tsx`를 본다면, 이 예제에서 우리가 Tailwind 클래스들을 사용하는걸 볼 수 있습니다.

```
import AcmeLogo from '@/app/ui/acme-logo';
import { ArrowRightIcon } from '@heroicons/react/24/outline';
import Link from 'next/link';
 
export default function Page() {
  return (
    // These are Tailwind classes:
    <main className="flex min-h-screen flex-col p-6">
      <div className="flex h-20 shrink-0 items-end rounded-lg bg-blue-500 p-4 md:h-52">
    // ...
  )
}
```

만약 이번이 Tailwind를 처음 사용하는 것이어도 걱정하지마세요. 시간을 아끼기 위해 우린 이미 모든 당신이 사용할 컴퍼넌트를 스타일 해놨습니다.

Tailwind를 가지고 놀아봅시다! 아래 코드를 복사하고 `/app/page.tsx` 내에 `<p>`태그 위에 넣어봅시다.

```
<div
  className="h-0 w-0 border-b-[30px] border-l-[20px] border-r-[20px] border-b-black border-l-transparent border-r-transparent"
/>
```

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">위에 나온 코드를 적용했을때 어떤 모양이 보이나요?</p>
    <div class="option-list">
      <div class="option" data-question-number="01">
        <span class="option__number">A</span>
        <span class="option__desc">A yellow star</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">B</span>
        <span class="option__desc">A blue triangle</span>
      </div>
      <div class="option" data-question-number="01" data-answer="true">
        <span class="option__number">C</span>
        <span class="option__desc">A black triangle</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">D</span>
        <span class="option__desc">A red circle</span>
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

만약 전통적인 CSS 규칙을 쓰거나, JSX - CSS 간 모듈로부터 스타일들을 분리하고 싶다면 훌륭한 대안들이 있습니다.

## CSS Modules
[CSS Modules](https://nextjs.org/docs/basic-features/built-in-css-support)은 자동으로 생성되는 유니크한 클래스 이름으로 컴퍼넌트의 CSS 영역 설정을 가능하게합니다. 그래서 스타일의 충돌도 걱정할 필요 없습니다.

이 강의에서 Tailwind를 계속 쓸 것입니다만 어떻게 CSS모듈을 이용해서 퀴즈의 같은 결과를 얻을 수 있는지 잠깐 봅시다.

/app/ui 내부에, `home.module.css` 파일을 새로 생성하고, 다음의 CSS를 추가합시다.

```
.shape {
  height: 0;
  width: 0;
  border-bottom: 30px solid black;
  border-left: 20px solid transparent;
  border-right: 20px solid transparent;
}
```

그다음 `/app/page.tsx` 파일에서 해당 스타일을 불러오고(import) `<div>`의 클래스를 방금 추가한 `styles.shape`로 바꿉시다:
```
import styles from '@/app/ui/home.module.css';
<div className={styles.shape} />;
```
변경사항을 저장하고, 브라우저에서 봅시다. 전과 같은 모양을 볼 수 있을 겁니다.
Tailwind와 CSS Modules은 Next.js 어플리케이션을 스타일하는 가장 많이 쓰는 방법들입니다. 둘 중 하나를 사용하는건 선호의 문제입니다 - 같은 어플리케이션에서 두가지 모두를 사용할 수도 있습니다.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">CSS 모듈을 사용할때의 장점 하나는 무엇인가요?</p>
    <div class="option-list">
      <div class="option" data-question-number="02">
        <span class="option__number">A</span>
        <span class="option__desc">다른 파일들간 CSS관리를 용이하게 하여 CSS 클래스들의 전역 영역을 확대합니다.</span>
      </div>
      <div class="option" data-question-number="02" data-answer="true">
        <span class="option__number">B</span>
        <span class="option__desc">CSS 클래스들을 컴퍼넌트에 대해 지역적 영역을 갖도록 만드는 방법을 제공하여, 스타일간의 충돌을 줄입니다.</span>
      </div>
      <div class="option" data-question-number="02">
        <span class="option__number">C</span>
        <span class="option__desc">자동으로 압축하고 CSS파일 크기를 줄여 페이지 로딩을 빠르게 합니다.</span>
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

## 클래스 변경(toggle)을 위한 `clsx` 라이브러리 사용하기

때때로 요소를 상태가, 다른 조건에 의해 다르게 스타일 해야할 경우가 있습니다.

[`clsx`](https://www.npmjs.com/package/clsx)는 클래스명을 쉽게 바꿀(toggle)수 있게 해주는 라이브러리 입니다. 더 세부적인 사항을 위해 [문서](https://github.com/lukeed/clsx)를 읽어보는걸 추천하지만, 기본적인 사용법은 다음과 같습니다:
- `status`를 받는 `InvoiceStatus` 컴퍼넌트를 생성한다고 가정합시다. 상태는 `pending` 또는 `paid`가 될 수 있습니다.
- 만약 `paid`라면, 색을 초록색으로 하고 싶습니다. `pending`이라면 회색으로 하고 싶습니다.
아래와 같이, `clsx`를 사용하여 클래스를 조건부로 받을 수 있습니다.

```
import clsx from 'clsx';
 
export default function InvoiceStatus({ status }: { status: string }) {
  return (
    <span
      className={clsx(
        'inline-flex items-center rounded-full px-2 py-1 text-sm',
        {
          'bg-gray-100 text-gray-500': status === 'pending',
          'bg-green-500 text-white': status === 'paid',
        },
      )}
    >
    // ...
)}
```

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">코드 에디터에서 `clsx`를 찾아봅시다, 어떤 컴퍼넌트가 클래스 명을 조건으로 받기 위해 이것을 쓰고있나요?</p>
    <div class="option-list">
      <div class="option" data-question-number="03"  data-answer="true">
        <span class="option__number">A</span>
        <span class="option__desc">'status.tsx' 그리고 'pagination.tsx'</span>
      </div>
      <div class="option" data-question-number="03">
        <span class="option__number">B</span>
        <span class="option__desc">'table.tsx' 그리고 'status.tsx'</span>
      </div>
      <div class="option" data-question-number="03">
        <span class="option__number">C</span>
        <span class="option__desc">'nav-links.tsx' 그리고 'table.tsx'</span>
      </div>
    </div>
    <div class="quiz__btn-container">
      <button class="quiz__btn"
        data-js-check-answer data-question="03">
        정답 확인
      </button>
    </div>
  </div>  
</div>

## 다른 스타일하기 옵션
우리가 이야기한것 외에 추가적으로, Next.js어플리케이션을 스타일할 수 있습니다:
- `.css` `.scss` 파일들을 불러(import)올수 있게해주는 Sass
- [styled-jsx](https://github.com/vercel/styled-jsx), [styled-components](https://github.com/vercel/next.js/tree/canary/examples/with-styled-components), [emotion](https://github.com/vercel/next.js/tree/canary/examples/with-emotion) 같은 CSS-in-JS 라이브러리들

더 많은 정보를 위해 [CSS 문서](https://nextjs.org/docs/app/building-your-application/styling)를 읽어보세요.



<div class="finish">
  <p class="finish__title">챕터 2를 완료했습니다.</p>
  <p>잘했습니다! Next.js 어플리케이션을 꾸밀 다양한 방법을 배웠습니다.</p>
  <div class="next-box">
    <p class="next">다음</p>    
    <p class="next__title">3: 폰트와 이미지 최적화</p>
    <p>커스텀 폰트와 히어로 이미지를 추가하면서 홈페이지 작업을 계속 할겁니다.</p>
    <a id="next__btn" href="https://thewys.tistory.com/entry/NextJS-%ED%8A%9C%ED%86%A0%EB%A6%AC%EC%96%BC-%EC%B1%95%ED%84%B0-3-%ED%8F%B0%ED%8A%B8%EC%99%80-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EC%B5%9C%EC%A0%81%ED%99%94">챕터 3 시작하기
    <svg data-testid="geist-icon" height="16" stroke-linejoin="round" viewBox="0 0 16 16" width="16" style="color: currentcolor;"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.53033 2.21968L9 1.68935L7.93934 2.75001L8.46967 3.28034L12.4393 7.25001H1.75H1V8.75001H1.75H12.4393L8.46967 12.7197L7.93934 13.25L9 14.3107L9.53033 13.7803L14.6036 8.70711C14.9941 8.31659 14.9941 7.68342 14.6036 7.2929L9.53033 2.21968Z" fill="currentColor"></path></svg>
    </a>
  </div>
</div>

## Ref
- [https://nextjs.org/learn/dashboard-app/css-styling]

<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/next-js-tutorial/css.css">
<script type="text/javascript" src="https://eso0117.github.io/web-practice/public/next-js-tutorial/js.js"></script>