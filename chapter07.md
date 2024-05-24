## 들어가기 전에

이 글은 [Next.js - Chapter 7 : Fetching Data](https://nextjs.org/learn/dashboard-app/fetching-data) 강의를 번역한 글입니다.

# 데이터 가져오기

이제 데이터베이스를 생성하고 초기데이터도 넣었으므로, 어플리케이션에 데이터를 가져올 다양한 방법에 대해 이야기해보고, 대시보드 Overview 페이지도 만듭시다.

## 이번 챕터에서...

여기 우리가 다룰 주제들이 있습니다.
- API, ORM, SQL 등 데이터를 가져오기 위한 다양한 접근법에 대해 배우기 
- 서버 컴퍼넌트가 더 안전하게 백엔드 자원을 가져올 수 있는 방법
- 네트워크 워터폴에 대해
- 자바스크립트 패턴을 이용해 데이터를 병렬로 가져오는 방법

## 데이터 가져올 방법 선택하기

### API 계층

API란 어플리케이션 코드와 데이터베이스 사이에 있는 중간 계층입니다. 

여기 API를 써야할 몇가지 경우가 있습니다.
- 우리가 API를 제공하는 3자업체 서비스를 이용할때
- 클라이언트에서 데이터를 가져온다면, 클라이언트에게 데이터베이스 비밀값들을 노출되는걸 피하기 위해 서버에 API 계층을 갖기를 원할겁니다.

Next.js에서, [Route Handlers](https://nextjs.org/docs/app/building-your-application/routing/route-handlers)를 사용해 API를 만들 수 있습니다.

### 데이터베이스 질의

풀스택 어플리케이션을 만들때, 우리는 데이터베이스와 상호작용하는 로직이 필요합니다.
Postgres같은 [관계형 데이터베이스](https://aws.amazon.com/relational-database/)에서는 이것을 SQL이나 [Prisma](https://www.prisma.io/) 같은 [ORM](https://vercel.com/docs/storage/vercel-postgres/using-an-orm#)으로 할 수 있습니다.

여기 우리가 데이터베이스 쿼리를 작성해야할 몇가지 경우가 있습니다.
- API를 생성할때, 데이터베이스와 상호작용 해야합니다.
- 만약 리액트 서버 컴퍼넌트(서버에서 데이터 가져오는)를 사용한다면, API layer를 스킵하고 데이터베이스 비밀값들이 노출되는 위험 없이 DB에 직접 질의할 수 있습니다.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">이 시나리오 중 어떤것이 데이터베이스에 직접적으로 질의를 하지 않는것 일까요?</p>
    <div class="option-list">
      <div class="option" data-question-number="01" data-answer="true">
        <span class="option__number">A</span>
        <span class="option__desc">클라이언트에서 데이터를 가져올때</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">B</span>
        <span class="option__desc">서버에서 데이터를 가져올때</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">C</span>
        <span class="option__desc">데이터베이스와 상호작용할 자신의 API를 만들때</span>
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

리액트 서버 컴퍼넌트에 대해 더 배워봅시다.

### 데이터를 가져오기 위한 서버 컴퍼넌트 사용

기본적으로, Next.js 어플리케이션은 **리액트 서버 컴퍼넌트**를 사용합니다. 서버 컴퍼넌트로 데이터를 가져오는것은 상대적으로 새로운 접근이고, 이것의 장점도 몇가지 있습니다.

- 서버 컴퍼넌트는 데이터를 가져오는 작업과 같은 비동기 작업을 위한 간단한 솔루션인 promise를 지원합니다. 또 `useEffect`, `useState` 나 데이터 가져오는 라이브러리에 대한 접근 없이 `async/await` 도 사용할 수 있습니다.
- 서버 컴퍼넌트는 서버에서 실행되고, 데이터를 가져오거나 연산처리 같은 처리비용이 드는 부분들을 서버에서 처리하게 두고, 오직 결과만 클라이언트로 보낼 수 있습니다. 
- 앞서 이야기 했듯, 서버 컴퍼넌트는 서버에서 실행되기 때문에, 추가적인 API 계층 없이 바로 데이터베이스에 질의 할 수 있습니다.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">리액트 서버 컴퍼넌트를 사용해서 데이터를 가져올때의 이점이 무엇인가요?</p>
    <div class="option-list">
      <div class="option" data-question-number="02">
        <span class="option__number">A</span>
        <span class="option__desc">SQL 주입으로부터 자동으로 보호됩니다.</span>
      </div>
      <div class="option" data-question-number="02" data-answer="true">
        <span class="option__number">B</span>
        <span class="option__desc">추가적인 API 계층없이 직접적으로 서버에서 데이터베이스에 질의 할 수 있게 해줍니다.</span>
      </div>
      <div class="option" data-question-number="02">
        <span class="option__number">C</span>
        <span class="option__desc">API 계층을 생성하고 엔드포인트를 만들도록 합니다.</span>
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

### SQL 사용하기
우리 대시보드 프로젝트에서, [Vercel Postgres SDK](https://vercel.com/docs/storage/vercel-postgres/sdk)와 SQL을 사용해 데이터베이스 쿼리를 작성할 겁니다. SQL을 사용하는 몇가지 이유가 있습니다.

- SQL은 관계형 데이터베이스에서 질의를 위한 산업 표준입니다.(예를들어 ORM은 그 밑단에서 SQL을 생성합니다.)
- 기본적은 SQL에 대한 이해는 다른 툴에서도 사용할 수 있도록 관계형 데이터베이스 기본에 대한 이해를 도와줄겁니다.
- SQL은 유용합니다, 데이터를 가져오고, 특정 데이터를 바꾸도록 해줄 수 있습니다.
- Vercel Postgres SDK는 [SQL 주입](https://vercel.com/docs/storage/vercel-postgres/sdk#preventing-sql-injections)으로부터 지켜줍니다.

SQL을 전에 해보지 않았더라도 걱정마세요 - 우리가 쿼리를 제공해줄겁니다.

`/app/lib/data.ts` 로 가서, 우리가 `@vercel/postgres`에서 불러오는 ['sql'](https://vercel.com/docs/storage/vercel-postgres/sdk#sql)함수를 볼 수 있습니다. 이 함수는 우리가 데이터베이스에 질의 할 수 있도록 해줍니다.

<div class="code-with-file">
/app/lib/data.ts

```
import { sql } from '@vercel/postgres';
 
// ...
```
</div>

우리는 어떠한 서버 컴퍼넌트 내에서도 `sql`을 호출할 수 있습니다. 더 쉽게 찾아보기 위해, 우리는 모든 데이터 쿼리를 `data.ts` 파일 안에 두었습니다. 이제 그것을 우리 컴퍼넌트에 불러(import)옵시다.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">데이터를 가져오는데 있어 SQL은 무엇을 하나요?</p>
    <div class="option-list">
      <div class="option" data-question-number="03">
        <span class="option__number">A</span>
        <span class="option__desc">데이터를 무차별적으로 조작하고 가져옵니다.</span>
      </div>
      <div class="option" data-question-number="03" data-answer="true">
        <span class="option__number">B</span>
        <span class="option__desc">특정한 데이터를 가져오고 조작할 수 있습니다.</span>
      </div>
      <div class="option" data-question-number="03">
        <span class="option__number">C</span>
        <span class="option__desc">성능향상을 위해 자동으로 데이터를 캐시합니다.</span>
      </div>
      <div class="option" data-question-number="03">
        <span class="option__number">D</span>
        <span class="option__desc">자동으로 데이터베이스 스키마를 바꿉니다</span>
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

<br>
<br>
<div class="hint">

**주의:**
- 챕터 6에서 자신의 데이터베이스를 사용했다면, 데이터베이스 쿼리를 자신의 것에 맞게 업데이트 해야합니다. `/app/lib/data.ts`에서 쿼리를 찾을 수 있습니다.

</div>

## 대시보드 Overiew 페이지에 데이터 가져오기

이제 데이터를 가져오는 다양한 방법에 대해 이해했으니, 대시보드 overview 페이지에 필요한 데이터를 가져옵시다. 우선 `/app/dashboard/page.tsx`로 이동해서, 아래 코드를 붙여 넣으시고 이것을 한번 알아봅시다.

<div class="code-with-file">
/app/dashboard/page.tsx

```
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
 
export default async function Page() {
  return (
    <main>
      <h1 className={`${lusitana.className} mb-4 text-xl md:text-2xl`}>
        Dashboard
      </h1>
      <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        {/* <Card title="Collected" value={totalPaidInvoices} type="collected" /> */}
        {/* <Card title="Pending" value={totalPendingInvoices} type="pending" /> */}
        {/* <Card title="Total Invoices" value={numberOfInvoices} type="invoices" /> */}
        {/* <Card
          title="Total Customers"
          value={numberOfCustomers}
          type="customers"
        /> */}
      </div>
      <div className="mt-6 grid grid-cols-1 gap-6 md:grid-cols-4 lg:grid-cols-8">
        {/* <RevenueChart revenue={revenue}  /> */}
        {/* <LatestInvoices latestInvoices={latestInvoices} /> */}
      </div>
    </main>
  );
}
```
</div>

위 코드에서,
- Page는 **비동기** 컴퍼넌트 입니다. 이것은 우리가 데이터를 가져오기 위해 `await`를 사용할 수 있도록 해줍니다.
- `<Card>`, `<RevenueChart`, `LatestInvoices` 데이터를 받을 3개 컴퍼넌트가 더 있습니다. 그것들은 에러를 막기위해 현재 주석처리 되어있습니다.

## `<RevenueChart />` 에 데이터 가져오기

`<RevenueChart />` 에 데이터를 가져오려면, `data.ts`에서 `fetchRevenue` 함수를 불러(import)오고 컴퍼넌트 안에서 호출합시다.

<div class="code-with-file">
/app/dashboard/page.tsx

```
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import { fetchRevenue } from '@/app/lib/data';
 
export default async function Page() {
  const revenue = await fetchRevenue();
  // ...
}
```
</div>

그 다음, 파일내에 `<RevenueChart />` 컴퍼넌트의 주석을 제거하고, 해당 컴퍼넌트 파일(`/app/ui/dashboard/revenue-chart.tsx`)로 이동해 그 안에 주석을 제거하세요. 로컬호스트에서 확인합시다. 이제 `revenue` 데이터를 사용하는 차트를 볼 수 있습니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Frecent-revenue.png&w=1920&q=75" alt="오버뷰 페이지 첫 완성">

계속해서 더 많은 데이터 쿼리를 불러옵시다!

## `<LatestInvoices />` 에 데이터 가져오기

`<LatestInvoices />` 에서 우리는 날짜로 정렬된 5개의 최신 송장들을 받아야 합니다.

모든 송장 데이터를 받고 자바스크립트로 이것을 정렬할 수 있습니다. 이것은 데이터가 작을때는 문제가 되지 않지만, 어플리케이션이 성장하면서 각 리퀘스트마다 전송되는 데이터 양이 너무나 커질 수 있고 자바스크립트는 그 큰 데이터를 정렬해야 합니다.

최신 송장을 메모리영역에서 정렬하는것 보다는, SQL 질의로 최신 5개의 데이터만 가져올 수 있습니다. 예를들어, 이것은 `data.ts` 파일에 있는 SQL 쿼리입니다.

<div class="code-with-file">
/app/lib/data.ts

```
// Fetch the last 5 invoices, sorted by date
const data = await sql<LatestInvoiceRaw>`
  SELECT invoices.amount, customers.name, customers.image_url, customers.email
  FROM invoices
  JOIN customers ON invoices.customer_id = customers.id
  ORDER BY invoices.date DESC
  LIMIT 5`;
```
</div>

이제 페이지에서, `fetchLatestInvoices` 함수를 불러(import)오세요.

<div class="code-with-file">
/app/dashboard/page.tsx

```
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import { fetchRevenue, fetchLatestInvoices } from '@/app/lib/data';
 
export default async function Page() {
  const revenue = await fetchRevenue();
  const latestInvoices = await fetchLatestInvoices();
  // ...
}
```
</div>

이제, `<LatestInvoices />` 컴퍼넌트의 주석을 지웁시다. 그리고 또한 `/app/ui/dashboard/latest-invoices`에 위치한 `<LatestInvoices />` 내에서 관련된 코드의 주석을 제거해야합니다.

이제 로컬호스트로 다시 가보면, 데이터베이스에서 오직 5개만 받는 것을 볼겁니다. 데이터베이스에 직접 질의하는것에 장점을 보고 있기를 바랍니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Flatest-invoices.png&w=1920&q=75" alt="5개의 최신 송장이 뜨는 overview 페이지">


## 실습해보기: `<Card>` 컴퍼넌트에 데이터 가져오기

이제, 당신의 차례입니다. `<Card>` 컴퍼넌트에 데이터를 가져오세요. 카드는 아래의 데이터를 보여줄겁니다.

- 지급된 송장의 금액
- 지연되는 송장의 금액
- 모든 송장의 개수
- 모든 고객의 수

다시, 모든 고객과 송장을 가져오고 자바스크립트로 데이터를 조작하고 싶긴 하겠지만, (예를들어 송장과 고객의 수를 가져오기 위해 `Array.length` 를 사용할 수 있습니다.)

```
const totalInvoices = allInvoices.length;
const totalCustomers = allCustomers.length;
```

그러나 SQL을 사용해, 오직 필요한 데이터만 가져올 수 있습니다. 이것은 `Array.length`를 사용하는것보다는 약간 길지만, 요청에서 더 적은 데이터만 전달된다는 것을 의미합니다. 여기 SQL 대안이 있습니다.

<div class="code-with-data">
/app/lib/data.ts

```
const invoiceCountPromise = sql`SELECT COUNT(*) FROM invoices`;
const customerCountPromise = sql`SELECT COUNT(*) FROM customers`;
```
</div>

우리가 불러(import)올 함수는 `fetchCardData` 라고 불립니다. 함수에서 반환된 값을 나눠야 합니다.

<div class="hint">

**힌트:**
- 어떤 데이터가 필요한지 카드 컴퍼넌트를 확인하세요.
- 무엇을 리턴하는지 `data.ts` 파일을 확인하세요.

</div>

이제 준비가 되었으면, 결과 코드를 확인하러 아래 토글버튼을 누르세요.

<button class="show-and-hide-btn--hidden show-and-hide-btn btn" data-target="practice-01">
  <span data-btn-content>정답 보이기</span>
</button>

<div class="is-inactive" data-hide-target="practice-01">

<div class="code-with-file">
/app/dashboard/page.tsx

```
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import {
  fetchRevenue,
  fetchLatestInvoices,
  fetchCardData,
} from '@/app/lib/data';
 
export default async function Page() {
  const revenue = await fetchRevenue();
  const latestInvoices = await fetchLatestInvoices();
  const {
    numberOfInvoices,
    numberOfCustomers,
    totalPaidInvoices,
    totalPendingInvoices,
  } = await fetchCardData();
 
  return (
    <main>
      <h1 className={`${lusitana.className} mb-4 text-xl md:text-2xl`}>
        Dashboard
      </h1>
      <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        <Card title="Collected" value={totalPaidInvoices} type="collected" />
        <Card title="Pending" value={totalPendingInvoices} type="pending" />
        <Card title="Total Invoices" value={numberOfInvoices} type="invoices" />
        <Card
          title="Total Customers"
          value={numberOfCustomers}
          type="customers"
        />
      </div>
      <div className="mt-6 grid grid-cols-1 gap-6 md:grid-cols-4 lg:grid-cols-8">
        <RevenueChart revenue={revenue} />
        <LatestInvoices latestInvoices={latestInvoices} />
      </div>
    </main>
  );
}
```
</div>
</div>

훌륭합니다!. 이제 대시보드 overview 페이지에 모든 데이터를 가져왔습니다. 당신의 페이지는 아래와 같을거에요.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fcomplete-dashboard.png&w=1920&q=75" alt="대시보드 화면 완성">

그러나 2가지 알아야 할 것들이 있습니다.
- 데이터 요청은 **리퀘스트 워터폴**을 생성하며 의도치 않게 다른것을 막을 수 있습니다. 
- 기본적으로 Next.js는 퍼포먼스 향상을 위해 라우트들을 **미리 렌더링** 합니다. 이것을 **정적 렌더링** 이라 부릅니다. 만약 데이터가 바뀐다면, 대시보드에 실시간으로 반영되지 않을겁니다.

## 리퀘스트 워터폴이란?

"워터폴"은 이전 요청 결과에 의존하는 일련의 네트워크 요청들을 의미합니다. 데이터를 가져오는 경우에, 각각의 요청들은 이전의 요청이 데이터를 반환해야 시작할 수 있습니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fsequential-parallel-data-fetching.png&w=3840&q=75" alt="리퀘스트 워터폴">

예를들어, 우리는 `fetchLatestInvoice()` 를 실행하기 전 `fetchRevenue()` 를 기다립니다. 

<div class="code-with-file">
/app/dashboard/page.tsx

```
const revenue = await fetchRevenue();
const latestInvoices = await fetchLatestInvoices(); // wait for fetchRevenue() to finish
const {
  numberOfInvoices,
  numberOfCustomers,
  totalPaidInvoices,
  totalPendingInvoices,
} = await fetchCardData(); // wait for fetchLatestInvoices() to finish
```
</div>

이 패턴은 나쁘지는 않습니다. 다음 리퀘스트를 생성하기 전 앞 요청에 따라 조건을 넣고 싶다면 이런 워터폴 방식을 원할 때도 있습니다. 예를들어 유저의 ID와 프로필 정보를 먼저 가저온 후, 그들의 친구목록을 가져오는것이죠. 이 경우에는 각각의 요청은 앞의 요청에 따라 달라집니다.

그러나, 이 행동은 의도치않게 성능에 영향을 끼칩니다.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">언제 워터폴 방식을 사용하길 바랄까요?</p>
    <div class="option-list">
      <div class="option" data-question-number="04" data-answer="true">
        <span class="option__number">A</span>
        <span class="option__desc">다음 요청을 만들기 전 조건을 만족시키기 위해</span>
      </div>
      <div class="option" data-question-number="04">
        <span class="option__number">B</span>
        <span class="option__desc">모든 요청을 동시에 만들기 위해</span>
      </div>
      <div class="option" data-question-number="04">
        <span class="option__number">C</span>
        <span class="option__desc">한번에 하나씩 데이터를 가져옴으로서 서버부하를 줄이기 위해</span>
      </div>
    </div>
    <div class="quiz__btn-container">
      <button class="quiz__btn"
        data-js-check-answer data-question="04">
        정답 확인
      </button>
    </div>
  </div>  
</div>

## 병렬로 데이터 가져오기

워터폴을 피할 일반적인 방법은 모든 데이터 요청을 동시에 하는겁니다. - 병렬적으로

자바스크립트에서, [`Promise.all()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) 또는 [`Promise.allSettled()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/allSettled) 함수로 모든 요청을 동시에 할 수 있습니다.
예를들어 `data.ts` 파일 `fetchCardData()` 내에, `Promise.all()` 을 사용하고 있습니다.

<div class="code-with-file">
/app/lib/data.js

```
export async function fetchCardData() {
  try {
    const invoiceCountPromise = sql`SELECT COUNT(*) FROM invoices`;
    const customerCountPromise = sql`SELECT COUNT(*) FROM customers`;
    const invoiceStatusPromise = sql`SELECT
         SUM(CASE WHEN status = 'paid' THEN amount ELSE 0 END) AS "paid",
         SUM(CASE WHEN status = 'pending' THEN amount ELSE 0 END) AS "pending"
         FROM invoices`;
 
    const data = await Promise.all([
      invoiceCountPromise,
      customerCountPromise,
      invoiceStatusPromise,
    ]);
    // ...
  }
}
```
</div>

이 패턴은
- 동시에 모든 데이터 가져오는 리퀘스트를 실행할 수 있습니다, 그리고 그것은 퍼포먼스가 향상될 수 있습니다.
- 어떤 라이브러리나 프레임워크에도 사용될 수 있는 네이티브 자바스크립트 패턴을 사용하는겁니다.

그러나, 이 자바스크립트 패턴엔 한가지 단점이 있습니다. 만약 한가지 데이터 요청이 다른것보다 느리다면 어떻게 될까요?


<div class="finish">
  <p class="finish__title">챕터 7을 완료했습니다.</p>
  <p>Next.js에서 데이터를 가져오는 다양한 방법을 배웠습니다.</p>
  <div class="next-box">
    <p class="next">다음</p>    
    <p class="next__title">8: 정적 그리고 동적 렌더링</p>
    <p>PPR과 Streaming으로 데이터 가져오기를 더 최적화 하는 방법을 배웁니다.</p>
    <a id="next__btn" href="https://nextjs.org/learn/dashboard-app/static-and-dynamic-rendering">챕터 8 시작하기
    <svg data-testid="geist-icon" height="16" stroke-linejoin="round" viewBox="0 0 16 16" width="16" style="color: currentcolor;"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.53033 2.21968L9 1.68935L7.93934 2.75001L8.46967 3.28034L12.4393 7.25001H1.75H1V8.75001H1.75H12.4393L8.46967 12.7197L7.93934 13.25L9 14.3107L9.53033 13.7803L14.6036 8.70711C14.9941 8.31659 14.9941 7.68342 14.6036 7.2929L9.53033 2.21968Z" fill="currentColor"></path></svg>
    </a>
  </div>
</div>

## Ref
- [https://nextjs.org/learn/dashboard-app/fetching-data](https://nextjs.org/learn/dashboard-app/fetching-data)


<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/next-js-tutorial/css.css">
<script type="text/javascript" src="https://eso0117.github.io/web-practice/public/next-js-tutorial/js.js"></script>
