## 들어가기 전에

이 글은 [Next.js - Chapter 9 : Streaming](https://nextjs.org/learn/dashboard-app/streaming) 강의를 번역한 글입니다.

# 스트리밍

이전 챕터에서, 대시보드 페이지를 동적 렌더링 하도록 만들었습니다. 하지만 느린 데이터 가져오기가 우리 어플리케이션 퍼포먼스에 어떤 영향을 끼치는지도 볼 수 있었습니다. 이제 느린 데이터 요청이 있을때, 어떻게 유저 경험을 향상시킬 수 있을지 이야기 해봅시다.

## 이번 챕터에서...

여기 우리가 다룰 주제들이 있습니다.
- 스트리밍이 무엇인지, 언제 쓸 수 있는지
- 스트리밍과 `loading.tsx` 그리고 `<Suspense>`을 어떻게 같이 쓸 수 있는지
- 로딩 스켈레톤이란 무엇인지
- 라우트 그룹이란 무엇이고, 언제 그것을 쓰면 되는지
- 어플리케이션 내에 Suspense 영역을 어디에 넣으면 되는지

## 스트리밍이란 무엇인가요?

스트리밍은 하나의 라우트를 더 작은 "청크(chunks)"로 나누어서 계속해서 서버로부터 그것들이 준비 될때 데이터를 보내는(stream) 데이터 전송 기술입니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fserver-rendering-with-streaming.png&w=3840&q=75" alt="Nextjs 스트리밍 설명">

스트리밍으로, 전체 페이지가 블럭되는 느린 데이터 요청을 막을 수 있습니다. 이를 통해 유저는 모든 데이터를 불러오는 걸 기다릴 필요 없이 페이지의 일부를 보고 상호작용 할 수 있습니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fserver-rendering-with-streaming-chart.png&w=3840&q=75" alt="스트리밍과 일부 UI 표시되는 예시">

스트리밍은 각각의 컴퍼넌트가 *청크(chunk)*으로 간주 될 수 있는 리액트의 컴퍼넌트 모델과 잘 맞습니다.

Next.js의 스트리밍을 할 방법이 2가지 있습니다.

1. 페이지 레벨에서, `loading.tsx` 파일을 사용
2. `<Suspense>`라는 특정한 컴퍼넌트 사용

이제 하나씩 살펴봅시다.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">스트리밍의 장점 한가지는 무엇인가요?</p>
    <div class="option-list">
      <div class="option" data-question-number="01">
        <span class="option__number">A</span>
        <span class="option__desc">데이터 요청이 청크(chunk) 암호화를 통해 더 안전해집니다.</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">B</span>
        <span class="option__desc">모든 청크들(chunks)이 데이터를 완전히 받고 나서 렌더링 됩니다.</span>
      </div>
      <div class="option" data-question-number="01" data-answer="true">
        <span class="option__number">C</span>
        <span class="option__desc">전체 로드 타임을 줄이며, 청크들(chunks)이 병렬적으로 렌더링됩니다.</span>
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

## `loading.tsx` 사용하며 전체페이지 스트리밍 하기

`/app/dashboard` 폴더에서, `loading.tsx` 파일을 생성합시다.

<div class="code-with-file">
/app/dashboard/loading.tsx

```
export default function Loading() {
  return <div>Loading...</div>;
}
```
</div>

[http://localhost:3000/dashboard](http://localhost:3000/dashboard)를 새로고침 하세요, 그러면 이제 이것을 볼 수 있습니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Floading-page.png&w=1920&q=75" alt="로딩 페이지가 보이는 Next.js 대시보스">

여기서 몇가지 일이 일어나고있습니다:

1. `loading.tsx`는 Suspense위에 만들어진 Next.js의 특별한 파일입니다. 이것은 페이지 컨텐츠를 불러오는 동안 대체화면이 보이도록 해줍니다.
2. `<SideNav>`는 정적이므로, 이것은 바로 보입니다. 유저는 `<SideNav>`에서 동적페이지가 로딩되는 동안 상호작용 할 수 있습니다.
3. 유저는 다른 페이지로 가기 전 로딩이 완료 될 때까지 페이지를 기다릴 필요 없습니다.(이것을 중단 가능한 페이지 이동이라 부릅니다).

축하합니다! 우리는 방금 스트리밍을 구현했습니다. 하지만 우리는 유저 경험을 더 향상시킬 수 있습니다. `Loading...` 글자가 아닌 로딩 스켈레톤을 만들어봅시다.

### 로딩 스켈레톤 넣기

로딩 스켈레톤은 UI의 단순화된 버전 입니다. 많은 웹사이트들이 유저들에게 컨텐츠가 로딩되고 있다고 알리는 용도로 그것을 이용합니다(혹은 대체화면을요).
`loading.tsx`에 있는 어느 UI도 정적 파일의 일부로서 포함되고 먼저 전송됩니다. 그러면 나머지 동적 컨텐츠들이 서버로부터 클라이언트로 스트림됩니다.

`loading.tsx`파일 내에, `<DashboardSkeleton>`이라 불리는 새로운 컴퍼넌트를 불러(import)옵시다.

<div class="code-with-file">
/app/dashboard/loading.tsx

```
import DashboardSkeleton from '@/app/ui/skeletons';
 
export default function Loading() {
  return <DashboardSkeleton />;
}
```
</div>

그리고 나서, [http://localhost:3000/dashboard](http://localhost:3000/dashboard)를 새로고침 합시다. 그러면 아래와 같은 화면을 볼 수 있습니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Floading-page-with-skeleton.png&w=1920&q=75" alt="Loaindg skeleton in nextjs">

### 라우트 그룹으로 로딩 스켈레톤 버그 고치기

지금, 우리의 로딩 스켈레톤은 invoices나 customers 페이지에도 적용될 겁니다.

`loading.tsx`가 `/invoices/page.tsx`나 `/customers/page.tsx` 보다 파일시스템 상의 상위 계층에 있기 때문에, 모든 하위 페이지에 적용됩니다.

이것을 [Route Groups](https://nextjs.org/docs/app/building-your-application/routing/route-groups)로 바꿀 수 있습니다.
대시보드 폴더 아래 `/(overview)` 폴더를 만듭시다. 그리고 `loading.tsx`와 `page.tsx`파일을 해당 폴더 안으로 옮깁니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Froute-group.png&w=3840&q=75" alt="nextjs /(overview) 생성과 파일구조 변경">

이제 `loading.tsx`파일은 오직 dashboard overview 페이지에만 적용됩니다.

라우트 그룹은 URL 경로의 영향을 미치지 않으면서 논리적 그룹으로 정리할 수 있게 해줍니다. `()` 괄호를 넣어 폴더를 생성할떄, 그 이름은 URL 경로에 포함 되지 않습니다. 그래서 `/dashboard/(overview)/page.tsx`는 `/dashboard` 가 됩니다.

여기선 오직 dashboard overview 페이지에서만 `loading.tsx`가 적용되도록 라우트 그룹을 사용하고 있지만 어플리케이션에서 라우트 그룹을 몇가지 섹션으로 분리하는데 이용할 수 있습니다. (예를 들어 `(marketing)` 라우트 그리고 `(shop)` 라우트) 또는 큰 어플리케이션의 용어대로도 가능합니다.

### 컴퍼넌트 스트리밍하기

지금까지, 우린 전체 페이지를 스트리밍하고 있었습니다. 하지만 리액트의 `<Suspense>`을 이용해서 더 작게 특정 컴퍼넌트들에 스트리밍 할 수도 있습니다.

`Suspense`은 어플리케이션에서 특정한 부분을 조건이 충족될때까지 렌더링을 미룰 수 있게 해줍니다.(예를 들어 데이터가 로드되는 것). 동적 컴퍼넌트를 지 `Suspense`으로 감쌀 수 있습니다. 그리고 나서, 동적 컴퍼넌트가 불러와지는 동안 보여줄 대체 컴퍼넌트를 넘겨줍니다.

전체 페이지를 느리게 하는 리퀘스트`fetchRevenue()`가 기억난다면,
전체 페이지를 블럭하는 대신, `Suspense`를 이 컴퍼넌트에 사용하고, 페이지의 나머지 부분을 즉시 보이게 할 수 있습니다.

이렇게 하기 위해, 데이터를 가져오는 컴퍼넌트를 옮길 필요가 있습니다. 코드를 업데이트 합시다.

`fetchRevenue()`와 그 데이터를 `/dashboard/(overview)/page.tsx`에서 지우세요.

<div class="code-with-file">
/app/dashboard/(overview)/page.tsx

```
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import { fetchLatestInvoices, fetchCardData } from '@/app/lib/data'; // remove fetchRevenue
 
export default async function Page() {
  const revenue = await fetchRevenue // delete this line
  const latestInvoices = await fetchLatestInvoices();
  const {
    numberOfInvoices,
    numberOfCustomers,
    totalPaidInvoices,
    totalPendingInvoices,
  } = await fetchCardData();
 
  return (
    // ...
  );
}
```
</div>

그리고 나서, `<Suspense>`를 React에서 불러(import)오세요, 그리고 `<RevenueChart />`를 감쌉니다. 또 `<RevenueChartSkeleton>`인 대체 UI 컴퍼넌트를 보낼 수 있습니다.

<div class="code-with-file">
/app/dashboard/(overview)/page.tsx

```
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import { fetchLatestInvoices, fetchCardData } from '@/app/lib/data';
import { Suspense } from 'react';
import { RevenueChartSkeleton } from '@/app/ui/skeletons';
 
export default async function Page() {
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
        <Suspense fallback={<RevenueChartSkeleton />}>
          <RevenueChart />
        </Suspense>
        <LatestInvoices latestInvoices={latestInvoices} />
      </div>
    </main>
  );
}
```
</div>

마지막으로, `<RevenueChart>` 컴퍼넌트를 업데이트해서, 자체 데이터를 가져오고 전달된 prop을 없앱니다.

<div class="code-with-file">
/app/ui/dashboard/revenue-chart.tsx

```
import { generateYAxis } from '@/app/lib/utils';
import { CalendarIcon } from '@heroicons/react/24/outline';
import { lusitana } from '@/app/ui/fonts';
import { fetchRevenue } from '@/app/lib/data';
 
// ...
 
export default async function RevenueChart() { // Make component async, remove the props
  const revenue = await fetchRevenue(); // Fetch data inside the component
 
  const chartHeight = 350;
  const { yAxisLabels, topLabel } = generateYAxis(revenue);
 
  if (!revenue || revenue.length === 0) {
    return <p className="mt-4 text-gray-400">No data available.</p>;
  }
 
  return (
    // ...
  );
}
 
```
</div>

이제 페이지를 새로고침 하세요, `<RevenueChart>`에 스켈레톤이 나오는 동안 대시보드 정보가 거의 바로 나오는 것을 볼 수 있습니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Floading-revenue-chart.png&w=1920&q=75" alt="바로 나오는 UI">

## 실습해보기: `<Card>` 컴퍼넌트에 데이터 가져오기

이제 당신의 차례입니다! `<LatestInvoices>` 컴퍼넌트를 스트리밍하여 방금 배운 것을 실험해봅시다.

`fetchLatestInvoices()`를 페이지에서 `<LatestInvoices>` 컴퍼넌트로 옮기세요. 그리고 그 컴퍼넌트를 `<Suspense>`로 감싸고 로딩동안 뜰 `<LatestInvoicesSkeleton>` 대체화면을 넘기세요.

준비가 되면 아래 토글 버튼을 눌러 해설 코드를 확인합시다.

<button class="show-and-hide-btn--hidden show-and-hide-btn btn" data-target="practice-01">
  <span data-btn-content>정답 보이기</span>
</button>

<div class="is-inactive" data-hide-target="practice-01">

대시보드 페이지:
<div class="code-with-file">
/app/dashboard/(overview)/page.tsx

```
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import { fetchCardData } from '@/app/lib/data'; // Remove fetchLatestInvoices
import { Suspense } from 'react';
import {
  RevenueChartSkeleton,
  LatestInvoicesSkeleton,
} from '@/app/ui/skeletons';
 
export default async function Page() {
  // Remove `const latestInvoices = await fetchLatestInvoices()`
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
        <Suspense fallback={<RevenueChartSkeleton />}>
          <RevenueChart />
        </Suspense>
        <Suspense fallback={<LatestInvoicesSkeleton />}>
          <LatestInvoices />
        </Suspense>
      </div>
    </main>
  );
}
```
</div>

`<LatestInvoices>` 컴퍼넌트. props 지우는거 잊지마세요!
<div class="code-with-file">
/app/ui/dashboard/latest-invoices.tsx

```
import { ArrowPathIcon } from '@heroicons/react/24/outline';
import clsx from 'clsx';
import Image from 'next/image';
import { lusitana } from '@/app/ui/fonts';
import { fetchLatestInvoices } from '@/app/lib/data';
 
export default async function LatestInvoices() { // Remove props
  const latestInvoices = await fetchLatestInvoices();
 
  return (
    // ...
  );
}
```
</div>

</div>

## 컴퍼넌트 그룹핑

훌륭합니다! 거의 다 했어요. 이제 `<Card>` 컴퍼넌트를 `Suspense`로 감쌉시다. 각각 카드에서 데이터를 가져오게 만들 수 있지만, 카드 데이터가 불러와 질때마다 UI가 나타나니 유저들에게 거슬릴 수 있습니다.

그럼 어떻게 이 문제를 해결할 수 있을까요?

조금 더 시차가 있는 효과를 주기 위해, 하나의 레퍼(wrapper) 컴퍼넌트로 카드를 그룹화 할 수 있습니다. 정적 컴퍼넌트인 `<SindeNav />`가 제일 먼저 나타날 것이고, 그다음 카드 등이 나타나게 됩니다.

`page.tsx` 파일에서

1. `<Card>` 컴퍼넌트를 지우세요
2. `fetchCardData()` 함수를 지우세요
3. 레퍼(wrapper) 컴퍼넌트 `<Cardwrapper />`를 불러(import)오세요.
4. 스켈레톤 컴퍼넌트 `<CardSkeleton />`을 불러(import)오세요.
5. <CardWrapper />을 `<Suspense>`로 감싸세요

<div class="/app/dashboard/page.tsx">
/app/dashboard/page.tsx

```
import CardWrapper from '@/app/ui/dashboard/cards';
// ...
import {
  RevenueChartSkeleton,
  LatestInvoicesSkeleton,
  CardsSkeleton,
} from '@/app/ui/skeletons';
 
export default async function Page() {
  return (
    <main>
      <h1 className={`${lusitana.className} mb-4 text-xl md:text-2xl`}>
        Dashboard
      </h1>
      <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        <Suspense fallback={<CardsSkeleton />}>
          <CardWrapper />
        </Suspense>
      </div>
      // ...
    </main>
  );
}
```
</div>

그 다음, `/app/ui/dashboard/card.tsx` 로 이동해, `fetchCardData()`를 불러(import)온 다음, `<CardWrapper />` 컴퍼넌트 안에서 실행하세요. 이 컴퍼넌트에 필요한 모든 코드를 주석 해제되었는지 확인하세요.

<div class="/app/ui/dashboard/cards.tsx">
/app/ui/dashboard/cards.tsx

```
// ...
import { fetchCardData } from '@/app/lib/data';
 
// ...
 
export default async function CardWrapper() {
  const {
    numberOfInvoices,
    numberOfCustomers,
    totalPaidInvoices,
    totalPendingInvoices,
  } = await fetchCardData();
 
  return (
    <>
      <Card title="Collected" value={totalPaidInvoices} type="collected" />
      <Card title="Pending" value={totalPendingInvoices} type="pending" />
      <Card title="Total Invoices" value={numberOfInvoices} type="invoices" />
      <Card
        title="Total Customers"
        value={numberOfCustomers}
        type="customers"
      />
    </>
  );
}
```
</div>

페이지를 새로고침 합시다, 그러면 카드들이 동시에 불러와지는 걸 볼 수 있습니다.
이 패턴은 동시에 여러 컴퍼넌트를 불러올때 사용할 수 있습니다.

## Suspense를 어느 영역에 넣을지 결정하기

`Suspense`로 감쌀 부분은 몇가지에 의해 결정됩니다.

1. 스트림 되는 동안 유저가 어떤 경험을 하길 원하는지
2. 우선순위가 되는 컨텐츠가 무엇인지
3. 컴퍼넌트들이 데이터 가져오는게 필요한지

대시보드 페이지를 한번 보면, 다르게 할 만한 부분이 있나요?

걱정마세요. 정답은 없습니다.

- `loading.tsx`로 했던것처럼 **전체 페이지**를 스트림 할 수 있습니다. 그러나 이것은 만약 데이터 가져오는데 오래걸리는 컴퍼넌트가 있다면 전체적으로 더 많은 로딩 시간이 들게 합니다.
- 각각의 **모든 컴퍼넌트에** 스트림을 할 수 있습니다만 이것은 각 컴퍼넌트가 준비가 될때마다 UI가 *불쑥 나타나게* 됩니다.
- 또한 페이지 영역을 스트리밍 할때 *지연* 효과를 사용할 수도 있습니다만 레퍼(wrapper) 컴퍼넌트를 만들어야 합니다.

Suspense를 넣을 영역은 어플리케이션에 따라 달라질 수 있습니다. 일반적으로 데이터 가져오기를 그것이 필요한 컴퍼넌트에 넣고 그것을 Suspense로 감싸는것은 좋은 방식입니다. 그러나 필요하다면, 전체페이지나 혹은 특정 영역을 스트리밍 하는것도 나쁜 방식은 아닙니다.

Suspense와 무엇이 잘 맞을지 실험해보는걸 두려워 마세요. 이것은 매력적인 유저 경험을 만들어주는데 도움을 주는 훌륭한 API입니다. 


<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">일반적으로, Suspense와 데이터 가져오기를 사용할때 어떤게 좋은 방식일까요?</p>
    <div class="option-list">
      <div class="option" data-question-number="02">
        <span class="option__number">A</span>
        <span class="option__desc">데이터 가져오기를 그것의 부모 컴퍼넌트에 넣기</span>
      </div>
      <div class="option" data-question-number="02">
        <span class="option__number">B</span>
        <span class="option__desc">데이터 가져오는데에 Suspense사용하지 않기</span>
      </div>
      <div class="option" data-question-number="02"  data-answer="true">
        <span class="option__number">C</span>
        <span class="option__desc">데이터 가져오기를 그것이 필요한 컴퍼넌트에 넣기</span>
      </div>
      <div class="option" data-question-number="02">
        <span class="option__number">D</span>
        <span class="option__desc">에러 부분에만 Suspense 사용하기</span>
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


## 다음 챕터 예고

스트리밍과 서버 컴퍼넌트는 데이터를 가져오고 로딩 상태를 다루는데 새로운 방법들을 주었고 유저 경험을 향상 시켰습니다.

다음 챕터에서, 스트리밍을 염두하고 개발된 렌더링 모델인 부분 미리 렌더링(Partial Prerendering)을 배워볼겁니다.

<div class="finish">
  <p class="finish__title">챕터 9를 완료했습니다.</p>
  <p>Suspense와 로딩 스켈레톤을 사용한 스트림 컴퍼넌트에 대해 배웠습니다.</p>
  <div class="next-box">
    <p class="next">다음</p>    
    <p class="next__title">10: 부분 미리 렌더링(Optional)</p>
    <p>부분 미리 렌더링을 미리 한번 들여다 봅시다 - 스트리밍을 사용하도록 만들어진 실험적인 렌더링 모델입니다.</p>
    <a id="next__btn" href="https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-10-부분-미리-렌더링Optional">챕터 10 시작하기
    <svg data-testid="geist-icon" height="16" stroke-linejoin="round" viewBox="0 0 16 16" width="16" style="color: currentcolor;"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.53033 2.21968L9 1.68935L7.93934 2.75001L8.46967 3.28034L12.4393 7.25001H1.75H1V8.75001H1.75H12.4393L8.46967 12.7197L7.93934 13.25L9 14.3107L9.53033 13.7803L14.6036 8.70711C14.9941 8.31659 14.9941 7.68342 14.6036 7.2929L9.53033 2.21968Z" fill="currentColor"></path></svg>
    </a>
  </div>
</div>

## Ref
- [https://nextjs.org/learn/dashboard-app/streaming](https://nextjs.org/learn/dashboard-app/streaming)


<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/next-js-tutorial/css.css">
<script type="text/javascript" src="https://eso0117.github.io/web-practice/public/next-js-tutorial/js.js"></script>
