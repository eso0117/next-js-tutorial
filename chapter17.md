## 들어가기 전에

이 글은 [Next.js - Chapter 17 : Next Steps
](https://nextjs.org/learn/dashboard-app/next-steps) 강의를 번역한 글입니다.

# 다음 과정 그리고 번역자의 말

축하합니다! Next.js의 주요 기능과 좋은 방법들을 배울 수 있었던 Next.js의 대시보드 코스를 끝마쳤습니다.

하지만 이건 겨우 시작입니다. 작은 사이드 프로젝트를 만드는 것부터, 스타트업 아이디어의 구현, 그리고 팀과 함께 만들 커다란 규모의 어플리케이션까지 Next.js는 이러한 구현을 위한 수많은 기능을 가지고 있습니다.

여기 Next.js에 대해 더 알아볼 문서들이 있습니다.

- [Next.js 문서](https://nextjs.org/docs)
- [Next.js 탬플릿](https://vercel.com/templates?framework=next.js)
  - [어드민 대시보드 탬플릿](https://vercel.com/templates/next.js/admin-dashboard-tailwind-planetscale-react-nextjs)
  - [Next.js 커머스](https://vercel.com/templates/next.js/nextjs-commerce)
  - [블로그 스타터 도구](https://vercel.com/templates/next.js/blog-starter-kit)
  - [Nextra: 문서 생성 도구](https://vercel.com/templates/next.js/blog-starter-kit)
  - [이미지 갤러리 스타터](https://vercel.com/templates/next.js/image-gallery-starter)
- [Next.js 깃 저장소](https://github.com/vercel/next.js)
- [Vercel 유튜브](https://www.youtube.com/@VercelHQ/videos)
  - [Next.js 앱 라우터: 라우팅, 데이터 가져오기, 캐싱](https://www.youtube.com/watch?v=gSSsZReIFRk)
  - [React와 Next.js 퍼포먼스(Lydia Hallie)](https://www.youtube.com/watch?v=SqVLqvsiAYQ)
  - [Vercel 프로덕트 알아보기](https://www.youtube.com/watch?v=sPmat30SE4k)

## 당신의 Next.js 앱을 공유하세요

우리는 당신이 이 튜토리얼에서 만든 앱을 X에 공유했으면 합니다. 만약 그렇게 한다면 [@nextjs](https://twitter.com/nextjs)으로 우리에게 알려주세요. 그러면 우리가 볼 수 있습니다. 또 이 강의에 대한 피드백도 받았으면 합니다.

당신에게 이 강의가 유익했길 희망하며, 계속해서 성장하며 만들어나가길 바랍니다.

<div class="hint">

**번역자의말**:
이 부분부터는 번역이 아니라 제가 추가한 부분입니다.
</div>

## 번역자의 튜토리얼 정리

1. **Next.js 셋업과 프로젝트 설치**
  - node.js 설치([Intro](https://thewys.tistory.com/entry/NextJS-튜토리얼-Nextjs-배우기))
  - 프로젝트 생성([Chapter1](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-1-시작하기))
2. **전체적인 페이지 생성과 UI 구성**
  - 라우팅 생성([Chapter4](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-4-레이아웃과-페이지-생성))
  - 페이지 Layout 생성([Chapter4](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-4-레이아웃과-페이지-생성))
  - 스타일링([Chapter2](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-2-CSS-스타일하기))
  - 커스텀 폰트 넣기([Chapter3](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-3-폰트와-이미지-최적화))
  - 페이지 이동([Chapter5](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-5-페이지간-이동))
3. **백엔드 요청**
  - 데이터 가져오기([Chapter7](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-7-데이터-가져오기), [Chapter9](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-9-스트리밍))
  - 데이터 수정하기([Chapter12](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-12-데이터-변형Mutating))
4. **추가기능 구현**
  - 로그인 로그아웃([Chapter15](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-15-인증-추가하기))
  - 메타데이터 추가([Chapter16](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-16-메타데이터-추가하기))
  - 접근성([Chaper14](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-14-접근성-향상))
  - 404나 일반적인 에러 처리([Chapter13](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-13-에러-핸들링))
  - 디바운싱([Chapter11](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-11-검색과-페이지네이션-추가하기))
  - 로딩 스켈레톤 UI([Chapter9](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-9-스트리밍))
5. **배포하기**
  - Vercel DB 세팅([Chapter 6](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-6-데이터-베이스-세팅하기))
  - Vercel 사용 배포([chapter 6](https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-6-데이터-베이스-세팅하기))
  

## 번역자의 말

전체적인 웹 어플리케이션 레이아웃, 페이지 UI 구성부터 인증과 배포까지 해보는 웹 개발 전체를 맛볼 수 있는 좋은 튜토리얼인것 같습니다. 제가 처음 Next.js 공부할때는 무슨 블로그 만들기 튜토리얼이었던것 같은데 바뀐것 같네요.

잘들 보셨나요? 번역 이상한 부분 많죠? 오타도 많고 하하
틈틈이 지난 글들을 다시보며 왜이렇게 발번역이야.., 무슨말이야..?, 오타있네! 하면서
다시 고치고있습니다
솔직히 몇명이나 볼까? 란 의심이 마음 속에 있습니다. 요즘 번역이 너무 잘 되어 있으니까 그냥 Next.js에서 번역기 돌려 보지 않을까..ㅠ..

그래도 한 분이라도 도움이 되셨다면 좋겠습니다!
Next.js 튜토리얼 여기까지 와주시다니 너무 감사합니다!

<div class="finish">
  <p class="finish__title">Next.js 튜토리얼을 완료했습니다.</p>
  <p>마지막 챕터를 끝냈습니다. 이제 다음 단계로 갈 준비가 되었습니다.</p>
</div>

## Ref
- <https://nextjs.org/learn/dashboard-app/adding-metadata>


<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/md-css.css">
<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/next-js-tutorial/css.css">
<script type="text/javascript" src="https://eso0117.github.io/web-practice/public/next-js-tutorial/js.js"></script>
