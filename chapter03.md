## 들어가기 전에
이 글은 [Next.js - Chapter 3 : Optimizing Fonts and Images](https://nextjs.org/learn/dashboard-app/optimizing-fonts-images)를 번역한 글입니다.

# 폰트와 이미지 최적화
이전 챕터에서, 우리는 Next.js 어플리케이션에서 스타일하는 방법을 배웠습니다. 이번엔 커스텀 폰트와 히어로 이미지를 추가하면서 계속해서 홈페이지에서 작업을 해봅시다.

## 이번 챕터에서...
여기 우리가 다룰 주제들이 있습니다.
- `next/font`에 커스텀 폰트를 추가하는 방법
- `next/image`에 이미지를 추가하는 방법
- Next.js에 이미지와 폰트들을 최적화 하는 방법

## 왜 폰트를 최적화 하나요?
폰트는 웹사이트 디자인에서 중요한 역할을 합니다. 그러나 만약 폰트를 가져오고 불러와야 한다면, 커스텀 폰트를 사용하는것은 웹 퍼포먼스에 영향을 미칠 수 있습니다.

[Cumulative Layout Shift](https://web.dev/cls/)는 웹사이트의 퍼포먼스와 유저 경험을 평가할때 사용하는 측정항목 입니다. 폰트를 쓰면, 브라우저가 처음에 대체 폰트나 시스템 폰트로 렌더링을 하고 커스텀 폰트가 다 불러온 후 이것을 대체할때 레이아웃의 변형이 일어납니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Ffont-layout-shift.png&w=3840&q=75" alt="커스텀 폰트에 의한 layout shift">

Next.js는 어플리케이션 내에서 `next/font` 모듈을 쓸 때, 자동으로 최적화를 해줍니다. 이것은 빌드시간에 미리 폰트를 다운로드 받고 다른 정적 assets과 함께 호스팅합니다. 이것은 유저가 당신의 어플리케이션을 방문할때, 폰트때문에 퍼포먼스에 영향을 미칠 추가적인 네트워크 요청은 하지 않는다는것을 의미합니다.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">Next.js는 어떻게 폰트 최적화를 하나요?</p>
    <div class="option-list">
      <div class="option" data-question-number="01">
        <span class="option__number">A</span>
        <span class="option__desc">퍼포먼스를 향상시킬 추가적인 네트워크에 의해</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">B</span>
        <span class="option__desc">모든 커스텀 폰트를 불가능하게 함으로서</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">C</span>
        <span class="option__desc">동작시간에 모든 폰트를 미리 불러옵니다.</span>
      </div>
      <div class="option" data-question-number="01" data-answer="true">
        <span class="option__number">D</span>
        <span class="option__desc">추가적인 네트워크 요청이 필요없도록 다른 정적파일과 함께 폰트를 호스팅합니다.</span>
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

## 주 폰트 추가하기
어플리케이션에 커스텀 구글 폰트를 추가하고 어떻게 동작하는지 봅시다!

`/app/ui` 폴더 내에, `fonts.ts`란 이름으로 파일을 만듭시다. 어플리케이션 전반에 사용될 폰트들을 저장한 파일입니다.

`next/font/google`모듈에서 `Inter`폴트를 불러오세요(import) - 이것은 주 폰트가 될겁니다. 그리고는 불러오고 싶은 [subset](https://fonts.google.com/knowledge/glossary/subsetting)을 명시하세요. 여기서는 `latin`입니다:

<div class="code-with-file">
/app/ui/fonts.ts

```
import { Inter } from 'next/font/google';
 
export const inter = Inter({ subsets: ['latin'] });
```
</div>

마지막으로, /app/layout.tsx에 `<body>`에 폰트를 추가하세요

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


`Inter`클래스를 `<body>`에 추가함으로서, 어플리케이션 전반에 해당 폰트가 적용이 됩니다. 또한 폰트를 부드럽게 나타나게해주는 Tailwind의 [antialiased](https://tailwindcss.com/docs/font-smoothing)도 불러오고 있습니다. 이 강의에서 필수는 아니지만, 글을 보기 좋게 만들어줍니다.

브라우저로 가서, 개발 툴을 열고 `body`를 선택하세요. `Inter`와 `Inter_Fallback`이 스타일에 적용된걸 볼 수 있습니다.

## 실습해보기: 두번째 폰트 추가하기
어플리케이션의 특정 요소에 폰트를 더 추가할 수 있습니다.

이제 당신의 차례입니다! `fonts.ts` 파일에서, `Lusitana`라고 불리는 두번째 폰트를 불러오세요 그리고 `/app/page.tsx`파일의 `<p>`태그에 넘겨주세요. 전에 했던것처럼 폰트의 서브셋을 명시하는것 외에도, 폰트의 weigiht를 명시해야 합니다.

준비가 되면, 정답을 보려면 아래 코드를 펼치세요.

<div class="hint">

**Hints:**
- 만약 폰트의 옵션으로 어떤 weight를 넘겨야 할지 잘 모르겠다면, 코드에디터의 에러표시를 확인하세요.
- [Google Fonts](https://fonts.google.com/)페이지를 방문해서 어떤 옵션들이 가능한지 `Lustina`폰트를 검색해보세요.
- [여러가지 폰트 추가하기](https://nextjs.org/docs/app/building-your-application/optimizing/fonts#using-multiple-fonts)와 [옵션 전체리스트](https://nextjs.org/docs/app/api-reference/components/font#font-function-arguments) 문서를 확인하세요.

</div>

<button class="show-and-hide-btn--hidden show-and-hide-btn btn" data-target="practice-lusitana">
  <span data-btn-content>정답 보이기</span>
</button>

<div class="code-with-file is-inactive" data-hide-target="practice-lusitana">
/app/ui/fonts.ts

```
import { Inter, Lusitana } from 'next/font/google';
 
export const inter = Inter({ subsets: ['latin'] });
 
export const lusitana = Lusitana({
  weight: ['400', '700'],
  subsets: ['latin'],
});
```
</div>
<div class="code-with-file is-inactive" data-hide-target="practice-lusitana">
/app/page.tsx

```
import AcmeLogo from '@/app/ui/acme-logo';
import { ArrowRightIcon } from '@heroicons/react/24/outline';
import Link from 'next/link';
import { lusitana } from '@/app/ui/fonts';
 
export default function Page() {
  return (
    // ...
    <p
      className={`${lusitana.className} text-xl text-gray-800 md:text-3xl md:leading-normal`}
    >
      <strong>Welcome to Acme.</strong> This is the example for the{' '}
      <a href="https://nextjs.org/learn/" className="text-blue-500">
        Next.js Learn Course
      </a>
      , brought to you by Vercel.
    </p>
    // ...
  );
}
```
</div>

마지막으로, `<AcmeLogo />` 컴퍼넌트 Lusitana를 씁니다. 이것은 에러를 안나게 하려고 주석처리 되어있었고, 이제 주석을 풀 수 있습니다.

<div class="code-with-file">
/app/page.tsx

```
// ...
 
export default function Page() {
  return (
    <main className="flex min-h-screen flex-col p-6">
      <div className="flex h-20 shrink-0 items-end rounded-lg bg-blue-500 p-4 md:h-52">
        <AcmeLogo />
        {/* ... */}
      </div>
    </main>
  );
}
```
</div>

훌륭합니다. 이제 2개의 커스텀 폰트를 당신의 어플리케이션의 추가했습니다. 이제 홈페이지에 히어로 이미지를 추가해봅시다.

## 왜 이미지를 최적하나요?
Next.js는 최상단 [`/public`](https://nextjs.org/docs/app/building-your-application/optimizing/static-assets) 폴더 하위에 정적 파일들을(assets) 제공할 수 있습니다. `/public` 안에 있는 파일들은 어플리케이션에서 참조될 수 있습니다.

표준 HTML로는, 아래와 같이 이미지를 추가할 수 있을겁니다.
```
<img
  src="/hero.png"
  alt="Screenshots of the dashboard project showing desktop version"
/>
```

그러나, 이것은 당신이 몇가지 메뉴얼 작업을 해야한다는걸 의미합니다.

- 다른 화면 사이즈에 맞게 이미지가 responsive 되도록 해야합니다.
- 다른 디바이스들에 맞게 이미지 사이즈를 명시해야합니다.
- 이미지가 불러와지면서 레이아웃 변형이 되는걸 막아야합니다.
- 유저의 보이는 영역 넘어의 이미지를 늦게 로딩되에 해야합니다.

이미지 최적화는 웹 개발에서 그것 자체만으로도 고려할것이 많은 주제입니다. 이러한 최적화들을 일일이 작업하는것보다는, 자동으로 이미지를 최적화 해주는 `next/image` 컴퍼넌트를 사용할 수 있습니다.

## <Image> 컴퍼넌트
<Image> 컴퍼넌트는 HTML `<img>`태그의 확장이고, 다음과 같은 이미지 최적화가 자동으로 됩니다.

- 이미지를 불러올때 레이아웃 변형을 막아줍니다.
- 작은 화면 사이즈에 큰 사이즈의 이미지가 전달되지 않도록 리사이징 해줍니다.
- 이미지들은 기본적으로 늦게 불러와집니다(뷰포트에 들어오면 이미지를 로드합니다)
- 브라우저가 지원을 한다면, [WebP](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Image_types#webp) 그리고 [AVIF](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Image_types#avif_image) 같은 현대 포맷을 제공해줍니다.

## PC화면 히어로 이미지 추가하기
`<Image>` 컴퍼넌트를 사용해봅시다. `/public`폴더를 들여다보면, 두 이미지가 있다는걸 볼겁니다: `hero-desktop.png`, `hero-mobile.png`. 이 두 이미지들은 완전히 다릅니다 그리고 이 이미지는 유저의 디바이스가 PC냐 mobile이냐에 따라 다르게 뜰겁니다.

`/app/page.tsx` 파일내에, [`next/image`](https://nextjs.org/docs/api-reference/next/image) 컴퍼넌트를 불러옵시다(import). 그리고 주석 부분 아래 이미지를 추가합니다.

<div class="code-width-file">
/app/page.tsx

```
import AcmeLogo from '@/app/ui/acme-logo';
import { ArrowRightIcon } from '@heroicons/react/24/outline';
import Link from 'next/link';
import { lusitana } from '@/app/ui/fonts';
import Image from 'next/image';
 
export default function Page() {
  return (
    // ...
    <div className="flex items-center justify-center p-6 md:w-3/5 md:px-28 md:py-12">
      {/* Add Hero Images Here */}
      <Image
        src="/hero-desktop.png"
        width={1000}
        height={760}
        className="hidden md:block"
        alt="Screenshots of the dashboard project showing desktop version"
      />
    </div>
    //...
  );
}
```
</div>

여기에서, `width`를 1000px 그리고 `height` 760px 으로 세팅하고 있습니다. 레이아웃 변형을 피하기 위해 width와 height를 명시하는건 좋은 활용입니다. 이 값들은 원본 이미지와 **같은** 비율을 갖고 있어야 합니다.

또한 모바일 화면에서 안 보여지기 위한 `hidden` 클래스, 그리고 PC화면에서 보여지기 위한 `md:block`를 볼겁니다.

이것은 이제 홈페이지에서 보여질 화면입니다:

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fhome-page-with-hero.png&w=1920&q=75" alt="NextJS 튜토리얼 PC 이미지 추가">

## 실습해보기: 모바일 히어로 이미지 추가하기
이제 당신의 차례입니다! 방금 막 추가한 이미지 아래에 또다른 `hero-mobile.png`를 위한 `<Image>` 컴퍼넌트를 추가하세요.
- 이미지는 `width` 560px `height` 620px를 가져야 합니다.
- 모바일 화면에서 보여야하며, PC화면에선 안 보여야 합니다. - 모바일과 PC화면에서 이미지들이 적절히 바뀌는지 개발툴로 확인할 수 있습니다.

준비가 되면 정답을 보기위해 아래 코드를 펼쳐보세요.

<button class="show-and-hide-btn--hidden show-and-hide-btn btn" data-target="practice-mobile-image">
  <span data-btn-content>정답 보이기</span>
</button>

<div class="code-with-file is-inactive" data-hide-target="practice-mobile-image">
/app/page.tsx

```
import AcmeLogo from '@/app/ui/acme-logo';
import { ArrowRightIcon } from '@heroicons/react/24/outline';
import Link from 'next/link';
import { lusitana } from '@/app/ui/fonts';
import Image from 'next/image';
 
export default function Page() {
  return (
    // ...
    <div className="flex items-center justify-center p-6 md:w-3/5 md:px-28 md:py-12">
      {/* Add Hero Images Here */}
      <Image
        src="/hero-desktop.png"
        width={1000}
        height={760}
        className="hidden md:block"
        alt="Screenshots of the dashboard project showing desktop version"
      />
      <Image
        src="/hero-mobile.png"
        width={560}
        height={620}
        className="block md:hidden"
        alt="Screenshot of the dashboard project showing mobile version"
      />
    </div>
    //...
  );
}
```
</div>

훌륭합니다! 당신의 홈페이지에 커스텀 폰트와 히어로 이미지가 생겼습니다.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">참 혹은 거짓: 크기가 없는 이미지와 웹 폰트들은 레이아웃 변형의 일반적인 원인들입니다.</p>
    <div class="option-list">
      <div class="option" data-question-number="02" data-answer="true">
        <span class="option__number">A</span>
        <span class="option__desc">참</span>
      </div>
      <div class="option" data-question-number="02">
        <span class="option__number">B</span>
        <span class="option__desc">거짓</span>
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

## 읽을거리
원격이미지 최적화 그리고 지역 폰트 파일 사용 같은, 이 주제에 대해 수많은 익힐것들이 있습니다. 만약 좀더 폰트와 이미지들에 대해 깊게 공부하고 싶다면,

- [이미지 최적화 문서](https://nextjs.org/docs/app/building-your-application/optimizing/images)
- [폰트 최적화 문서](https://nextjs.org/docs/app/building-your-application/optimizing/fonts)
- [이미지 사용과 웹 성능 향상(MDN)](https://developer.mozilla.org/en-US/docs/Learn/Performance/Multimedia)
- [웹 폰트(MDN)](https://developer.mozilla.org/en-US/docs/Learn/CSS/Styling_text/Web_fonts)




<div class="finish">
  <p class="finish__title">챕터 3을 완료했습니다.</p>
  <p>Next.js에서 이미지와 폰트를 최적화 하는 방법을 배웠습니다.</p>
  <div class="next-box">
    <p class="next">다음</p>    
    <p class="next__title">4: 레이아웃과 페이지 생성</p>
    <p>대시보드 라우트와, 중첩된 레이아웃과 페이지를 만들어 봅시다.</p>
    <a id="next__btn" href="https://nextjs.org/learn/dashboard-app/creating-layouts-and-pages">챕터 4 시작하기
    <svg data-testid="geist-icon" height="16" stroke-linejoin="round" viewBox="0 0 16 16" width="16" style="color: currentcolor;"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.53033 2.21968L9 1.68935L7.93934 2.75001L8.46967 3.28034L12.4393 7.25001H1.75H1V8.75001H1.75H12.4393L8.46967 12.7197L7.93934 13.25L9 14.3107L9.53033 13.7803L14.6036 8.70711C14.9941 8.31659 14.9941 7.68342 14.6036 7.2929L9.53033 2.21968Z" fill="currentColor"></path></svg>
    </a>
  </div>
</div>

## Ref
- [https://nextjs.org/learn/dashboard-app/optimizing-fonts-images]


<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/next-js-tutorial/css.css">
<script type="text/javascript" src="https://eso0117.github.io/web-practice/public/next-js-tutorial/js.js"></script>
