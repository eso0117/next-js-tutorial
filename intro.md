## 들어가기 전에
이 글은 [Next.js 튜토리얼 - Next.js 배우기](https://nextjs.org/learn/dashboard-app)를 번역한 글입니다.

# Next.js 배우기
Next.js App Router 튜토리얼에 오신것을 환영합니다! 이 자유롭게 상호작용하는 강의에서, 풀스택 웹 어플리케이션을 만들면서 Next.js의 핵심 기능들을 배울겁니다.

## 우리가 만들것
<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fdashboard.png&w=3840&q=75" alt="Nextjs 튜토리얼 결과물">

이 코스에서, 우리는 다음과 같은 기능을 가진 간단한 버전의 재무관리 대시보드를 만들겁니다.

- 홈페이지
- 로그인 페이지
- 인증에 따라 보호되는 대시보드
- 유저를 위한 송장(invoice)을 추가하고 편집하고 삭제하는 기능

대시보드는 데이터베이스 또한 가질 것이며, [추후 챕터](https://nextjs.org/learn/dashboard-app/setting-up-your-database)에서 셋업을 진행합니다.

이 튜토리얼의 끝에서 풀스택 Next.js 어플리케이션을 개발할 핵심 기능을 배울겁니다.

## 개요
- 스타일하기: Next.js내에 앱을 스타일 할 수 있는 다양한 방법
- 최적화: 이미지, 링크, 폰트 등의 최적화하는 방법
- 라우팅: 파일 시스템 라우팅을 이용하여 중첩된 레이아웃과 페이지를 만드는 방법.
- 데이터 패치: Vercel을 통한 데이터베이스 셋업과, 데이터를 가져오고 스트리밍 할 최고의 방법.
- 검색과 페이지네이션: URL 파라미터를 이용한 검색과 페이지네이션 방법
- 데이터 변경(mutating): React Server Action을 통해 데이터를 변경하는 방법과 Next.js 캐시를 재검증하는 방법
- Error Handling: 일반적인(general) 에러, 404 not found 에러를 처리하는 방법
- 폼(Form) 유효성 검사와 접근성: 서버사이드 폼 유효성 검사와 접근성 향상을 위한 팁들
- 인증: [NextAuth.js](https://next-auth.js.org/)와 미들웨어를 통해 앱에 인증기능을 추가하는 방법
- 메타데이터: 메타데이터를 추가하고, 앱을 소셜미디어에 공유하는 방법

## 선행 지식
이 튜토리얼은 당신이 리액트와 자바스크립트에 대한 기본적 지식이 있다고 가정합니다. 만약 리액트가 처음이라면, [리액트 기초](https://nextjs.org/learn/react-foundations)코스를 통해 먼저 컴퍼넌트, props, state, hook, 그리고 더 새로운 Server Component들과 Suspense 같은 리액트 기본을 배우길 추천합니다.

## 시스템 요구사항
이 코스를 시작하기전에, 시스템이 아래 요구를 충족하는지 확인하세요
- Node.js 18.17.0 이거나 이후 버전. [여기서 받으세요](https://nodejs.org/en)
- 운영체제: mac OS, 윈도우 (WSL 포함), 또는 리눅스.

추가로, [깃헙 계정](https://github.com/join/)과 [Vercel 계정](https://vercel.com/signup)이 필요합니다.

## 함께 대화해요
만약 이 튜토리얼에서 질문이 있거나 피드백을 제공하고싶다면, [디스코드](https://discord.com/invite/Q3AsD4efFC)나 [깃헙](https://github.com/vercel/next-learn) 같은 우리의 커뮤니티에서 질문할 수 있습니다.

<div class="finish">
  <p class="finish__title">시작할 준비 되었나요?</p>
  <p>지금까진 튜토리얼의 도입부였습니다. 더 들어가봅시다.</p>
  <div class="next-box">
    <p class="next">다음</p>    
    <p class="next__title">1: 시작하기</p>
    <p>Next.js 어플리케이션을 생성하는 방법과 로컬 개발 서버를 시작하는 방법을 배웁니다.</p>
    <a id="next__btn" href="https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-1-시작하기">챕터 1 시작하기
<svg data-testid="geist-icon" height="16" stroke-linejoin="round" viewBox="0 0 16 16" width="16" style="color: currentcolor;"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.53033 2.21968L9 1.68935L7.93934 2.75001L8.46967 3.28034L12.4393 7.25001H1.75H1V8.75001H1.75H12.4393L8.46967 12.7197L7.93934 13.25L9 14.3107L9.53033 13.7803L14.6036 8.70711C14.9941 8.31659 14.9941 7.68342 14.6036 7.2929L9.53033 2.21968Z" fill="currentColor"></path></svg>
</a>
  </div>
</div>

## Ref
- [https://nextjs.org/learn/dashboard-app](https://nextjs.org/learn/dashboard-app)

<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/next-js-tutorial/css.css">
<script type="text/javascript" src="https://eso0117.github.io/web-practice/public/next-js-tutorial/js.js"></script>
