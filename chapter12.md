## 들어가기 전에

이 글은 [Next.js - Chapter 12 : Mutating Data
](https://nextjs.org/learn/dashboard-app/mutating-data) 강의를 번역한 글입니다.

# 데이터 변형(Mutating)

이전 챕터에서 우리는 URL 검색 파라미터와 Next.js API를 이용해 검색과 페이지네이션(Pagination) 기능을 구현했습니다. 이제 invoices 페이지에 송장을 추가,업데이트, 삭제하는 기능을 추가해봅시다.

## 이번 챕터에서...

여기 우리가 다룰 주제들이 있습니다.
- React Server Action이란 무엇이고 데이터를 변형(mutate)하는데 어떻게 사용하는지
- 폼(form)과 서버 컴퍼넌트로 작업하는 방법
- 타입 유효성 검사와 네이티브 `formData` 객체를 사용하는 좋은 방법
- `revalidatePath` API를 사용한 클라이언트 캐시 리밸리데이션(revalidate) 방법
- 특정 ID값들을 사용한 동적 라우트 경로 생성 방법

## Server Actions이란 무엇인가요?

React Server Actions은 비동기 코드를 직접 서버에서 실행할 수 있습니다. 이것들을 사용하면 데이터를 변형(mutate)하기위해 API endpoints를 생성할 필요가 없습니다. 대신 서버에서 실행하고 클라이언트 컴퍼넌트나 서버 컴퍼넌트로에서 호출될 비동기 함수를 작성합니다.

웹 어플리케이션은 다양한 위협에 취약할 수 있으므로 보안이 가장 중요한 부분입니다.
여기가 Server Actions이 필요한 부분입니다.

이것은 데이터를 지키고 승인된 접근을 확인하며 다양한 타입의 공격으로부터 보호하면서 효과적인 보안 솔루션을 제공합니다. Server Actions은 POST 요청과, 암호화된 클로져, 엄격한 입력 확인, 에러메시지 해싱, 호스트 제한 등 여러 기술을 사용하였고 이것이 한 대 어우러져 어플리케이션의 안정성을 향상시켰습니다.


## Server Actions을 적용한 폼(form) 사용하기

React에서 `<form>`의 액션을 지정하기 위해 `action` 속성을 쓸 수 있었습니다. 액션은 자동적으로 입력된 데이터를 담고 있는 네이티브 [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData) 객체를 받게 됩니다.

<div class="code-with-file">

**예를 들면**
```
// Server Component
export default function Page() {
  // Action
  async function create(formData: FormData) {
    'use server';
 
    // Logic to mutate data...
  }
 
  // Invoke the action using the "action" attribute
  return <form action={create}>...</form>;
}
```
</div>

서버 컴퍼넌트 내에 Server Action을 호출하는 장점은 [점진적 향상](https://developer.mozilla.org/ko/docs/Glossary/Progressive_Enhancement)입니다. - 폼(form)은 클라이언트에서 자바스크립트를 사용 안함으로 하여도 동작합니다.

## Server Action Next.js

Server Action은 Next.js의 [캐싱](https://nextjs.org/docs/app/building-your-application/caching)과 깊게 통합되어 있습니다. 폼(form)이 Server Action으로 전송될때, 데이터를 변경(mutate)하기위해 action을 쓸 수 있는것 뿐 아니라, `revalidatePath`와 `revalidateTag` 같은 API들을 통해 관련된 캐시를 revalidate 할 수 있습니다.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">Server Action을 사용하는 하나의 장점은 무엇인가요?</p>
    <div class="option-list">
      <div class="option" data-question-number="01">
        <span class="option__number">A</span>
        <span class="option__desc">SEO 향상</span>
      </div>
      <div class="option" data-question-number="01" data-answer="true">
        <span class="option__number">B</span>
        <span class="option__desc">점진적 향상</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">C</span>
        <span class="option__desc">웹 사이트를 빠르게 해줌</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">D</span>
        <span class="option__desc">데이터 암호화</span>
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
이제 이것이 어떻게 동작하는지 봅시다.


## 송장 생성하기

여기 새로운 송장을 만들기 위해 우리가 진행할 과정이 있습니다.

1. 유저의 입력을 받을 폼(form) 생성하기
2. 새로운 Server Action 생성하고 폼에서 호출하기
3. Server Action내에서 `formData` 객체로부터 데이터 가져오기
4. 데이터 유효성 검사하고 데이터베이스에 넣을 준비하기
5. 데이터를 넣고 에러를 처리하기
6. 캐시 revalidate하고 유저를 invoices 페이지로 리다이렉트 시키기

### 1. 새로운 라우트와 폼(form) 생성

`invoices` 폴더 내에, `/create` 인 새로운 라우트 경로를 생성하고 `page.tsx` 파일을 만드세요.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fcreate-invoice-route.png&w=1920&q=75" alt="invoices 내에 create 폴더와 page.tsx 생성">

이제 이 라우트는 새로운 송장을 만드는데 사용할 겁니다. `page.tsx` 안에, 다음의 코드를 붙여놓고 어떤 코드인지 한번 들여다 봅시다.

<div class="code-with-file">

**/dashboard/invoices/create/page.tsx**

```
import Form from '@/app/ui/invoices/create-form';
import Breadcrumbs from '@/app/ui/invoices/breadcrumbs';
import { fetchCustomers } from '@/app/lib/data';
 
export default async function Page() {
  const customers = await fetchCustomers();
 
  return (
    <main>
      <Breadcrumbs
        breadcrumbs={[
          { label: 'Invoices', href: '/dashboard/invoices' },
          {
            label: 'Create Invoice',
            href: '/dashboard/invoices/create',
            active: true,
          },
        ]}
      />
      <Form customers={customers} />
    </main>
  );
}
```
</div>

이 페이지는 `customers`를 가져오고 `<Form>` 컴퍼넌트에 전달하는 서버 컴퍼넌트 입니다. 시간 절약을 위해, `<Form>` 컴퍼넌트를 미리 만들어 놨습니다.

`<Form>`컴퍼넌트로 이동하면, 다음과 같은 폼을 볼겁니다.

- 고객 리스트를 위한 `<select>`(드롭다운) 요소를 1개 갖고 있습니다.
- 총액을 넣을 `<input>` 요소를 1개 갖고. `type="number"`입니다.
- 상태를 위한 `type="radio"` `<input>`이 2개 있습니다.
- `type="submit"`의 버튼 하나가 있습니다.

이제 [http://localhost:3000/dashboard/invoices/create](http://localhost:3000/dashboard/invoices/create)로 가면, 다음과 같은 UI를 볼 수 있습니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fcreate-invoice-page.png&w=1080&q=75" alt="/invoices/create UI">

### Server Action 생성하기

훌륭합니다. 이제 폼(form)이 제출될때 불려질 Server Action을 생성해 봅시다.

`lib` 디렉토리로 이동해서 `action.ts`라는 이름의 새로운 파일을 생성합시다. 파일 상단에 React [use server](https://react.dev/reference/react/use-server) 지시자를 추가합니다.

<div class="code-with-file">

**/app/lib/actions.ts**
```
'use server';
```
</div>

`'use server'`를 추가함으로서, 파일 내에 모든 내보내지는 함수들은(exported function) 서버 함수라고 표시를 합니다. 이 서버 함수들은 클라이언트와 서버 컴퍼넌트 모두에서 불러(import)올 수 있으므로, 다양하게 사용할 수 있습니다.

그리고 또한 "use server"를 액션 내에 추가함으로서 Server Action을 서버 컴퍼넌트 내에 바로 쓸 수 있습니다. 하지만 이 강의에서는 별도의 파일로 정리해서 갖고 있겠습니다.

이제 `action.ts` 파일 내에, `formData`를 받는 비 동기 함수를 생성하세요.

<div class="code-with-file">

**/app/lib/actions.ts**

```
'use server';
 
export async function createInvoice(formData: FormData) {}
```
</div>

그런 다음, `actions.ts` 파일 내에 `createInvoice`를 `<Form>` 컴퍼넌트에서 불러(import)옵시다. `<form>`요소에 `action` 속성을 추가하세요, 그리고 `createInvoice` 액션을 넣으세요.

<div class="code-with-file">

**/app/ui/invoices/create-form.tsx**
```
import { customerField } from '@/app/lib/definitions';
import Link from 'next/link';
import {
  CheckIcon,
  ClockIcon,
  CurrencyDollarIcon,
  UserCircleIcon,
} from '@heroicons/react/24/outline';
import { Button } from '@/app/ui/button';
import { createInvoice } from '@/app/lib/actions';
 
export default function Form({
  customers,
}: {
  customers: customerField[];
}) {
  return (
    <form action={createInvoice}>
      // ...
  )
}
```
</div>

<div class="hint">

**알면 좋은 것**

HTML에서 `action`에 URL을 넣곤 했을겁니다. 이 URL은 폼(form) 데이터가 제출 될 목적지가 됩니다.(보통은 API의 엔드포인트)

그러나 React에서 `action` 요소는 특별한 prop으로 간주됩니다. React가 그 위에서 액션을 호출 할 수 있도록 빌드된다는 의미입니다.

화면 뒤에서, Server Actions은 POST API endpoint를 생성합니다. 이것이 우리가 API endpoint를 따로 생성할 필요 없었던 이유입니다.

</div>

### 3. `formData` 으로부터 데이터 추출하기

`actions.ts`로 돌아가서, `formData`의 값을 추출할 필요가 있습니다. [수많은 방법](https://developer.mozilla.org/en-US/docs/Web/API/FormData/append)이 있는데, 이 강의에서는 [`.get(name)`](https://developer.mozilla.org/en-US/docs/Web/API/FormData/get) 메소드를 쓰겠습니다.

<div class="code-with-file">

**/app/lib/actions.ts**
```
'use server';
 
export async function createInvoice(formData: FormData) {
  const rawFormData = {
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  };
  // Test it out:
  console.log(rawFormData);
}
```
</div>

<div class="hint">

**Tip:**
만약 많은 필드를 가진 폼(form)으로 작업을 한다면, 자바스크립트 [`Object.fromEntries()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/fromEntries)와 [`entries()`](https://developer.mozilla.org/en-US/docs/Web/API/FormData/entries)의 사용을 고려해보세요. 

예를 들어:

```const rawFormData = Object.fromEntries(formData.entries())```

</div>

모든게 다 잘 되었는지 확인하기 위해, 먼저 폼(form)을 입력해봅시다. 제출 버튼을 누른 후에 우리가 폼에 누른 입력을 터미널의 콘솔에서 볼 수 있을겁니다.

이제 우리 데이터가 객체의 모양을 갖췄으니, 작업이 훨씬 쉬워질 겁니다.

### 4. 유효성 검사와 데이터 준비하기

폼(form) 데이터를 데이터 베이스에 전송하기 전에, 정확한 타입을 가진 형태의 데이터인지 확인하고 싶을겁니다. 강의 앞 부분에서, invoices 테이블은 다음과 같은 형태를 가진다고 정의 했었습니다.

<div class="code-with-file">

**/app/lib/definitions.ts**
```
export type Invoice = {
  id: string; // Will be created on the database
  customer_id: string;
  amount: number; // Stored in cents
  status: 'pending' | 'paid';
  date: string;
};
```
</div>

아직은, 폼(form) 데이터에서 `customer_id`, `amount`, `status`만 가지고 있습니다.

#### 유효성 검사와 타입 변환(coercion)

폼(form)의 데이터가 데이터 베이스 타입과 일치하여 유효한지 검사하는것은 중요합니다. 예를 들어, 액션 내에 `console.log`를 다음과 같이 추가한다면,

```
console.log(typeof rawFormData.amount);
```

`amount`가 `number`가 아닌 `string` 타입이란걸 알 수 있습니다. `type="number`를 갖고 있는 `input`은 실제로 숫자가 아닌 문자열을 반환하기 때문이죠!

타입 유효성 검사를 위해 몇가지 옵션이 있습니다. 일일이 유효성 검사를 하는 방법도 있고, 시간과 노력을 줄이기 위해 유효성 검사 라이브러리를 사용할 수도 있습니다. 이번 예제에서 우리는 유효성 검사를 간단히 할 수 있는 타입스크립트의 라이브러리 [Zod](https://zod.dev/)를 사용할 겁니다.

`action.ts`에 Zod를 불러(import)오고, 폼(form) 오브젝트의 형태와 일치하는 스키마를 정의합시다. 이 스키마는 `formData`를 데이터 베이스에 저장하기 전에 유효성 검사를 할 겁니다.


<div class="code-with-file">

**/app/lib/actions.ts**

```
'use server';
 
import { z } from 'zod';
 
const FormSchema = z.object({
  id: z.string(),
  customerId: z.string(),
  amount: z.coerce.number(),
  status: z.enum(['pending', 'paid']),
  date: z.string(),
});
 
const CreateInvoice = FormSchema.omit({ id: true, date: true });
 
export async function createInvoice(formData: FormData) {
  // ...
}
```
</div>

`amount` 필드는 특히 그것의 타입 유효성 검사하는 동안 문자열에서 숫자로 coerce(타입변환)을 설정했습니다.

이제 타입 검사를 위해 `rawFormData`를 `CreateInvoice`로 넘길 수 있습니다.

<div class="code-with-file">

**/app/lib/actions.ts**
```
// ...
export async function createInvoice(formData: FormData) {
  const { customerId, amount, status } = CreateInvoice.parse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });
}
```
</div>

#### 값을 cents로 저장하기

통화 값을 cents로 데이터베이스에 저장하는 것은 높은 정확성과 자바스크립트의 부동소수점 오차(floating point) 에러를 없애주는 좋은 방식 입니다.

`amount` 데이터를 cents로 변환해봅시다.

<div class="code-with-file">

**/app/lib/actions.ts**
```
// ...
export async function createInvoice(formData: FormData) {
  const { customerId, amount, status } = CreateInvoice.parse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });
  const amountInCents = amount * 100;
}
```
</div>

#### 새로운 날짜 만들기

이제 송장 생성 날짜 명시를 위한 "YYYY-MM-DD" 형식을 가진 날짜 정보를 만들어 봅시다.

<div class="code-with-file">


**/app/lib/actions.ts**
```
// ...
export async function createInvoice(formData: FormData) {
  const { customerId, amount, status } = CreateInvoice.parse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });
  const amountInCents = amount * 100;
  const date = new Date().toISOString().split('T')[0];
}
```
</div>

### 5. 데이터베이스에 데이터 넣기

이제 데이터베이스에 필요한 모든 값들을 가지고 있으니, 새로운 송장을 데이터베이스에 넣고 변수를 전달하는 SQL 쿼리를 만들 수 있습니다.

<div class="code-with-file">

**/app/lib/actions.ts**

```
import { z } from 'zod';
import { sql } from '@vercel/postgres';
 
// ...
 
export async function createInvoice(formData: FormData) {
  const { customerId, amount, status } = CreateInvoice.parse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });
  const amountInCents = amount * 100;
  const date = new Date().toISOString().split('T')[0];
 
  await sql`
    INSERT INTO invoices (customer_id, amount, status, date)
    VALUES (${customerId}, ${amountInCents}, ${status}, ${date})
  `;
}
```
</div>

아직 우리는 어떤 에러도 다루고 있지 않습니다. 그것은 다음 챕터에서 다룰 예정이고, 지금은 다음 과정으로 넘어갑시다.

### 6. Revalidate 그리고 Redirect

Next.js는 유저의 브라우저 내에 라우트 경로를 저장하는 [클라이언트 사이드 라우터 캐시](https://nextjs.org/docs/app/building-your-application/caching#router-cache)를 가지고 있습니다. [미리 가져오기](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#1-prefetching)와 이것은 라우트 간의 이동에서 서버에게 가는 요청 수를 줄이며 빠르게 유저가 페이지를 이동할 수 있게 해줍니다.

invoices 라우터에서 표시되는 데이터를 업데이트 하고 있기때문에, 이 캐시를 클리어하고, 서버에 새로운 요청을 보낼 필요가 있습니다. 우리는 이것을 Next.js의 `revalidatePath` 함수로 할 수 있습니다.

<div class="">

**/app/lib/actions.ts**

```
'use server';
 
import { z } from 'zod';
import { sql } from '@vercel/postgres';
import { revalidatePath } from 'next/cache';
 
// ...
 
export async function createInvoice(formData: FormData) {
  const { customerId, amount, status } = CreateInvoice.parse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });
  const amountInCents = amount * 100;
  const date = new Date().toISOString().split('T')[0];
 
  await sql`
    INSERT INTO invoices (customer_id, amount, status, date)
    VALUES (${customerId}, ${amountInCents}, ${status}, ${date})
  `;
 
  revalidatePath('/dashboard/invoices');
}
```
</div>

데이터베이스가 업데이트 되면, `/dashboard/invoices` 경로는 revalidate 되고 새로운 데이터를 가져올 겁니다.

이 지점에서 우리는 유저가 `/dashboard/invoices` 경로로 리다이렉트 되길 원합니다. 이것은 Next.js의 [`redirect`](https://nextjs.org/docs/app/api-reference/functions/redirect) 함수로 가능합니다.


<div class="code-with-file">

**/app/lib/actions.ts**
```
'use server';
 
import { z } from 'zod';
import { sql } from '@vercel/postgres';
import { revalidatePath } from 'next/cache';
import { redirect } from 'next/navigation';
 
// ...
 
export async function createInvoice(formData: FormData) {
  // ...
 
  revalidatePath('/dashboard/invoices');
  redirect('/dashboard/invoices');
}
```
</div>

축하합니다! 우리는 처음으로 Server Action을 구현했습니다. 새로운 송장을 추가하면서 모든게 정상적으로 동작하는지 확인해보세요.

1. 제출 한 후 `/dashboard/invoices`로 리다이렉트 되야합니다.
2. 테이블의 최상단에 새롭게 생성된 송장을 볼 수 있어야 합니다.

## 송장 업데이트

송장을 업데이트 하는 양식과 생성하는 양식은 데이터베이스에서 업데이트 되어야할 송장의 `id`를 넘겨야하는것을 제외하고는 비슷합니다. 어떻게 `id`를 얻어서 보내는지 한번 봅시다.

여기 새로운 송장을 만들기 위해 우리가 진행할 과정이 있습니다.

1. 송장의 id를 갖고 있는 새로운 동적 라우트 경로를 생성합니다.
2. 해당 페이지의 파라미터로부터 송장의 `id`를 읽습니다.
3. 데이터베이스로부터 해당 송장을 가져옵니다.
4. 송장의 데이터를 폼(form) 안에 미리 채워 놓습니다.
5. 데이터베이스 내에 송장 데이터를 업데이트 합니다.

### 1. 송장의 `id`를 갖는 동적 라우트 경로 생성

Next.js에서는 데이터 기반으로 경로를 생성하고 싶거나 정확한 경로를 지정할 수 없을때 [동적 라우트 경로](https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes)를 생성할 수 있습니다. 이것은 블로그 글의 제목, 상품 페이지 기타 등등이 될 수 있습니다. `[id]`, `[post]` 또는 `[slug]` 같이 대괄호로 지어진 폴더 명으로 동적 라우트 경로를 만들 수 있습니다.

`/invoices` 폴더에서, `[id]` 란 이름의 동적 라우트를 생성합시다. 그리고 `edit`이란 이름의 새로운 라우트와 `page.tsx`를 생성합니다. 파일 구조는 아래와 같을 겁니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fedit-invoice-route.png&w=1920&q=75" alt="송장 수정하기 파일 구조">

<Table> 컴퍼넌트 내에, 테이블의 레코드로 부터 송장의 id를 받는 `<updateInvoice />` 버튼을 보세요.

<div class="code-with-file">

**/app/ui/invoices/table.tsx**
```
export default async function InvoicesTable({
  query,
  currentPage,
}: {
  query: string;
  currentPage: number;
}) {
  return (
    // ...
    <td className="flex justify-end gap-2 whitespace-nowrap px-6 py-4 text-sm">
      <UpdateInvoice id={invoice.id} />
      <DeleteInvoice id={invoice.id} />
    </td>
    // ...
  );
}
```
</div>

`<UpdateInvoice />` 컴퍼넌트로 이동해서 `Link`의 `href`를 `id` prop를 받도록 업데이트 하세요. 동적 라우트 경로를 위해 템플릿 리터럴을 사용할 수 있습니다.

<div class="code-with-file">

**/app/ui/invoices/buttons.tsx**
```
import { PencilIcon, PlusIcon, TrashIcon } from '@heroicons/react/24/outline';
import Link from 'next/link';
 
// ...
 
export function UpdateInvoice({ id }: { id: string }) {
  return (
    <Link
      href={`/dashboard/invoices/${id}/edit`}
      className="rounded-md border p-2 hover:bg-gray-100"
    >
      <PencilIcon className="w-5" />
    </Link>
  );
}
```
</div>

### 2. 페이지의 `params`에서 송장의 `id` 읽기

`<Page>` 컴퍼넌트로 돌아가서, 아래의 코드를 붙여 놓읍시다.

<div class="code-with-file">

**/app/dashboard/invoices/[id]/edit/page.tsx**
```
import Form from '@/app/ui/invoices/edit-form';
import Breadcrumbs from '@/app/ui/invoices/breadcrumbs';
import { fetchCustomers } from '@/app/lib/data';
 
export default async function Page() {
  return (
    <main>
      <Breadcrumbs
        breadcrumbs={[
          { label: 'Invoices', href: '/dashboard/invoices' },
          {
            label: 'Edit Invoice',
            href: `/dashboard/invoices/${id}/edit`,
            active: true,
          },
        ]}
      />
      <Form invoice={invoice} customers={customers} />
    </main>
  );
}
```
</div>

이 파일이 다른 폼(`edit-form.tsx` 파일에서 온)을 불러(import)오는걸 제외하고 송장의 `/create` 페이지와 얼마나 유사한지 주목하세요. 이 폼(form)은 고객 이름, 송장의 금액, 상태 값들을 미리 채워야 합니다. 폼 필드에 값을 미리 채워놓기 위해 `id`를 이용해 특정한 송장을 가져와야 합니다.

`searchParam`외에도 추가적으로 페이지 컴퍼넌트는 `param`이란 이름의 prop을 받습니다. 이것을 이용해 `id`를가져올 수 있습니다. `<Page>` 컴퍼넌트를 해당 prop을 받을 수 있게 업데이트 합시다.

<div class="code-with-file">

**/app/dashboard/invoices/[id]/edit/page.tsx**
```
import Form from '@/app/ui/invoices/edit-form';
import Breadcrumbs from '@/app/ui/invoices/breadcrumbs';
import { fetchCustomers } from '@/app/lib/data';
 
export default async function Page({ params }: { params: { id: string } }) {
  const id = params.id;
  // ...
}
```
</div>

### 3. 특정한 송장 가져오기

그리고나서

- `fetchInvoiceById`란 이름의 새로운 함수를 불러(import)오고, id를 함수 인자로 넘기세요.
- `fetchCustomers`를 불러(import)오고 드랍다운 메뉴에 고객 이름을 표시합니다.

`Promise.all`을 사용해서 송장과 고객들 정보를 병렬로 가져올 수 있습니다.

<div class="code-with-file">

**/dashboard/invoices/[id]/edit/page.tsx**
```
import Form from '@/app/ui/invoices/edit-form';
import Breadcrumbs from '@/app/ui/invoices/breadcrumbs';
import { fetchInvoiceById, fetchCustomers } from '@/app/lib/data';
 
export default async function Page({ params }: { params: { id: string } }) {
  const id = params.id;
  const [invoice, customers] = await Promise.all([
    fetchInvoiceById(id),
    fetchCustomers(),
  ]);
  // ...
}
```
</div>

`invoice`는 잠재적으로 `undefined`이므로 일시적으로 터미널에서 `invoice` prop에 TS 에러가 나는 걸 볼 겁니다. 지금은 걱정하지 마세요, 우리는 다음 챕터에서 이것을 해결할 겁니다.

훌륭합니다. 이제 잘 동작하는지 확인해봅시다.
[http://localhost:3000/dashboard/invoices](http://localhost:3000/dashboard/invoices)를 방문해서 연필 아이콘을 눌러 송장을 수정해봅시다.
페이지 이동 후에, 송장의 상세 정보가 미리 입력된 양식을 볼 수 있습니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fedit-invoice-page.png&w=1080&q=75" alt="송장 수정 화면">

URL도 다음과 같이 `id`로 업데이트 되어야 합니다.

`http://localhost:3000/dashboard/invoice/uuid/edit`

<div class="hint">

**UUIDs vs. Auto-incrementing Keys**

우리는 증가값(1, 2, 3... etc) 을 키로 갖는것 대신 UUID를 사용합니다. 이것은 URL을 더 길게 만들지만, 전역적으로 유니크하여 ID충돌의 위험을 없앱니다, 그리고
열거 공격의 위험을 줄여 대규모 데이터베이스에 이상적입니다.

하지만, 만약 더 깔끔한 URL형태를 좋아한다면, 자동으로 증가하는 key 형태를 선호할 겁니다.

</div>


### `id`를 Server Action에 보내기

이제 `id`를 Server Action으로 보내서 옳은 기록을 업데이트 할 수 있게 만들고 싶습니다. 하지만 아래와 같이 `id`는 함수 인자로 보내질 수 없습니다.

<div class="code-with-file">

**/app/ui/invoices/edit-form.tsx**

```
// Passing an id as argument won't work
<form action={updateInvoice(id)}>
```
</div>

대신, JS `bind`를 이용해서 `id`를 Server Action으로 보낼 수 있습니다. 이렇게 하면 Server Action으로 보내지는 모든 값이 인코딩 됩니다.


<div class="code-with-file">

**/app/ui/invoices/edit-form.tsx**
```
// ...
import { updateInvoice } from '@/app/lib/actions';
 
export default function EditInvoiceForm({
  invoice,
  customers,
}: {
  invoice: InvoiceForm;
  customers: CustomerField[];
}) {
  const updateInvoiceWithId = updateInvoice.bind(null, invoice.id);
 
  return (
    <form action={updateInvoiceWithId}>
      <input type="hidden" name="id" value={invoice.id} />
    </form>
  );
}
```
</div>

<div class="hint">

**Note:** 폼(form) 안에 hidden input 필드를 사용해도 동작은 합니다(`<input type="hidden" name="id" value={invoice.id} />`). 

하지만, HTML 소스에서 값이 완전히 노출되고 이것은 IDs같은 민감한 데이터에는 이상적인 방식이 아닙니다.

</div>
<br>

이제 `actions.ts` 파일에서 `updateInvoice` 라는 새로운 action을 생성합시다.

<div class="code-with-file">

**/app/lib/actions.ts**
```
// Use Zod to update the expected types
const UpdateInvoice = FormSchema.omit({ id: true, date: true });
 
// ...
 
export async function updateInvoice(id: string, formData: FormData) {
  const { customerId, amount, status } = UpdateInvoice.parse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });
 
  const amountInCents = amount * 100;
 
  await sql`
    UPDATE invoices
    SET customer_id = ${customerId}, amount = ${amountInCents}, status = ${status}
    WHERE id = ${id}
  `;
 
  revalidatePath('/dashboard/invoices');
  redirect('/dashboard/invoices');
}
```
</div>

`createInvoice` action과 비슷하게,

1. `formData`로 부터 데이터를 추출합니다.
2. Zod를 이용해 타입 유효성 검사를 합니다.
3. 금액을 cents로 바꿉니다.
4. 변수를 SQL 쿼리에 전달합니다.
5. 클라이언트 캐시를 삭제하고, 새로운 서버 요청을 위해 `revalidatePath`를 호출합니다.
6. 유저를 송장 페이지로 리다이렉트 시키기 위해 `redirect`를 호출합니다.

## 송장 지우기

Server Action을 이용해서 송장을 지우기 위해, 삭제 버튼을 `<form>` 요소로 감싸고 `bind`를 사용해 `id`를 Server Action으로 보내세요.

<div class="code-with-file">

**/app/ui/invoices/buttons.tsx**
```
import { deleteInvoice } from '@/app/lib/actions';
 
// ...
 
export function DeleteInvoice({ id }: { id: string }) {
  const deleteInvoiceWithId = deleteInvoice.bind(null, id);
 
  return (
    <form action={deleteInvoiceWithId}>
      <button className="rounded-md border p-2 hover:bg-gray-100">
        <span className="sr-only">Delete</span>
        <TrashIcon className="w-4" />
      </button>
    </form>
  );
}
```
</div>

`action.ts` 파일 내에, `deleteInvoice`라는 새로운 action을 생성합니다.

<div class="code-with-file">

**/app/lib/actions.ts**
```
export async function deleteInvoice(id: string) {
  await sql`DELETE FROM invoices WHERE id = ${id}`;
  revalidatePath('/dashboard/invoices');
}
```
</div>

이 action은 `/dashboard/invoices` 경로에서 호출되기 떄문에, `redirect`를 호출할 필요 없습니다. `revalidatePath` 호출은 새로운 서버요청과 테이블을 다시 렌더링 하라는 트리거 입니다.

## 더 읽을거리

이 챕터에서, Server Action을 사용해 데이터를 변형(mutate)하는 방법을 배웠습니다. 또한 `revalidatePath` API를 호출해 Next.js 캐시를 revalidate하고 `redirect`로 유저를 새로운 페이지로 리다이렉트 하는걸 배웠습니다.

또한 [Server Action과 보안](https://nextjs.org/blog/security-nextjs-server-components-actions)을 읽으면서 추가적으로 더 배울 수 있습니다.


<div class="finish">
  <p class="finish__title">챕터 12를 완료했습니다.</p>
  <p>축하합니다! form과 React Server Action으로 데이터를 변형(mutate)하는 법을 배웠습니다.</p>
  <div class="next-box">
    <p class="next">다음</p>    
    <p class="next__title">13. 에러 핸들링</p>
    <p>오류 처리 및 접근성을 포함해서 폼(form)으로 데이터를 변형(mutate)하는 좋은 방법을 알아봅시다.</p>
    <a id="next__btn" href="https://nextjs.org/learn/dashboard-app/error-handling">챕터 13 시작하기
    <svg data-testid="geist-icon" height="16" stroke-linejoin="round" viewBox="0 0 16 16" width="16" style="color: currentcolor;"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.53033 2.21968L9 1.68935L7.93934 2.75001L8.46967 3.28034L12.4393 7.25001H1.75H1V8.75001H1.75H12.4393L8.46967 12.7197L7.93934 13.25L9 14.3107L9.53033 13.7803L14.6036 8.70711C14.9941 8.31659 14.9941 7.68342 14.6036 7.2929L9.53033 2.21968Z" fill="currentColor"></path></svg>
    </a>
  </div>
</div>

## Ref
- [https://nextjs.org/learn/dashboard-app/mutating-data#4-pass-the-id-to-the-server-action](https://nextjs.org/learn/dashboard-app/mutating-data#4-pass-the-id-to-the-server-action)


<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/next-js-tutorial/css.css">
<script type="text/javascript" src="https://eso0117.github.io/web-practice/public/next-js-tutorial/js.js"></script>
