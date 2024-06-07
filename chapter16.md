## 들어가기 전에

이 글은 [Next.js - Chapter 16 : Adding Metadata
](https://nextjs.org/learn/dashboard-app/adding-metadata) 강의를 번역한 글입니다.

# 메타데이터 추가하기

메타데이터는 SEO와 공유에 있어서 중요한 역할을 합니다. 이번 챕터에서, Next.js에서 메타데이터를 추가하는 방법에 관해 이야기해 볼 겁니다.

## 이번 챕터에서...

여기 우리가 다룰 주제들이 있습니다.
- 메타데이터는 무엇인지
- 메타데이터 타입들
- 메타데이터를 통해 Open Graph 이미지를 추가하는 방법
- 메타데이터로 파비콘 이미지를 추가하는 방법

## 메타데이터는 무엇인가요?

웹 개발에서, 메타데이터는 웹페이지에 대해 추가적인 상세정보를 제공합니다. 메타데이터는 페이지를 방문하는 유저에게 보이지 않습니다. 대신 화면 뒤에서 페이지의 HTML내에 내장되어(보통 `<head>`요소) 작동합니다. 이 숨겨진 정보는 웹페이지 컨텐츠를 이해하는게 필요한 검색엔진과 다른 시스템들에게 중요합니다.

## 왜 메타데이터는 중요한가요?

메타데이터는 검색엔진과 소셜미디어 플랫폼에서 해당 페이지를 더 접근 가능하게 만들고, 이해하게 만들며 웹페이지 SEO를 향상시키는데 중요한 역할을 합니다. 적절한 메타데이터는 검색결과의 랭킹을 향상시키면서 검색엔진이 효과적으로 웹 페이지를 인덱싱 하는데 도움을 줍니다. 추가적으로, Open Graph같은 메타데이터는 유저들에게 더 유용해보이고 매력적이게 링크를 만들어주어 소셜 미디어에서 공유되는 링크를 더 좋게 만듭니다.

## 메타데이터 타입들

각각 고유한 목적을 가진 다양한 메타데이터 타입들이 있습니다. 몇몇 공통된 타입들은

**제목 메타데이터**: 브라우저의 탭에서 보이는 웹페이지의 타이틀을 담당합니다. 검색엔진이 페이지가 어떤것인지 이해하는데 도움을 주고 SEO에서 매우 중요합니다.

```
<title>Page Title</title>
```

**설명 메타데이터**: 웹페이지의 간단한 요약을 제공하는 메타데이터 입니다. 종종 검색엔진 결과에 노출됩니다.

```
<meta name="description" content="A brief description of the page content." />
```

**키워드 메타데이터**: 이 메타데이터는 웹페이지 컨텐츠와 관련된 키워드를 포함하면서, 검색엔진이 페이지를 인덱싱 하는것을 도와줍니다.


**Open Graph 메타데이터**: 소셜미디어 플랫폼에서 페이지가 공유 될 때, 타이틀, 설명, 미리보기 이미지 등을 제공하면서 웹페이지가 나타나는 형식을 향상시킵니다.

```
<meta property="og:title" content="Title Here" />
<meta property="og:description" content="Description Here" />
<meta property="og:image" content="image_url_here" />
```

**파비콘 메타데이터**: 파비콘(작은 아이콘)을 웹페이지와 연결하여 브라우저 검색바나, 탭에서 나타나게 합니다.

```
<link rel="icon" href="path/to/favicon.ico" />
```

## 메타데이터 추가하기

Next.js는 어플리케이션의 메타데이터를 정의하는데 사용되는 메타데이터 API를 갖고 있습니다.
어플리케이션에 메타데이터를 추가하는데는 2가지 방법이 있습니다.

- **설정 기반**: [정적 `메타데이터` 객체](https://nextjs.org/docs/app/api-reference/functions/generate-metadata#metadata-object)를 내보내거나(export) `layout.js`나 `page.js`파일 내에 동적 [`generateMetadat` 함수](https://nextjs.org/docs/app/api-reference/functions/generate-metadata#generatemetadata-function) 를 추가합니다.

- **파일 기반**: Next.js는 메타데이터를 목적으로 사용되는 다양한 특별한 파일들이 있습니다.
  - `favicon.ico`, `apple-icon.jpg`, `icon.jpg`: 아이콘과 파비콘에 활용됩니다.
  - `opengraph-image.jpg`, `twitter-image.jpg`: 소셜미디어 이미지를 위해 사용합니다.
  - `robots.txt`: 검색엔진 크롤링을 위한 설명을 제공합니다.
  - `sitemap.xml`: 웹사이트 구조에 대한 정보를 제공합니다.

정적 메타데이터를 위해 이런 파일들을 사용하거나, 프로젝트 내에서 프로그래밍으로 생성할 수도 있습니다.

이 두가지 옵션을 기반으로, Next.js는 자동으로 관련된 `<head>` 요소를 페이지 내에 생성합니다.

### 파비콘과 Open Graph 이미지

우리 `/public` 폴더 내에, `favicon.ico`와 `opengraph-image.jpg` 두 이미지가 있다는걸 확인할 수 있습니다.

이 이미지들을 `/app` 폴더의 루트 경로로 옮기세요.

이렇게 옮긴 후에, Next.js는 자동으로 파비콘과 Open Graph 이미지로서 해당 파일을 식별하고 사용합니다. 개발툴에서 어플리케이션의 `<head>`안에서 확인해볼 수 있습니다.

<div class="hint">

**알면 좋은 것:** [`ImageResponse`](https://nextjs.org/docs/app/api-reference/functions/image-response)를 이용해 동적 Open Graph 이미지를 생성할 수 있습니다.

</div>

### 페이지 제목과 설명

또한 어느 `layout.js`나 `page.js`에서도[`metadata` 객체](https://nextjs.org/docs/app/api-reference/functions/generate-metadata#metadata-fields)를 넣어서 제목과 설명같은 페이지의 추가적인 정보를 나타낼 수 있습니다. `layout.js` 안에 있는 어떠한 메타데이터도 그것을 사용하는 모든 페이지에 상속됩니다.

루트 레이아웃에서, 다음의 값을 가진 `metadata`를 생성하세요.

<div class="code-with-file">

**/app/layout.tsx**
```
import { Metadata } from 'next';
 
export const metadata: Metadata = {
  title: 'Acme Dashboard',
  description: 'The official Next.js Course Dashboard, built with App Router.',
  metadataBase: new URL('https://next-learn-dashboard.vercel.sh'),
};
 
export default function RootLayout() {
  // ...
}
```
</div>

Next.js는 자동적으로 제목과 설명 그리고 메타데이터를 어플리케이션에 추가합니다.

그러나 만약 특정한 페이지에 다른 제목을 넣고 싶다면 어떻게 해야할까요? 그것은 해당 페이지에 `metadata` 객체를 추가하면 됩니다. 내제된 페이지의 메타데이터는 부모의 메타데이터를 덮어씁니다.

예를들어, ` /dashboard/invoices` 페이지 내에 페이지 제목을 다음과 같이 업데이트 할 수 있습니다.

<div class="code-with-file">

**/app/dashboard/invoices/page.tsx**
```
import { Metadata } from 'next';
 
export const metadata: Metadata = {
  title: 'Invoices | Acme Dashboard',
};
```
</div>

이것은 동작합니다만, 모든 페이지에서 어플리케이션의 제목을 반복해서 쓰고 있습니다. 만약 회사 명 같은것이 바뀐다면 모든 페이지를 업데이트 해야할 겁니다.

대신, `metadata` 객체에서 `title.template` 필드를 사용해 페이지 제목들의 템플릿을 정할 수 있습니다. 해당 템플릿은 페이지 제목 그리고 포함하고 싶은 다른 정보를 포함할 수 있습니다.

루트 레이아웃에서, `metadata` 객체를 업데이트하여 템플릿을 포함하세요.

<div class="code-with-file">

**/app/layout.tsx**
```
import { Metadata } from 'next';
 
export const metadata: Metadata = {
  title: {
    template: '%s | Acme Dashboard',
    default: 'Acme Dashboard',
  },
  description: 'The official Next.js Learn Dashboard built with App Router.',
  metadataBase: new URL('https://next-learn-dashboard.vercel.sh'),
};
```
</div>

템플릿 내에 `%s`는 특정한 페이지 제목으로 대체됩니다.

이제 `/dashboard/invoices` 페이지에서, 페이지 제목을 추가할 수 있습니다.

<div class="code-with-file">

**/app/dashboard/invoices/page.tsx**
```
export const metadata: Metadata = {
  title: 'Invoices',
};
```
</div>

`/dashboard/invoices` 페이지로 이동하여, `<head>` 요소를 확인해보세요. 페이지 제목이 이제 `Invoices | Acme Dashboard`로 나타나는걸 볼 수 있습니다.

## 실습해보기: 메타데이터 추가하기

이제 메타데이터에 대해 배웠으니, 다른페이지에 제목을 추가하는걸 해봅시다.

1. `/login` 페이지
2. `/dashboard/` 페이지
3. `/dashboard/customers` 페이지
4. `/dashboard/invoices/create` 페이지
5. `/dashboard/invoices/[id]/edit` 페이지

Next.js 메타데이터 API는 어플리케이션 메타데이터의 모든 제어를 제공하면서 강력하고 유연합니다. 우리는 어떻게 기본적인 메타데이터를 추가하는지 보여주었습니다 그러나 원하면 `keywords`, `robots`, `canonical` 같은  더 많은 필드를 추가할 수 있습니다. [이 문서](https://nextjs.org/docs/app/api-reference/functions/generate-metadata)를 보면서 자유롭게 탐구하고 어플리케이션에 추가하고 싶은 메타데이터를 추가하세요.

<div class="finish">
  <p class="finish__title">챕터 16을 완료했습니다.</p>
  <p>축하합니다! 어플리케이션에 메타데이터를 추가하고 메타데이터 API에 대해 배웠습니다.</p>
  <div class="next-box">
    <p class="next">다음</p>    
    <p class="next__title">17. 다음 과정</p>
    <p>Next.js에 대해 계속 알아보세요.</p>
    <a id="next__btn" href="https://nextjs.org/learn/dashboard-app/next-steps">챕터 17 시작하기
    <svg data-testid="geist-icon" height="16" stroke-linejoin="round" viewBox="0 0 16 16" width="16" style="color: currentcolor;"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.53033 2.21968L9 1.68935L7.93934 2.75001L8.46967 3.28034L12.4393 7.25001H1.75H1V8.75001H1.75H12.4393L8.46967 12.7197L7.93934 13.25L9 14.3107L9.53033 13.7803L14.6036 8.70711C14.9941 8.31659 14.9941 7.68342 14.6036 7.2929L9.53033 2.21968Z" fill="currentColor"></path></svg>
    </a>
  </div>
</div>

## Ref
- <https://nextjs.org/learn/dashboard-app/adding-metadata>


<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/next-js-tutorial/css.css">
<script type="text/javascript" src="https://eso0117.github.io/web-practice/public/next-js-tutorial/js.js"></script>
