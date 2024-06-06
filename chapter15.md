## 들어가기 전에

이 글은 [Next.js - Chapter 15 : Adding Authentication
](https://nextjs.org/learn/dashboard-app/adding-authentication) 강의를 번역한 글입니다.

# 인증 추가하기

이전 챕터에서, 폼(form) 유효성 검사를 추가하고, 접근성 향상을 하여 invoices 라우트를 완성했습니다. 이번 챕터에서는 대시보드에 인증을 추가할 것입니다.

## 이번 챕터에서...

여기 우리가 다룰 주제들이 있습니다.
- 인증(authentication)이란 무엇인지
- NextAuth.js를 통해 인증을 추가하는 방법
- 우리의 라우트를 지키고, 유저를 리다이렉트 하기 위해 미들웨어를 사용하는 방법
- 폼(form) 에러와 pending 상태를 처리하기 위한 React의 `useFormStatus`와 `useFormState`를 사용하는 방법


## 인증(authentication)이란 무엇인가요?

인증(Authentication)은 오늘날 많은 웹 어플리케이션에서 핵심 파트입니다. 인증은 시스템이 유저가 자기가 말하는 사람이 맞는지 체크하는 방법입니다.

안전한 웹사이트는 유저의 신분을 확인할 다양한 방법을 사용합니다. 예를들면, 유저 이름과 비밀번호를 입력한 후에 인증 코드를 유저의 기기에 보내거나 Google Authenticator 같은 외부 앱을 사용합니다. 이러한 2단계 인증(2FA) 보안을 향상시킵니다. 누군가 비밀번호를 알아내도, 고유의 토큰 없이는 계정에 접근할 수 없으니까요.

### 인증(Authentication) vs 권한(Authorization)

웹 개발에서, 인증(Authentication)과 권한(Authorization)은 다른 역할을 합니다.
- **인증(Authentication)**은 유저가 말하는 사람이 맞는지 확인하는 것입니다. 유저이름과 비밀번호 같은 것으로 우리의 신분을 증명합니다.
- **권한(Authorization)**은 그 다음 과정입니다. 유저의 신분이 확인되면, 권한은 해당 유저가 사용할 수 있는 어플리케이션의 범위를 결정합니다.

인증은 당신이 누구인지 확인하는 것이고, 권한은 어플리케이션에서 할 수 있는 것, 접근할 수 있는 것을 결정합니다.

<div class="quiz">
  <div class="quiz__icon">
    <svg fill="none" height="32" viewBox="0 0 32 32" width="32" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_132_19094)"><path clip-rule="evenodd" d="M16 30.5C24.0081 30.5 30.5 24.0081 30.5 16C30.5 7.99187 24.0081 1.5 16 1.5C7.99187 1.5 1.5 7.99187 1.5 16C1.5 24.0081 7.99187 30.5 16 30.5ZM16 32C24.8366 32 32 24.8366 32 16C32 7.16344 24.8366 0 16 0C7.16344 0 0 7.16344 0 16C0 24.8366 7.16344 32 16 32ZM17.5 22C17.5 22.8284 16.8284 23.5 16 23.5C15.1716 23.5 14.5 22.8284 14.5 22C14.5 21.1716 15.1716 20.5 16 20.5C16.8284 20.5 17.5 21.1716 17.5 22ZM13.5142 11.3218C13.9564 10.391 14.9041 9.75 16 9.75C17.5188 9.75 18.75 10.9812 18.75 12.5C18.75 13.8852 17.7252 15.0323 16.3926 15.2223C15.8162 15.3045 15.25 15.787 15.25 16.5V17.25V18H16.75V17.25V16.6839C18.7397 16.3292 20.25 14.5916 20.25 12.5C20.25 10.1528 18.3472 8.25 16 8.25C14.3035 8.25 12.8406 9.24406 12.1593 10.6782L11.8375 11.3556L13.1924 11.9993L13.5142 11.3218Z" fill="currentColor" fill-rule="evenodd"></path></g><defs><clipPath id="clip0_132_19094"><rect fill="currentColor" height="32" width="32"></rect></clipPath></defs></svg>
  </div>
  <p class="quiz__title">퀴즈할 시간입니다!</p>
  <p class="quiz__desc">익힌걸 테스트해보고 무엇을 배웠는지 봅시다.</p>
  <div class="quiz__box">
    <p class="quiz__question">인증과 권한에 대한 차이를 설명하는 것 중 어느 게 제일 옳은 걸까요?</p>
    <div class="option-list">
      <div class="option" data-question-number="01">
        <span class="option__number">A</span>
        <span class="option__desc">인증은 접근할 수 있는것을 결정합니다. 권한은 신분을 확인합니다.</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">B</span>
        <span class="option__desc">인증과 권한 둘 다 유저가 접근할 수 있는 어플리케이션의 부분을 결정합니다.</span>
      </div>
      <div class="option" data-question-number="01" data-answer="true">
        <span class="option__number">C</span>
        <span class="option__desc">인증은 신분을 확인하는 것이고, 권한은 유저가 접근할 수 있는걸 결정합니다.</span>
      </div>
      <div class="option" data-question-number="01">
        <span class="option__number">D</span>
        <span class="option__desc">차이는 없습니다. 둘 다 같은 의미입니다.</span>
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

