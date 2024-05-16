## 들어가기 전에
이 글은 [Next.js 튜토리얼](https://nextjs.org/learn/dashboard-app/getting-started)을 번역한 글입니다.

# 시작하기

## 신규 프로젝트 생성하기
Next js app을 생성하기위해, cd명령어로 terminal을 열고 프로젝트를 생성하고 싶은곳으로 이동하세요. 그리고 다음 명령어를 실행하세요.

```
npx create-next-app@latest nextjs-dashboard --use-npm --example "https://github.com/vercel/next-learn/tree/main/dashboard/starter-example"
```

위 명령어는 Next.js 어플리케이션을 셋업하는 CLI툴의 명령어 create-next-app를 사용합니다. `--example` flag를 사용하는 위의 커맨드로 강의의 [starter example](https://github.com/vercel/next-learn/tree/main/dashboard/starter-example)를 바로 받을 수 있습니다.

## 프로젝트 살펴보기
아예 처음부터 시작하는 다른 튜토리얼들과 달리, 이 강의에서 대부분의 코드는 이미 쓰여있습니다. 이게 코드베이스가 존재하는 상태에서 일을 하기 쉬운 할 당신의 실제 개발세계를 더 잘 반영할겁니다.

우리 목표는 불필요한 코드를 *모두* 쓸 필요 없이 당신이 Next.js의 핵심 기능들을 익히는데 포커싱을 맞추도록 도와주는 것입니다.

설치 후, 코드에디터로 프로젝트를 열고 nextjs-dashboard로 이동해주세요.

```
cd nextjs-dashboard
```
이제 프로젝트를 살펴봅시다.

### 폴더 구조
당신은 프로젝트로 아래와 같은 구조를 따른다는걸 알게 될겁니다.
<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Flearn-folder-structure.png&w=1920&q=75">

- `/app`: 우리가 작업을 해야할 공간으로서 어플리케이션에서 사용되는 모든 라우트, 컴퍼넌트, 로직이 있습니다.
- `/app/lib`: 공통 함수나 데이터 가져오는 함수들같이 앱에서 사용되면서 재사용 될 수 있는 함수들이 담겨 있습니다.
- `/app/ui`: 카드형태, 테이블, 폼 같은  모든 UI컴퍼넌트가 담겨 있습니다. 시간을 아끼기 위해 우리는 미리 이런 컴퍼넌트들을 만들어 놨습니다.
- `/public/`: 이미지 같은 모든 정적파일들이 담겨있습니다.
- `/scripts`: 이후 챕터에서 데이터베이스를 연동할때 데이터를 넣을 스크립트들이 있습니다.
- **Config Files**: 어플리케이션 루트 폴더의 `next.config.js` 파일같은 설정 파일을 눈치 챘을겁니다. 대부분의 이러한 파일들은 당신이 `create-next-app`으로 새로운 프로젝트를 시작할때 생성되고, 미리 설정됩니다. 이번 강의에서 이 파일들을 바꿀 필요는 없습니다.

폴더 내부를 자유롭게 탑색하구, 아직 코드의 전부를 이해하지 못했다고 걱정하지 마세요.

### 플레이스홀더 데이터
UI를 만들때, 플레이스홀더 데이터를 갖는게 도움이 됩니다. 만약 데이터베이스나 API를 아직 사용할 수 없다면, 아래 처럼 할 수 있습니다.
- JSON형태나 자바스크립트 객체같은 플레이스홀더 데이터 이용하기
- [mockAPI](https://mockapi.io/)같은 3rd party 서비스 사용하기

이 프로젝트에서는, 우리는 이미 데이터를 `app/lib/placeholder-data.js`에 제공하고 있습니다. 각 자바스크립트 오브젝트는 당신의 데이터베이스의 테이블을 나타냅니다. 예를들어, invoice 테이블 같은경우:

```
const invoices = [
  {
    customer_id: customers[0].id,
    amount: 15795,
    status: 'pending',
    date: '2022-12-06',
  },
  {
    customer_id: customers[1].id,
    amount: 20348,
    status: 'pending',
    date: '2022-11-14',
  },
  // ...
];

```

[데이터 베이스 세팅하기](https://nextjs.org/learn/dashboard-app/setting-up-your-database)챕터에서, 이 데이터로 데이터베이스를 *심을때(seed)* 사용할 겁니다.(초기데이터로 데이터 넣기).

### 타입스크립트
이 프로젝트는 타입스크립트로 쓰였기때문에 아마 대부분 파일이 `.ts` 또는 `.tsx` 확장자를 갖고 있는걸 확인 할 수 있습니다. 현대의 웹의 모습을 반영하는 강좌를 만들고 싶었습니다.

타입스크립트를 몰라도 괜찮습니다--필요할때 타입스크립트 코드를 제공해줄 겁니다.

지금은 `/app/lib/definitions.ts` 파일을 봅시다. 여기에, 우리는 수동으로 데이터베이스로부터 리턴될 타입들을 선언했습니다. 예를들어. invoices 테이블은 아래와 같은 타입을 갖습니다.

```
export type Invoice = {
  id: string;
  customer_id: string;
  amount: number;
  date: string;
  // In TypeScript, this is called a string union type.
  // It means that the "status" property can only be one of the two strings: 'pending' or 'paid'.
  status: 'pending' | 'paid';
};
```

타입스크립트를 사용함으로서, invoice의 `amount`에 `number`타입 대신 `string`을 보내는 것과 같은, 잘못된 데이터 형태가 컴퍼넌트나 데이터베이스로 보내지지 않았는지 확인 할 수 있습니다.


당신이 타입스크립트 개발자라면:
-  우리는 수동으로 데이터 타입을 정의하고 있습니다, 그러나 더 나은 타입 안정성을 위해, 자동으로 데이터베이스 스킴에 따라 타입들을 생성해주는 [Prisma](https://www.prisma.io/)를 추천합니다.
- Next.js는 당신의 프로젝트가 타입스크립트를 쓰고, 필요한 패키지와 설정을 설치 했는지 알아냅니다. 또한 자동완성과 타입 안정성을 위해 당신의 코드 에디터에 쓸 [타입스크립트 플러그인](https://nextjs.org/docs/app/building-your-application/configuring/typescript#typescript-plugin)을 Next.js와 함께 쓸 수 있습니다.

## 개발 서버 실행하기
프로젝트의 패키지들을 설치하기 위해 `npm i` 를 실행해보세요.
```
npm i
```
개발 서버를 실행하기 위해 다음의 `npm run dev`를 실행하세요
```
npm run dev
```

`npm run dev` 는 3000 포트에 Next.js 개발서버를 시작하는 명령어입니다.
이게 잘 동작하는지 확인해봅시다. 브라우저에 [http://localhost:3000](http://localhost:3000/)을 열어보세요. 홈페이지가 다음과 같을 겁니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Facme-unstyled.png&w=1920&q=75">


<div class="finish">
  <p class="finish__title">챕터 1을 완료했습니다.</p>
  <p>축하해요! 시작 예제로 Next.js 어플리케이션을 만들고 실행해보았습니다.</p>
  <div class="next-box">
    <p class="next">다음</p>    
    <p class="next__title">2: CSS 스타일하기</p>
    <p>당신의 어플리케이션을 스타일 할 수 있는 다양한 방법에 대해 실행하고 이야기 해봅시다.</p>
    <a id="next__btn" href="https://thewys.tistory.com/entry/NextJS-%ED%8A%9C%ED%86%A0%EB%A6%AC%EC%96%BC-%EC%B1%95%ED%84%B0-2-CSS-%EC%8A%A4%ED%83%80%EC%9D%BC%ED%95%98%EA%B8%B0">챕터 2 시작하기
<svg data-testid="geist-icon" height="16" stroke-linejoin="round" viewBox="0 0 16 16" width="16" style="color: currentcolor;"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.53033 2.21968L9 1.68935L7.93934 2.75001L8.46967 3.28034L12.4393 7.25001H1.75H1V8.75001H1.75H12.4393L8.46967 12.7197L7.93934 13.25L9 14.3107L9.53033 13.7803L14.6036 8.70711C14.9941 8.31659 14.9941 7.68342 14.6036 7.2929L9.53033 2.21968Z" fill="currentColor"></path></svg>
</a>
  </div>
</div>

<style>
  /* 찐파 - #006bff */
  /* 퀴즈 UI */
  .quiz {
    padding: 16px;
    background-color: #fafafa;
    border-radius: 16px;
    text-align: center;
  }

  .quiz__icon {
    display: flex;
    align-items: center;
    justify-content: center;
    margin: auto;
    margin-bottom: 16px;
    width: 56px;
    height: 56px;
    border-radius: 28px;
    background-color: #006bff;
    color: white; 
  }

  .quiz__title {
    font-weight: bold;
    font-size: 28px;
    color: black;
  }

  .quiz__box {
    padding: 24px;
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
  }

  .quiz__btn-container {
    margin-top: 16px;
    text-align: right;
  }

  .quiz__btn {
    padding: 14px;
    background: black;
    color: white !important;
    border-radius: 8px;
    text-decoration: none !important;
    font-weight: bold;
    font-size: 14px;
    cursor: pointer;
  }

  .quiz__question {
    color: black;
    font-size: 16px;
  }

  .option-list {
    background-color: #F4F4F4;
    border-color: #00000014;
    border: 1px solid;
    border-radius: 8px;
  }

  .option {
    cursor: pointer;
    display: flex;
    padding: 16px;
    align-items: center;
    
  }

  .option:first-child {
    border-top-left-radius: 8px;
    border-top-right-radius: 8px;
  }
  .option:last-child {
    border-bottom-left-radius: 8px;
    border-bottom-right-radius: 8px;
  }

  .option:hover {
    background: white;
  }

  .option__number {
    margin-right: 12px;
    display: flex;
    align-items: center;
    justify-content: center;
    width: 32px;
    height: 32px;
    flex-shrink: 0;
    color: #005ff2;
    background-color: #dfefff;
    border-radius: 9999px;
  }

  .option__desc {

  }

  .option-selected {
    background-color: white;

  }

  .option-selected .option__number {
    background-color: #006bff;
    color: white;
  }

  .option-selected.option-mark {
    background-color: #fca5a5;
  }

  .option-selected.option-mark .option__number {
    background-color: #b91c1c;
  }

  .option-mark[data-answer="true"] {
    background-color: #bbf7d0;
  }

  .option-selected.option-mark[data-answer="true"] .option__number {
    background-color: #16a34a;
  }

  /* 완료 UI */
  .finish {
    margin-top: 64px;
    margin-bottom: 32px;
    text-align: center;
  }

  .finish__title {
    font-size: 36px;
    font-weight: bold;
  }

  .next-box {
    color: #666666;
    border: 1px solid #CCCCCC;
    border-radius: 8px;
    padding: 24px 16px;
    font-size: 16px;
  }
  .next {
    font-size: 14px;
    margin: 0;
  }

  .next__title {
    color: black;
    font-size: 24px;
    font-weight: bold;
  }

  #next__btn {
    display: inline-block;
    font-size: 20px;
    padding: 14px;
    margin-bottom: 8px;
    background: black;
    color: white !important;
    border-radius: 8px;
    text-decoration: none !important;
  }

  h2 {
    font-weight: bold !important;
  }

  code {
    background-color: #ebebeb; 
    padding: 2px 4px;
    border-radius: 4px;
  }

  li {
    list-style: disc;
  }

  .tt_article_useless_p_margin p {
    padding-top: 8px !important;
    padding-bottom: 8px !important;
    margin: auto !important;
  }

  @media screen and (max-width: 600px) {
    .quiz__btn-container {
      text-align: center;
    }

    .quiz__btn {
      width: 100%;
    }

  }
</style>


## 몰랐던 단어 & 문장
Populating a databae: To add data to the database.

## Ref
[https://nextjs.org/learn?utm_source=next-site&utm_medium=homepage-cta&utm_campaign=home] <br>
[https://www.quora.com/What-is-meant-by-populating-database]

