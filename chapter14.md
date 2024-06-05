## 들어가기 전에

이 글은 [Next.js - Chapter 14 : Improving Accessibility
](https://nextjs.org/learn/dashboard-app/improving-accessibility) 강의를 번역한 글입니다.

# 접근성 향상

이전 챕터에서, 404 에러를 포함하여 에러를 처리하여 대체 UI를 보여주는 방법을 배웠습니다. 하지만 아직도 폼(form) 유효성 검사 관련하여 남은 퍼즐 조각에 대해 이야기 할 게 남아있습니다. 이제 Server Action을 통한 서버사이드 유효성 검사를 하는 방법과 `useFormState` 훅을 통해 에러를 보여주는 방법을 배울겁니다. -- 접근성을 고려하면서

## 이번 챕터에서...

여기 우리가 다룰 주제들이 있습니다.
- `eslint-plugin-jsx-a11y` 를 통한 Next.js에서 접근성을 구현하는 좋은 방법
- 서버 사이드 폼(form) 유효성 검사를 하는 방법
- 리액트 `useFormState` 훅을 사용해 폼(form) 에러를 다루고 방법 유저들에게 보여주는 방법.

## 접근성이란 무엇인가요?

접근성이란 장애가 있는 사람까지 포함하여 **모두**가 사용할 수 있도록 웹을 디자인하고 구현하는 것을 말합니다. 이것에 대해선 다양한 영역을 아우르는 많은 주제가 있습니다. 키워드 이동, 시맨틱 HTML(Semantic HTML), 이미지, 색상, 비디오 등 말이죠.

이 강의에서는 접근성에 대해 자세히 다루지는 않을테지만, Next.js에서 가능한 접근성 관련 기능과 우리 어플리케이션의 접근성을 향상시킬 일반적인 방법들에 대해 이야기 해보겠습니다.

접근성에 대해 더 배우고 싶다면 [web.dev](https://web.dev/)의 [Learn Accessibility](https://web.dev/learn/accessibility/)를 추천합니다.

## Next.js에서 ESLint 접근성 플러그인 사용하기

기본적으로, Next.js는 접근성 이슈를 위해 [`eslint-plugin-jsx-a11y`](https://www.npmjs.com/package/eslint-plugin-jsx-a11y) 플러그인을 포함하고 있습니다. 예를 들어 이 플러그인은 `alt` 문구 없이 이미지를 사용하거나 `aria-*`와 `role` 요소를 부적절하게 사용하는 등 이러한 접근성 관련 이슈에 경고를 줍니다.

이제 어떻게 동작하는지 봅시다!

`package.json`파일에 `next lint`를 스크립트에 추가하세요.

<div class="code-with-file">

**/package.json**
```
"scripts": {
    "build": "next build",
    "dev": "next dev",
    "seed": "node -r dotenv/config ./scripts/seed.js",
    "start": "next start",
    "lint": "next lint"
},
```
</div>

그리고 `npm run lint`를 터미널에서 실행하세요!

<div class="code-with-file">

**Terminal**

```
npm run lint
```
</div>

그럼 다음과 같은 메시지가 보일겁니다.

<div class="code-with-file">

**Terminal**

```
✔ No ESLint warnings or errors
```
</div>

그러나 만약 `alt`없는 이미지가 있었다면 어떻게 되었을까요? 한번 알아봅시다!

`/app/ui/invoices/table.tsx`로 가서, `Image`의 `alt` prop을 지우세요. 에디터의 검색기능을 이용하면 빠르게 찾을 수 있습니다.

<div class="code-with-file">

**/app/ui/invoices/table.tsx**

```
<Image
  src={invoice.image_url}
  className="rounded-full"
  width={28}
  height={28}
  alt={`${invoice.name}'s profile picture`} // Delete this line
/>
```
</div>

다시 `npm run lint`를 실행해봅시다. 그러면 다음과 같은 에러를 확인할 수 있습니다.

<div class="code-with-file">

**Terminal**
```
./app/ui/invoices/table.tsx
45:25  Warning: Image elements must have an alt prop,
either with meaningful text, or an empty string for decorative images. jsx-a11y/alt-text
```
</div>

`next lint`는 빌드 과정의 일부에도 포함되어 동작하기 때문에, 만약 Vercel에 어플리케이션을 배포하려했다면, 빌드 로그에 이 경고가 나타났을겁니다. 그래서 접근성 이슈를 위해 `lint`를 로컬에서 돌려 배포하기 전에 알아낼 수 있습니다.

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
