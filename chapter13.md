## 들어가기 전에

이 글은 [Next.js - Chapter 13 : Handling Errors
](https://nextjs.org/learn/dashboard-app/error-handling) 강의를 번역한 글입니다.

# 에러 핸들링

이전 챕터에서, Server Action을 이용한 데이터 변형 방법을 배웠습니다. 이제 자바스크립트 `try/catch`문과 Next.js API를 이용해서 *우아하게(gracefully)* 에러를 처리하는법을 배웁시다.

## 이번 챕터에서...

여기 우리가 다룰 주제들이 있습니다.
- `error.tsx` 파일을 사용하여 라우트 경로의 에러를 잡는 방법과 유저에게 대체 UI를 보여주는 방법
- 404 에러(경로를 찾을 수 없습니다.)를 처리하기 위한 `notFound` 함수와 `not-found` 파일을 사용하는 방법

## Server Action에 `try/catch` 추가하기

우선, 우리가 만든 Server Action에 자바스크립트의 `try/catch` 구문을 추가해서 에러를 다뤄봅시다.

이미 어떻게 할 지 알 것 같으면, 시간을 조금 들여 Server Actions를 업데이트 해보세요, 아니면 아래의 코드를 복사해도 좋습니다.

<button class="show-and-hide-btn--hidden show-and-hide-btn btn" data-target="practice-01">
  <span data-btn-content>정답 보이기</span>
</button>

<div class="is-inactive" data-hide-target="practice-01">
  <div class="code-with-file">

**/app/lib/actions.ts**
```
export async function createInvoice(formData: FormData) {
  const { customerId, amount, status } = CreateInvoice.parse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });
 
  const amountInCents = amount * 100;
  const date = new Date().toISOString().split('T')[0];
 
  try {
    await sql`
      INSERT INTO invoices (customer_id, amount, status, date)
      VALUES (${customerId}, ${amountInCents}, ${status}, ${date})
    `;
  } catch (error) {
    return {
      message: 'Database Error: Failed to Create Invoice.',
    };
  }
 
  revalidatePath('/dashboard/invoices');
  redirect('/dashboard/invoices');
}
```
  </div>
</div>

<br>

<button class="show-and-hide-btn--hidden show-and-hide-btn btn" data-target="practice-02">
  <span data-btn-content>정답 보이기</span>
</button>

<div class="is-inactive" data-hide-target="practice-02">
  <div class="code-with-file">

**/app/lib/actions.ts**
```
export async function updateInvoice(id: string, formData: FormData) {
  const { customerId, amount, status } = UpdateInvoice.parse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });
 
  const amountInCents = amount * 100;
 
  try {
    await sql`
        UPDATE invoices
        SET customer_id = ${customerId}, amount = ${amountInCents}, status = ${status}
        WHERE id = ${id}
      `;
  } catch (error) {
    return { message: 'Database Error: Failed to Update Invoice.' };
  }
 
  revalidatePath('/dashboard/invoices');
  redirect('/dashboard/invoices');
}
```
  </div>
</div>

<br>

<button class="show-and-hide-btn--hidden show-and-hide-btn btn" data-target="practice-03">
  <span data-btn-content>정답 보이기</span>
</button>

<div class="is-inactive" data-hide-target="practice-03">
  <div class="code-with-file">

**/app/lib/actions.ts**
```
export async function deleteInvoice(id: string) {
  try {
    await sql`DELETE FROM invoices WHERE id = ${id}`;
    revalidatePath('/dashboard/invoices');
    return { message: 'Deleted Invoice.' };
  } catch (error) {
    return { message: 'Database Error: Failed to Delete Invoice.' };
  }
}
```
  </div>
</div>

try/catch 블록 바깥에서 `redirect`가 어떻게 불리는지 확인해보세요. error를 던지고 나서 리다이렉트가 동작하는걸 피하기 위해 우리는 `redirect`을 `try/catch` **후에** 호출했습니다. `redirect`는 오직 `try`가 성공한 후에 불립니다.

이제 Server Action에서 에러가 발생할 때 무엇이 일어나는지 확인해볼겁니다. 
`deleteInvoice` 액션 내 함수 최상단에 에러를 던져봅시다.

<div class="code-with-file">

**/app/lib/actions.ts**
```
export async function deleteInvoice(id: string) {
  throw new Error('Failed to Delete Invoice');
 
  // Unreachable code block
  try {
    await sql`DELETE FROM invoices WHERE id = ${id}`;
    revalidatePath('/dashboard/invoices');
    return { message: 'Deleted Invoice' };
  } catch (error) {
    return { message: 'Database Error: Failed to Delete Invoice' };
  }
}
```
</div>

송장을 지우려고 시도할때, 로컬호스트에서 에러를 볼 수 있습니다.

개발하는 동안 이러한 에러를 보는 것은 잠재적인 문제를 미리 볼 수 있으므로 도움이 됩니다. 하지만 우린 실제 유저에게는 갑작스러운 실패 로그보다는 어플리케이션이 계속 동작하는 모습을 보여주고 싶을겁니다.

여기서 Next.js의 [`error.tsx`](https://nextjs.org/docs/app/api-reference/file-conventions/error)가 필요한 부분입니다.

## `error.tsx`로 에러 다루기

`error.tsx` 파일은 라우트 경로에 UI 바운더리를 정의하기 위해 쓰일 수 있습니다. 이것은 예상하지 못한 에러를 위한 **광범위한 에러 페이지로서(catch-all)** 동작하며 대체 UI를 보여줄 수 있습니다.

`/dashboard/invoice` 폴더 내에, `error.tsx` 란 이름의 새로운 파일을 생성하고 다음의 코드를 붙여넣으세요.

<div class="code-with-file">

**/dashboard/invoices/error.tsx**
```
'use client';
 
import { useEffect } from 'react';
 
export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  useEffect(() => {
    // Optionally log the error to an error reporting service
    console.error(error);
  }, [error]);
 
  return (
    <main className="flex h-full flex-col items-center justify-center">
      <h2 className="text-center">Something went wrong!</h2>
      <button
        className="mt-4 rounded-md bg-blue-500 px-4 py-2 text-sm text-white transition-colors hover:bg-blue-400"
        onClick={
          // Attempt to recover by trying to re-render the invoices route
          () => reset()
        }
      >
        Try again
      </button>
    </main>
  );
}
```
</div>

위의 코드에서 주목할게 몇가지 있습니다.
`
- **"use client"** - `error.tsx`는 클라이언트 컴퍼넌트 입니다.
- 2가지 props를 받습니다
  - `error`: 자바스크립트의 네이비트 [`Error`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error) 오브젝트 입니다.
  - `reset`: 이 함수는 에러 바운더리를 리셋하는데 쓰입니다. 실행이되면, 함수는 해당 결로를 다시 렌더링 합니다.

송장을 지우려고 다시 시도해보면, 다음과 같은 UI를 볼 수 있습니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Ferror-page.png&w=1920&q=75" alt="Next.js error.tsx 송장 지우기">

## `notFound` 함수를 이용한 404 에러 핸들링

에러를 우아하게(gracefully) 다룰 다른 한가지 방법은 `notFound` 함수를 쓰는 겁니다. `error.tsx`가 **모든** 에러를 잡는다면, `notFound`는 존재하지 않는 리소스를 가져오려 할때 사용할 수 있습니다.

예를들어 <http://localhost:3000/dashboard/invoices/2e94d1ed-d220-449f-9f11-f0bbceed9645/edit> 를 방문해보세요.

이것은 가짜 UUID로, 데이터베이스내에 존재하지 않습니다.

`error.tsx`를 볼 겁니다. 왜냐하면 `error.tsx`가 정의된 `/invoices`의 하위 경로이기 때문이죠.

만약 더 구체적인 오류 표시가 되길 원한다면, 404 에러를 보여줘서 유저에게 지금 접근하려는 경로가 존재하지 않는다고 알려줄 수 있습니다.

`data.ts`파일 내에 `fetchInvoiceById` 함수로 가서 데이터가 존재하지 않는지 확인할 수 있고 `invoice`를 리턴하는 로그도 추가할 수 있습니다.

<div class="code-with-file">

**/app/lib/data.ts**
```
export async function fetchInvoiceById(id: string) {
  noStore();
  try {
    // ...
 
    console.log(invoice); // Invoice is an empty array []
    return invoice[0];
  } catch (error) {
    console.error('Database Error:', error);
    throw new Error('Failed to fetch invoice.');
  }
}
```
</div>

이제 데이터베이스 내에 송장이 존재하지 않는걸 알았으니, `notFound`를 사용해서 에러를 처리합시다. `/dashboard/invoices/[id]/edit/page.tsx`로 이동해서, `'next/navigation'`에서 `notFound`를 불러(import)오세요.

<div class="code-with-file">

**/dashboard/invoices/[id]/edit/page.tsx**

```
import { fetchInvoiceById, fetchCustomers } from '@/app/lib/data';
import { updateInvoice } from '@/app/lib/actions';
import { notFound } from 'next/navigation';
 
export default async function Page({ params }: { params: { id: string } }) {
  const id = params.id;
  const [invoice, customers] = await Promise.all([
    fetchInvoiceById(id),
    fetchCustomers(),
  ]);
 
  if (!invoice) {
    notFound();
  }
 
  // ...
}
```
</div>

완벽합니다! 이제 송장을 찾을 수 없다면 `<Page>` 는 에러를 던질 겁니다. 유저에게 에러 UI를 보여주기 위해 `not-found.tsx` 파일을 `/edit`폴더 내에 생성합시다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fnot-found-file.png&w=3840&q=75" alt="not-found.tsx 파일 경로">

그리고나서, `not-found.tsx` 파일 내에, 다음의 코드를 붙여넣으세요.

<div class="code-with-file">

**/dashboard/invoices/[id]/edit/not-found.tsx**
```
import Link from 'next/link';
import { FaceFrownIcon } from '@heroicons/react/24/outline';
 
export default function NotFound() {
  return (
    <main className="flex h-full flex-col items-center justify-center gap-2">
      <FaceFrownIcon className="w-10 text-gray-400" />
      <h2 className="text-xl font-semibold">404 Not Found</h2>
      <p>Could not find the requested invoice.</p>
      <Link
        href="/dashboard/invoices"
        className="mt-4 rounded-md bg-blue-500 px-4 py-2 text-sm text-white transition-colors hover:bg-blue-400"
      >
        Go Back
      </Link>
    </main>
  );
}
```
</div>

이제 해당 페이지를 새로고침하세요. 다음과 같은 UI를 볼 수 있습니다.

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2F404-not-found-page.png&w=1920&q=75" alt="404 Not Found UI">

`notFound`는 `error.tsx`보다 우선 적용 된다는걸 명심하세요 그래서 더 구체적인 오류를 처리하고 싶을때 이것에 접근할 수 있습니다.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">라우트 경로에서 예상치 못한 에러를 전반적으로 다루는 Next.js의 파일은 무엇일까요?</p>
    <div class="option-list">
      <div class="option" data-question-number="01">
        <span class="option__number">A</span>
        <span class="option__desc">404.tsx</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">B</span>
        <span class="option__desc">not-found.tsx</span>
      </div>
      <div class="option" data-question-number="01" data-answer="true">
        <span class="option__number">C</span>
        <span class="option__desc">error.tsx</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">D</span>
        <span class="option__desc">catch-all.tsx</span>
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

## 읽을거리

Next.js의 에러 핸들링에 대해 더 배우고싶다면, 다음의 문서들을 확인하세요.

- [Error Handling](https://nextjs.org/docs/app/building-your-application/routing/error-handling)
- [error.js API Reference](https://nextjs.org/docs/app/api-reference/file-conventions/error)
- [notFound() API Reference](https://nextjs.org/docs/app/api-reference/functions/not-found)
- [not-found.js API Reference](https://nextjs.org/docs/app/api-reference/file-conventions/not-found)

<div class="finish">
  <p class="finish__title">챕터 13을 완료했습니다.</p>
  <p>이제 어플리케이션에서 우아하게 에러를 다룰 수 있습니다.</p>
  <div class="next-box">
    <p class="next">다음</p>    
    <p class="next__title">14. 접근성 향상</p>
    <p>유저 경험을 향상시킬 방법을 알아봅시다. 서버 사이드 폼(form) 유효성 검사와 접근성 향상을 배울겁니다.</p>
    <a id="next__btn" href="https://nextjs.org/learn/dashboard-app/improving-accessibility">챕터 14 시작하기
    <svg data-testid="geist-icon" height="16" stroke-linejoin="round" viewBox="0 0 16 16" width="16" style="color: currentcolor;"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.53033 2.21968L9 1.68935L7.93934 2.75001L8.46967 3.28034L12.4393 7.25001H1.75H1V8.75001H1.75H12.4393L8.46967 12.7197L7.93934 13.25L9 14.3107L9.53033 13.7803L14.6036 8.70711C14.9941 8.31659 14.9941 7.68342 14.6036 7.2929L9.53033 2.21968Z" fill="currentColor"></path></svg>
    </a>
  </div>
</div>

## Ref
- <https://nextjs.org/learn/dashboard-app/error-handling>


<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/next-js-tutorial/css.css">
<script type="text/javascript" src="https://eso0117.github.io/web-practice/public/next-js-tutorial/js.js"></script>