## 로그인 라우트 생성하기

`/login`이라는 이름의 새로운 라우트를 생성하고 다음의 코드를 붙여넣으세요.

<div class="code-with-file">

**/app/login/page.tsx**
```
import AcmeLogo from '@/app/ui/acme-logo';
import LoginForm from '@/app/ui/login-form';
 
export default function LoginPage() {
  return (
    <main className="flex items-center justify-center md:h-screen">
      <div className="relative mx-auto flex w-full max-w-[400px] flex-col space-y-2.5 p-4 md:-mt-32">
        <div className="flex h-20 w-full items-end rounded-lg bg-blue-500 p-3 md:h-36">
          <div className="w-32 text-white md:w-36">
            <AcmeLogo />
          </div>
        </div>
        <LoginForm />
      </div>
    </main>
  );
}
```
</div>

이 페이지에서 추후 업데이트 할 `<LoginForm />` 을 불러(import)오고 있습니다.

## NextAuth.js

어플리케이션에 인증을 추가하기 위해 [NextAuth.js](https://nextjs.authjs.dev/)를 사용할 겁니다. NextAuth.js는 세션을 관리, 로그인과 로그아웃, 그 외 인증에 필요한 여러가지를 복잡성을 추상화합니다. 우리가 일일이 이러한 기능들을 구현한다면, 그 과정에서 시간이 많이 소요되고 에러도 나기 쉬울겁니다. NextAuth.js는 Next.js 어플리케이션에서의 인증을 위한 통합된 솔루션을 제공하면서 이러한 과정을 단순화합니다.

## NextAuth.js 세팅하기

터미널에서 아래의 명령을 실행하여 NextAuth.js를 설치하세요.


<div class="code-with-file">

**Terminal**

```
npm install next-auth@beta
```
</div>

지금 우리는 Next.js 14와 호환 되는 NextAuth.js의 `beta` 버전을 설치하고 있습니다.

다음으로 어플리케이션의 비밀 키를 생성하세요. 이 키는 유저의 세션 보안을 강화하는 쿠키의 암호화 키로 사용됩니다. 키 생성은 터미널에서 다음과 같은 명령어를 실행하여 생성할 수 있습니다.

<div class="code-with-file">

**Terminal**
```
openssl rand -base64 32
```
</div>

그다음, `.env` 파일 내에 `AUTH_SECRET` 변수에 생성된 키를 넣으세요.

<div class="code-with-file">

**.env**

```
AUTH_SECRET=your-secret-key
```
</div>

프로덕션 환경에서 인증이 동작하게 하려면, Vercel 프로젝트에서도 환경변수를 업데이트 해야합니다. 이 [가이드](https://vercel.com/docs/projects/environment-variables)를 확인하여 Vercel에서 환경 변수를 추가하는 방법을 알 수 있습니다.

### 페이지 옵션 추가하기

우리 프로젝트의 루트 경로에 `authConfig` 객체를 내보내는(export) `auth.config.ts` 파일을 생성하세요. 이 파일은 NextAuth.js의 설정 옵션들이 있습니다. 지금은 `page` 옵션만 있습니다.

<div class="code-with-file">

**/auth.config.ts**
```
import type { NextAuthConfig } from 'next-auth';
 
export const authConfig = {
  pages: {
    signIn: '/login',
  },
};
```
</div>

`page` 옵션을 이용해 로그인, 로그아웃 그리고 에러 페이지에 대한 경로를 명시 할 수 있습니다. 이것은 필수 필드가 아니지만, `pate` 옵션에 `signIn: '/login'` 을 추가하여 유저는 NextAuth.js 기본 페이지가 아닌 커스텀 로그인 페이지로 리다이렉트 될 수 있습니다.

## Next.js 미들웨어를 통한 라우트 지키기








## 폼(form) 접근성 향상

폼(form)의 접근성 향상을 위해 우리가 이미 해놓은 3가지가 있습니다.

- **시맨틱 HTML(Semantic HTML)**: `<div>` 대신 시맨틱 요소(`<input>`, `<option>` 등)을 사용합니다. 이것은 폼(form)을 이해하기 쉽게 만들어, 보조기술(AT)이 입력 요소를 찾고, 적절한 문맥적 정보를 유저에게 줄 수 있도록 합니다. 
- **라벨링**: `<label>`과 `htmlFor`를 요소를 추가하여, 각 폼(form) 필드에 설명하는 텍스트 라벨을 갖게 합니다. 이것은 문맥을 제공하고 유저가 라벨을 클릭하여 해당하는 인풋 필드에 포커스를 할 수 있도록 하여 이용성을 증대시킴으로서 보조기술(AT)의 도움을 향상시킵니다.
- **포커스 윤곽선**: 필드에 초점이 맞춰졌을때, 윤곽선이 나타나도록 적절히 스타일링 합니다. 이것은 페이지의 활성화된 요소를 시각적으로 가리킴으로서 키보드와 스크린 리더가 유저에게 폼에서의 현재 위치를 알려줌으로서 접근성에 중요한 역할을 합니다. `tab`을 눌러 확인할 수 있습니다.

이러한 방법들은 더 많은 유저들이 우리의 폼(form)에 접근 가능하게 만드는 좋은 기초가 됩니다. 그러나 그것들은 **폼(form)유효성 검사**나 **에러**를 알려주진 않습니다.

## 폼(form) 유효성검사

<http://localhost:3000/dashboard/invoices/create>에 가보세요, 그리고 빈 폼(form)을 제출해보세요. 어떤일이 일어나나요?

에러를 얻습니다! 이것은 Server Action에 빈 폼(form)을 보냈기 때문입니다. 우리는 클라이언트나 서버에서 폼(form) 유효성 검사를 통해 이것을 막을 수 있습니다.

### 클라이언트 사이드 유효성 검사

클라이언트 사이드에서 폼(form)의 유효성을 검사 할 다양한 방법이 있습니다.
가장 간단한 방법은 폼(form) 안에 `<input>`과 `<select>` 요소에 `required` 속성을 추가하여 브라우저에서 제공하는 폼 유효성 검사 방식을 사용하는 것 입니다.

예를들어

<div class="code-with-file">

**/app/ui/invoices/create-form.tsx**
```
<input
  id="amount"
  name="amount"
  type="number"
  placeholder="Enter USD amount"
  className="peer block w-full rounded-md border border-gray-200 py-2 pl-10 text-sm outline-2 placeholder:text-gray-500"
  required
/>
```
</div>

다시 폼(form) 제출을 해보세요. 만약 빈 값으로 폼(form)을 제출하려 했다면 브라우저창의 경고 메시지를 볼 것입니다.

몇몇 AT들은 브라우저 유효성 검사를 지원 하므로 이런 방식은 일반적으로 괜찮습니다.

클라이언트 사이드 유효성 검사의 대안은 서버 사이드 유효성 검사입니다. 다음 부분부터 어떻게 서버 사이드 유효성 검사를 구현할건지 알아볼 겁니다. 지금은 `required` 속성을 지우세요.

### 서버 사이드 유효성 검사

유효성 검사를 서버에서 함으로서
- 데이터베이스에 넣기 전에 데이터가 올바른 형태를 갖췄는지 확인할 수 있습니다.
- 악의적인 유저가 클라이언트 사이드 유효성 검사를 우회하는 것의 위험을 줄일 수 있습니다.
- 어떤게 `유효한` 데이터인지 확인할 [단일 진실 공급원](https://ko.wikipedia.org/wiki/단일_진실_공급원)을 갖게 됩니다.

`create-form.tsx` 컴퍼넌트 내에, `react-dom`으로부터 `useFormState` 훅을 불러(import)오세요. `useFormState`는 훅이기 때문에, 해당 폼(form)을 `"use client"` 선언을 통해 클라이언트 컴퍼넌트로 바꿔야 합니다.

<div class="code-with-file">

**/app/ui/invoices/create-form.tsx**
```
'use client';
 
// ...
import { useFormState } from 'react-dom';
```
</div>

폼(Form) 컴퍼넌트 내에, `useFormState` 훅은:
- 두개의 인자를 받습니다: `(action, initialState)`
- 두개의 값을 리턴합니다: `[state, dispatch]` - 폼(form) 상태와, dispatch 함수입니다. ([useReducer](https://react.dev/reference/react/useReducer))와 비슷합니다.

`createInvoice` 액션을 인자로서 `useFormState`에 넣으세요. 그리고 `<form action={}>` 속성 안에 dispatch를 넣어 호출하세요.

<div class="code-with-file">

**/app/ui/invoices/create-form.tsx**
```
// ...
import { useFormState } from 'react-dom';
 
export default function Form({ customers }: { customers: CustomerField[] }) {
  const [state, dispatch] = useFormState(createInvoice, initialState);
 
  return <form action={dispatch}>...</form>;
}
```
</div>

`초기상태(initialState)`는 우리가 정의하는 무엇이든 될 수 있습니다. 이번 경우에는 `message`와 `errors` 키를 갖고 있는 빈 오브젝트를 생성할 겁니다.

<div class="code-with-file">

**/app/ui/invoices/create-form.tsx**
```
// ...
import { useFormState } from 'react-dom';
 
export default function Form({ customers }: { customers: CustomerField[] }) {
  const initialState = { message: null, errors: {} };
  const [state, dispatch] = useFormState(createInvoice, initialState);
 
  return <form action={dispatch}>...</form>;
}
```
</div>

처음에는 좀 혼란스러워 보일 수 있습니다, 그러나 우리가 Server Action을 업데이트 한다면 더 이해가 될 겁니다.


`action.ts` 파일 내에, 폼(form) 유효성 검사를 위해 Zod를 사용할 수 있습니다. `FormSchema`를 다음과 같이 업데이트 하세요.

<div class="code-with-file">

**/app/lib/action.ts**
```
const FormSchema = z.object({
  id: z.string(),
  customerId: z.string({
    invalid_type_error: 'Please select a customer.',
  }),
  amount: z.coerce
    .number()
    .gt(0, { message: 'Please enter an amount greater than $0.' }),
  status: z.enum(['pending', 'paid'], {
    invalid_type_error: 'Please select an invoice status.',
  }),
  date: z.string(),
});
```
</div>

- `customerId` - `string`값이 오길 예상하는 고객 ID 필드가 비어있다면 Zod는 에러를 던집니다. 유저에게 고객을 선택하라는 친근한 메시지를 추가합니다.
- `amount` - 우리가 `amount`를 `string` 에서 `number`로 형 변환 하고 있으므로, 빈 문자열이 온다면 기본적으로 0으로 세팅합니다. Zod에 `.gt()` 함수를 통해 0보다 큰 값이 와야 한다고 선언합니다.
- `status` - `status`필드가 "pending" 이거나 "paid" 상태이길 예상하고 있습니다. 따라서 해당 값이 비어있다면 Zod는 에러를 던집니다. 유저에게 상태를 선택하라는 메시지를 추가합니다.

다음으로, `createInvoice` 액션을 두 파라미터를 받도록 업데이트 하세요.

<div class="code-with-file">

**/app/lib/actions.ts**
```
// This is temporary until @types/react-dom is updated
export type State = {
  errors?: {
    customerId?: string[];
    amount?: string[];
    status?: string[];
  };
  message?: string | null;
};
 
export async function createInvoice(prevState: State, formData: FormData) {
  // ...
}
```
</div>

- `formData` - 전과 같습니다.
- `prevState` - `useFormState` 훅으로부터 전달되는 state를 갖고 있습니다. 이 예제에서는 액션에서 해당 값을 사용하지 않을겁니다만, 필요한 prop입니다.

그다음, Zod의 `parse()` 함수를 `safeParse()`로 바꿉니다.

<div class="code-with-file">

**/app/lib/actions.ts**
```
export async function createInvoice(prevState: State, formData: FormData) {
  // Validate form fields using Zod
  const validatedFields = CreateInvoice.safeParse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });
 
  // ...
}
```
</div>

`safeParse()`는 `success`나 `error`필드를 담고있는 객체를 반환합니다. 이것은 내부에 `try/catch` 구문을 추가하는 것 없이 유효성 검사를 좀 더 우아하게(gracefully) 다루는데 도움을 줄겁니다.

데이터베이스에 정보를 보내기 전에, 조건에 맞게 폼(form) 필드들이 유효성 검사가 잘 이루어졌는지 확인합니다.

<div class="code-with-file">

**/app/lib/actions.ts**
```
export async function createInvoice(prevState: State, formData: FormData) {
  // Validate form fields using Zod
  const validatedFields = CreateInvoice.safeParse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });
 
  // If form validation fails, return errors early. Otherwise, continue.
  if (!validatedFields.success) {
    return {
      errors: validatedFields.error.flatten().fieldErrors,
      message: 'Missing Fields. Failed to Create Invoice.',
    };
  }
 
  // ...
}
```
</div>

만약 `validateFields`가 성공적이지 않다면, Zod 오류메시지와 함께 함수를 초기에 반환합니다.

<div class="hint">

**Tip:** validateFields를 console.log로 출력하게 만든다음, 빈 폼을 보내 어떠한 형태인지 확인해보세요.
</div>

마침내, 폼(form) 유효성 검사를 try/catch 구문 밖에서 별개로 하고 있습니다. 어떠한 데이터베이스 에러를 다루기 위한 특정한 에러 메시지도 반환할 수 있습니다. 최종 코드는 다음과 같을겁니다.

<div class="code-with-file">

**/app/lib/actions.ts**
```
export async function createInvoice(prevState: State, formData: FormData) {
  // Validate form using Zod
  const validatedFields = CreateInvoice.safeParse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });
 
  // If form validation fails, return errors early. Otherwise, continue.
  if (!validatedFields.success) {
    return {
      errors: validatedFields.error.flatten().fieldErrors,
      message: 'Missing Fields. Failed to Create Invoice.',
    };
  }
 
  // Prepare data for insertion into the database
  const { customerId, amount, status } = validatedFields.data;
  const amountInCents = amount * 100;
  const date = new Date().toISOString().split('T')[0];
 
  // Insert data into the database
  try {
    await sql`
      INSERT INTO invoices (customer_id, amount, status, date)
      VALUES (${customerId}, ${amountInCents}, ${status}, ${date})
    `;
  } catch (error) {
    // If a database error occurs, return a more specific error.
    return {
      message: 'Database Error: Failed to Create Invoice.',
    };
  }
 
  // Revalidate the cache for the invoices page and redirect the user.
  revalidatePath('/dashboard/invoices');
  redirect('/dashboard/invoices');
}
```
</div>

훌륭합니다, 이제 우리 폼(form) 컴퍼넌트에서 에러를 보여줘 봅시다. `create-form.tsx` 컴퍼넌트로 돌아가 폼의 `state`를 사용해 에러를 만들어 봅시다.

각 에러를 위한 **조건 연산자(tenary operator)**를 추가하세요. 예를들어 고객 필드 다음에 다음과 같이 추가할 수 있습니다.

<div class="code-with-file">

**/app/ui/invoices/create-form.tsx**
```
<form action={dispatch}>
  <div className="rounded-md bg-gray-50 p-4 md:p-6">
    {/* Customer Name */}
    <div className="mb-4">
      <label htmlFor="customer" className="mb-2 block text-sm font-medium">
        Choose customer
      </label>
      <div className="relative">
        <select
          id="customer"
          name="customerId"
          className="peer block w-full rounded-md border border-gray-200 py-2 pl-10 text-sm outline-2 placeholder:text-gray-500"
          defaultValue=""
          aria-describedby="customer-error"
        >
          <option value="" disabled>
            Select a customer
          </option>
          {customers.map((name) => (
            <option key={name.id} value={name.id}>
              {name.name}
            </option>
          ))}
        </select>
        <UserCircleIcon className="pointer-events-none absolute left-3 top-1/2 h-[18px] w-[18px] -translate-y-1/2 text-gray-500" />
      </div>
      <div id="customer-error" aria-live="polite" aria-atomic="true">
        {state.errors?.customerId &&
          state.errors.customerId.map((error: string) => (
            <p className="mt-2 text-sm text-red-500" key={error}>
              {error}
            </p>
          ))}
      </div>
    </div>
    // ...
  </div>
</form>
```
</div>

<div class="hint">

**Tip:**
console.log로 컴퍼넌트 내에 `state`를 출력하고, 모든게 잘 연결 되었는지 확인하세요. 우리의 폼(form)이 클라이언트 컴퍼넌트이므로 브라우저의 개발 툴에서 로그를 확인하세요.
</div>

위의 코드에서, 우리는 다음과 같은 aria 레이블을 추가하고 있습니다.

- `aria-describedby="customer-error"`: 이것은 `select`요소와 에러 메시지 컨테이너 간의 관계를 설정합니다. 이것은 `id="customer-error"`를 가진 컨테이너가 `select`요소를 설명한다고 가르킵니다. 스크린리더는 유저가 `select` 박스와 상호작용할때 그들에게 에러를 알리기 위해 이 설명을 읽을겁니다.
- `id="customer-error"`: `id` 요소는 `select` input에 에러 메시지를 갖고 있는 HTML 엘리먼트를 고유하게 구분합니다. 이것은 관계를 구축하기 위해 `aria-describedby`에 필수적입니다.
- `aria-live="polite"`: `div`내에 에러가 업데이트 되었을때 스크린 리더가 정중하게 유저에게 알려야 합니다. 컨텐츠가 바뀌었을때(예를들어, 유저가 에러를 고쳤을 경우), 스크린리더는 유저를 방해하지 않기 위해 유저가 액션을 하지 않는 상태일때에만 이러한 변화를 알립니다.

## 실습해보기: aria 라벨들 추가하기
위의 예제를 참고하여, 남은 폼(form) 필드들에 에러를 추가하세요. 어떤 필드라도 값이 안 채워져 있으면, 폼(form)의 하단에 메시지를 보여야 합니다. UI는 다음과 같습니다

<img src="https://nextjs.org/_next/image?url=%2Flearn%2Flight%2Fform-validation-page.png&w=1920&q=75" alt="챕터14 실습해보기">

준비가 되면, `npm run lint`를 실행해서 aria 라벨들을 바르게 사용하는지 확인하세요.

만약 더 도전적인걸 원한다면, 이번 챕터에서 배운걸 활용하여 `edit-form.tsx` 컴퍼넌트에 폼(form) 유효성 검사를 추가하세요

다음의 과정이 필요합니다.
- `useFormState`를 `edit-form.tsx` 컴퍼넌트에 추가하세요. 
- Zod의 유효성 검사를 다루기 위해 `updateInvoice` 액션을 수정하세요
- 컴퍼넌트 내에 에러를 보여주세요. 그리고 접근성 향상을 위해 aria 라벨들을 추가하세요.

준비가 되면, 아래 버튼을 눌러 해답을 확인하세요.

<button class="show-and-hide-btn--hidden show-and-hide-btn btn" data-target="practice-01">
  <span data-btn-content>정답 보이기</span>
</button>

<div class="is-inactive" data-hide-target="practice-01">

#### Edit Invoice Form:
  <div class="code-with-file">

**/app/ui/invoices/edit-form.tsx**
```
export default function EditInvoiceForm({
  invoice,
  customers,
}: {
  invoice: InvoiceForm;
  customers: CustomerField[];
}) {
  const initialState = { message: null, errors: {} };
  const updateInvoiceWithId = updateInvoice.bind(null, invoice.id);
  const [state, dispatch] = useFormState(updateInvoiceWithId, initialState);
 
  return <form action={dispatch}>...</form>;
}
```
  </div>


#### Server Action:
  <div class="code-with-file">

**/app/lib/actions.ts**
```
export async function updateInvoice(
  id: string,
  prevState: State,
  formData: FormData,
) {
  const validatedFields = UpdateInvoice.safeParse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });

  if (!validatedFields.success) {
    return {
      errors: validatedFields.error.flatten().fieldErrors,
      message: 'Missing Fields. Failed to Update Invoice.',
    };
  }

  const { customerId, amount, status } = validatedFields.data;
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


<div class="finish">
  <p class="finish__title">챕터 14를 완료했습니다.</p>
  <p>리액트 폼(form) Status와 서버사이드 유효성 검사를 통해 접근성을 향상시키는 방법을 배웠습니다.</p>
  <div class="next-box">
    <p class="next">다음</p>    
    <p class="next__title">15. 인증 추가하기</p>
    <p>우리 어플리케이션은 거의 다 되었습니다. 다음 챕터에서 NextAuth.js를 이용한 인증을 추가하는 방법을 배울겁니다.</p>
    <a id="next__btn" href="https://nextjs.org/learn/dashboard-app/adding-authentication">챕터 15 시작하기
    <svg data-testid="geist-icon" height="16" stroke-linejoin="round" viewBox="0 0 16 16" width="16" style="color: currentcolor;"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.53033 2.21968L9 1.68935L7.93934 2.75001L8.46967 3.28034L12.4393 7.25001H1.75H1V8.75001H1.75H12.4393L8.46967 12.7197L7.93934 13.25L9 14.3107L9.53033 13.7803L14.6036 8.70711C14.9941 8.31659 14.9941 7.68342 14.6036 7.2929L9.53033 2.21968Z" fill="currentColor"></path></svg>
    </a>
  </div>
</div>

## Ref
- <https://nextjs.org/learn/dashboard-app/improving-accessibility>


<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/next-js-tutorial/css.css">
<script type="text/javascript" src="https://eso0117.github.io/web-practice/public/next-js-tutorial/js.js"></script>
