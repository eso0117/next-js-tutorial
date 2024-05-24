## 들어가기 전에

이 글은 [Next.js - Chapter 6 : Setting Up Your Database](https://nextjs.org/learn/dashboard-app/setting-up-your-database) 강의를 번역한 글입니다.

# 데이터 베이스 세팅하기

대시보드에서 작업을 계속 진행하기 전에 우리는 데이터가 필요합니다. 이번 챕터에서 `@vercel/postgres`를 사용해 `PostgreSQL` 데이터베이스를 세팅할겁니다. 만약 이미 PostgreSQL에 익숙하고 자신의 것을 사용하고 싶다면 이 챕터를 스킵하고 자신의 것으로 세팅해도 좋습니다. 그렇지 않다면 따라오세요!

## 이번 챕터에서...

여기 우리가 다룰 주제들이 있습니다.
- 깃허브에 프로젝트 올리기
- 미리보기와 배포를 할 수 있는 Vercel 계정을 생성하고 깃허브 연동하기
- 프로젝트와 Postgres 데이터베이스 연동하기
- 초기값으로 데이터베이스에 데이터 넣기

## 깃허브 저장소 생성하기

만약 아직 깃허브에 푸시를 안 했다면 푸시를 해봅시다. 그러면 데이터베이스 셋업과 배포를 더 쉽게 할 수 있습니다.
만약 저장소 세팅하는데 도움이 필요하다면, [이 깃허브 가이드](https://help.github.com/en/github/getting-started-with-github/create-a-repo)를 보세요.

<div class="hint">

**알면 좋은 것:**
- GitLab이나 Bitbucket같은 다른 깃 호스팅 서비스를 이용해도 됩니다.
- 만약 깃허브가 처음이라면, 배포과정을 간단하게 할 수 있는 [Github 데스크탑 앱](https://desktop.github.com/)을 추천합니다.

</div>

## Vercel 계정 만들기

[vercel.com/signup](https://vercel.com/signup)에 방문하여 계정을 만듭시다. 무료 "hobby" 플랜을 선택하세요. 깃허브 계정과 Vercel 계정 연동을 위해 **Continue with GitHub**을 선택하세요.

## 프로젝트 연동하고 배포하기

다음으로, 방금 막 생성된 깃허브 저장소를 선택하고 불러올(import) 수 있는 이 화면으로 이동되었을겁니다. 방금 생성된 저장소를 불러오세요.
<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fimport-git-repo.png&w=1920&q=75" alt="vercel 깃허브 저장소 연동중">

프로젝트의 이름을 짓고 **Deploy**를 누르세요.
<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fconfigure-project.png&w=1920&q=75" alt="Vercel 배포">

축하합니다! 🎉 이제 프로젝트가 배포되었습니다.
<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fdeployed-project.png&w=1920&q=75" alt="Nextjs 튜토리얼 vercel 배포 후 화면">

깃허브 저장소와 연결함으로서, **메인** 브랜치에 푸시를 할 때마다, Vercel에서 어떠한 설정 없이도 자동적으로 배포를 할겁니다. 풀리퀘스트가 생성되면, 배포 에러를 초기에 찾을 수 있게 해주고 프로젝트 미리보기를 통해 팀원들과 공유할 수 있는 [즉시 미리보기](https://vercel.com/docs/deployments/preview-deployments#preview-urls) 기능도 있습니다.

## Postgres database 생성하기

다음으로, 데이터 베이스를 셋업하기 위해 **Continue to Dashboard**를 누르세요 그리고 프로젝트 대시보드에서 **Storage** 탭을 선택하세요. **Connect Store → Create New → Postgres → Continue** 를 선택하세요.
<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fcreate-database.png&w=1920&q=75" alt="PostgresSQL DATABASE 생성하기">

(** 여기서 실제로 나는 Storage tab에서 바로 **Postgres create -> Accept** 버튼이 있어 누를 수 있었다. 해당 튜토리얼때랑 지금이랑 좀 UI가 다른듯.)

약관에 동의하고, 데이터베이스의 이름을 설정하고, 데이터베이스의 지역이 **Washington D.C (iad1)** 로 되어있는지 확인합시다. - 이것은 모든 새로운 Vercel 프로젝트를 위한 [기본설정 지역](https://vercel.com/docs/functions/serverless-functions/regions#select-a-default-serverless-region)입니다. 데이터베이스 위치를 어플리케이션 코드와 같거나 가까운 지역으로 설정함으로서, 데이터 요청의 [지연](https://developer.mozilla.org/en-US/docs/Web/Performance/Understanding_latency)을 줄일 수 있습니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fdatabase-region.png&w=1920&q=75" alt="Postgres Database 생성하기">

<div class="hint">

**알면 좋은 것:**
- 데이터베이스가 한번 초기설정이 되면 지역을 바꿀 수 없습니다. 만약 다른 지역을 원한다면, 데이터베이스가 생성되기 전에 설정해야합니다.

</div>

이제 연결이 되었으면, `.env.local`탭으로 이동해봅시다. **Show secret**와 **Copy Snippet**을 눌러 해당 값들을 복사하세요. 복사되기 전 secrets들이 나타나야하는걸 명심하세요.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fdatabase-dashboard.png&w=1920&q=75" alt="sercet 값들을 복사하기 직전">

코드 에디터에서 `.env.example`파일 이름을 `.env`로 바꾸세요. 그리고 Vercel에서 복사된 값들을 붙여넣습니다.

**Important:** 푸시할때 데이터베이스의 설정값들이 노출되는걸 막기위해 `.gitignore`파일에 `.env` 파일이 있는지 확인하세요.

마지막으로 [Vercel Postgres SDK](https://vercel.com/docs/storage/vercel-postgres/sdk)를 설치하기 위해 터미널에서 `npm i @vercel/postgres`명령어를 실행하세요.

## 초기 데이터 넣기(Seed)

이제 우리의 데이터베이스가 생성되었습니다. 대시보드 작업을 할때 필요한 약간의 초기 데이터를 그 안에 넣어봅시다(seed).

프로젝트의 `/scripts` 폴더 안에 `seed.js` 라는 파일이 있습니다.
이 스크립트로 `invoices`, `customers`, `user`, `revenue` 테이블을 생성하고 데이터를 넣을(seed) 수 있습니다.

코드가 하고 있는것을 전부 이해하지 못하더라도 걱정하지마세요, 그러나 전체적인 맥락으로서, 이 스크립트는 테이블 생성을 위해 SQL을 사용합니다. 그리고 `placeholder-data.js` 파일에 있는 데이터들이 테이블 생성 후에 초기 데이터로 입력될겁니다.

<div class="code-with-file">
/package.json


```
"scripts": {
  "build": "next build",
  "dev": "next dev",
  "start": "next start",
  "seed": "node -r dotenv/config ./scripts/seed.js"
},
```
</div>

이 명령어는 `seed.js`를 실행합니다.

이제 `npm run seed` 명령어를 실행해보세요. 그러면 터미널에서 스크립트가 실행하고있는 `console.log` 메시지들을 볼 수 있습니다.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">데이터베이스의 맥락에서 'seeding'이란 무엇인가요?</p>
    <div class="option-list">
      <div class="option" data-question-number="01">
        <span class="option__number">A</span>
        <span class="option__desc">데이터베이스 내 모든 데이터 삭제</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">B</span>
        <span class="option__desc">데이터베이스의 스키마 불러오기</span>
      </div>
      <div class="option" data-question-number="01" data-answer="true">
        <span class="option__number">C</span>
        <span class="option__desc">초기데이터 셋으로 데이터베이스에 데이터 넣기</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">D</span>
        <span class="option__desc">테이블간의 관계 생성하기</span>
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
<br>

<div class="hint">

**문제해결**
- `.env`파일에 데이터를 복사하기 전, 데이터베이스 비밀값들을 드러내게 하는걸 잊지마세요.
- 이 스크립트는 유저의 비밀번호를 해시하기 위해 `bcrypt`를 사용합니다. 만약 `bycrypt`가 환경에 맞지 않는다면, [`bcryptjs`](https://www.npmjs.com/package/bcryptjs)를 대신 사용하도록 업데이트 할 수 있습니다.
- 만약 데이터베이스에 초기데이터를 넣는 동안 이슈가 있어서 다시 스크립트를 돌리는 경우, 현재 테이블을 데이터베이스 쿼리 인터페이스에서 `DROP TABLE tablename`이란 명령어를 통해 드랍할 수 있습니다. 아래 [쿼리 실행하기](https://nextjs.org/learn/dashboard-app/setting-up-your-database#executing-queries)에서 더 세부사항을 확인하세요. 하지만 주의하세요. 이 명령어는 테이블과 그 모든 데이터를 지웁니다.
우리는 지금 플레이스홀더 데이터로 작업을 하므로 예제 앱에서 하는것은 괜찮습니다. 그러나 이 명령어는 실제 운영하고 있는 프로덕션 어플리케이션에서 하면 안됩니다.
- 만약 Vercel Postgres 데이터베이스에 데이터를 넣는데 어려움이 있다면, [깃허브](https://github.com/vercel/next-learn/issues)에 이슈를 올려주세요.

</div>

## 데이터베이스 탐구하기

이제 우리 데이터베이스가 어떠한지 살펴봅시다. Vercel로 돌아가 사이드바에 있는 `Data`를 클릭하세요.

여기에서, users, customers, invoices, 그리고 revenue 4개의 새로운 테이블을 볼겁니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fdatabase-tables.png&w=2048&q=75" alt="Vercel 데이터베이스에 나오는 테이블들">

각각의 테이블을 선택해서, 안의 데이터와 내용이 `placeholder-data.js` 파일과 일치하는지 확인할 수 있습니다.

## 쿼리 실행하기

쿼리탭으로 바꿔서 데이터베이스에게 질의할 수 있습니다. 이곳에서는 표준 SQL커맨드를 지원합니다. 예를들어, `DROP TABLE customers`을 입력하면 "customers" 테이블과 그 안에 모든 데이터를 지울 수 있습니다. - ***그러니 조심하세요!***

첫번째 데이터베이스 질의를 실행해봅시다. 다음의 SQL코드를 복사하고 Vercel 인터페이스에 붙여넣으세요

```
SELECT invoices.amount, customers.name
FROM invoices
JOIN customers ON invoices.customer_id = customers.id
WHERE invoices.amount = 666;
```

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">위의 질의에서 나온 invoice는 어떤 customer것인가요?</p>
    <div class="option-list">
      <div class="option" data-question-number="02">
        <span class="option__number">A</span>
        <span class="option__desc">Lee Robinson</span>
      </div>
      <div class="option" data-question-number="02" data-answer="true">
        <span class="option__number">B</span>
        <span class="option__desc">Evil Rabbit</span>
      </div>
      <div class="option" data-question-number="02">
        <span class="option__number">C</span>
        <span class="option__desc">Delba de Oliveira</span>
      </div>
      <div class="option" data-question-number="02">
        <span class="option__number">D</span>
        <span class="option__desc">Steph Dietz</span>
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



<div class="finish">
  <p class="finish__title">챕터 6을 완료했습니다.</p>
  <p>이제 데이터베이스가 세팅되었습니다, 우리 어플리케이션에서 다시 작업해봅시다.</p>
  <div class="next-box">
    <p class="next">다음</p>    
    <p class="next__title">7: 데이터 가져오기</p>
    <p>API, SQL, 그리고 다른 방법 등 데이터베이스로부터 데이터를 가져올 수 있는 방법에 대해 이야기해봅시다.</p>
    <a id="next__btn" href="https://nextjs.org/learn/dashboard-app/fetching-data">챕터 7 시작하기
    <svg data-testid="geist-icon" height="16" stroke-linejoin="round" viewBox="0 0 16 16" width="16" style="color: currentcolor;"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.53033 2.21968L9 1.68935L7.93934 2.75001L8.46967 3.28034L12.4393 7.25001H1.75H1V8.75001H1.75H12.4393L8.46967 12.7197L7.93934 13.25L9 14.3107L9.53033 13.7803L14.6036 8.70711C14.9941 8.31659 14.9941 7.68342 14.6036 7.2929L9.53033 2.21968Z" fill="currentColor"></path></svg>
    </a>
  </div>
</div>

## Ref
- [https://nextjs.org/learn/dashboard-app/setting-up-your-database](https://nextjs.org/learn/dashboard-app/setting-up-your-database)


<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/next-js-tutorial/css.css">
<script type="text/javascript" src="https://eso0117.github.io/web-practice/public/next-js-tutorial/js.js"></script>
