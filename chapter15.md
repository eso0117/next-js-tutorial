## 들어가기 전에

이 글은 [Next.js - Chapter 15 : Adding Authentication
](https://nextjs.org/learn/dashboard-app/adding-authentication) 강의를 번역한 글입니다.

# 인증 추가하기

이전 챕터에서, 폼(form) 유효성 검사를 추가하고, 접근성 향상을 하여 invoices 라우트를 완성했습니다. 이번 챕터에서는 대시보드에 인증을 추가할 것입니다.

## 이번 챕터에서...

여기 우리가 다룰 주제들이 있습니다.
- 인증(authentication)이란 무엇인지
- NextAuth.js를 통해 인증을 추가하는 방법
- 우리의 라우트를 보호하고, 유저를 리다이렉트 하기 위해 미들웨어를 사용하는 방법
- 폼(form) 에러와 pending 상태를 처리하기 위한 React의 `useFormStatus`와 `useFormState`를 사용하는 방법


## 인증(authentication)이란 무엇인가요?

인증(Authentication)은 오늘날 많은 웹 어플리케이션에서 핵심 파트입니다. 인증은 시스템이 유저가 자기가 말하는 사람이 맞는지 체크하는 방법입니다.

안전한 웹사이트는 유저의 신분을 확인할 다양한 방법을 사용합니다. 예를들면, 유저 이름과 비밀번호를 입력한 후에 인증 코드를 유저의 기기에 보내거나 Google Authenticator 같은 외부 앱을 사용합니다. 이러한 2단계 인증(2FA)은 보안을 향상시킵니다. 누군가 비밀번호를 알아내도, 고유의 토큰 없이는 계정에 접근할 수 없으니까요.

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

우리 프로젝트의 루트 경로에 `authConfig` 객체를 내보내는(export) `auth.config.ts` 파일을 생성하세요. 이 파일은 NextAuth.js의 설정 옵션들이 있습니다. 지금은 `pages` 옵션만 있습니다.

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

`pages` 옵션을 이용해 로그인, 로그아웃 그리고 에러 페이지에 대한 경로를 명시 할 수 있습니다. 이것은 필수 필드가 아니지만, `pages` 옵션에 `signIn: '/login'` 을 추가하여 유저는 NextAuth.js 기본 페이지가 아닌 커스텀 로그인 페이지로 리다이렉트 될 수 있습니다.

## Next.js 미들웨어를 통한 라우트 보호하기

이제 우리 라우트를 보호하는 로직을 추가하여 로그인 안 한 유저가 대시보드에 접근하는 것을 막을겁니다.

<div class="code-with-file">

**/auth.config.ts**
```
import type { NextAuthConfig } from 'next-auth';
 
export const authConfig = {
  pages: {
    signIn: '/login',
  },
  callbacks: {
    authorized({ auth, request: { nextUrl } }) {
      const isLoggedIn = !!auth?.user;
      const isOnDashboard = nextUrl.pathname.startsWith('/dashboard');
      if (isOnDashboard) {
        if (isLoggedIn) return true;
        return false; // Redirect unauthenticated users to login page
      } else if (isLoggedIn) {
        return Response.redirect(new URL('/dashboard', nextUrl));
      }
      return true;
    },
  },
  providers: [], // Add providers with an empty array for now
} satisfies NextAuthConfig;
```
</div>

`authorized` 콜백은 요청이 [Next.js 미들웨어](https://nextjs.org/docs/app/building-your-application/routing/middleware)를 통해 페이지에 접근하도록 승인이 되었는지 검증하는데 사용합니다. 이것은 페이지 접근 요청이 완료되기 전에 호출되고, `auth`와 `request` 값이 담긴 객체를 받습니다. `auth` 값은 유저의 세션을 담고 있고, `request`는 들어오는 요청을 담고 있습니다.

`providers` 옵션은 다양한 로그인 옵션이 나열된 배열입니다. 지금은 NextAuth 설정을 위해 빈 배열로 두겠습니다. [Credentials provider 추가하기](https://nextjs.org/learn/dashboard-app/adding-authentication#adding-the-credentials-provider)에서 이것에 대해 더 배울 수 있습니다.

다음으로,`authConfig` 객체를 미들웨어 파일에 불러(import)옵니다. 프로젝트 루트 경로에 `middleware.ts`라는 이름의 파일을 생성하고 다음의 코드를 붙여넣으세요.

<div class="code-with-file">

**/middleware.ts**
```
import NextAuth from 'next-auth';
import { authConfig } from './auth.config';
 
export default NextAuth(authConfig).auth;
 
export const config = {
  // https://nextjs.org/docs/app/building-your-application/routing/middleware#matcher
  matcher: ['/((?!api|_next/static|_next/image|.*\\.png$).*)'],
};
```
</div>

여기에서 `authConfig` 객체를 사용해 NextAuth.js를 초기화 하고 `auth` 프로퍼티를 내보내고(export) 있습니다. 또 미들웨어의 `matcher` 옵션을 사용해서 지정된 경로에서 동작하도록 명시합니다.

미들웨어를 사용하는 이점은, 보호되고 있는 라우트가 미들웨어를 통해 인증을 확인하기 전까지는 렌더링을 시작하지 않는다는 것입니다 이를 통해 어플리케이션의 퍼포먼스와 보안성을 향상시킵니다.

### 비밀번호 해싱(hashing)

데이터베이스에 비밀번호를 넣기 전에 암호를 **해시(hash)**하는 것은 좋은 방법입니다. 해싱은 비밀번호를 고정된 길이의 랜덤값의 문자열로 변환합니다. 이것은 보안층을 만들어 유저의 데이터가 노출되는것을 막습니다.

`seed.js` 파일 내에서, `bcrypt`란 패키지를 사용해 비밀번호가 데이터베이스에 저장되기 전에 그 값을 해시(hash)했었습니다. 이것을 이 장의 뒷부분에서 *다시* 사용하여 유저가 입력한 비밀번호가 데이터베이스에 있는것과 일치하는지 비교하는데 쓸 겁니다. 지금은 `bcrypt` 패키지를 위한 별도의 파일을 생성할겁니다. 왜냐하면 `bcrypt`는 Next.js 미들웨어가 아닌 Node.js API를 사용하기 때문입니다.

`authConfig` 객체를 전개하는 `auth.ts`란 이름의 파일을 생성하세요.

<div class="code-with-file">

**/auth.ts**
```
import NextAuth from 'next-auth';
import { authConfig } from './auth.config';
 
export const { auth, signIn, signOut } = NextAuth({
  ...authConfig,
});
```
</div>

### Credentials provider 추가하기

다음으로, NextAuth.js에 `providers` 옵션을 추가합니다. `providers`는 Google이나 GitHub 같은 다양한 로그인 옵션을 나열하는 배열입니다. 이 강의에서는 [Credentials provider](https://authjs.dev/getting-started/providers/credentials-tutorial)를 사용하는데 집중하겠습니다.

Credentials provider는 유저이름과 비밀번호로 로그인할 수 있도록 해줍니다.

<div class="code-with-file">


**/auth.ts**
```
import NextAuth from 'next-auth';
import { authConfig } from './auth.config';
import Credentials from 'next-auth/providers/credentials';
 
export const { auth, signIn, signOut } = NextAuth({
  ...authConfig,
  providers: [Credentials({})],
});
```
</div>

<div class="hint">

**알면 좋은 것**
우리는 Credentials provider를 사용하지만, 일반적으로 [OAuth](https://authjs.dev/getting-started/providers/oauth-tutorial)나 [email](https://authjs.dev/getting-started/providers/email-tutorial) 같은 다른 provider를 사용하는걸 추천합니다. 가능한 리스트를 [NextAuth.js 문서](https://authjs.dev/getting-started/providers)에서 확인할 수 있습니다.
</div>

### 로그인 기능 추가하기

이제 인증 로직을 다루기 위해 `authorize`를 사용할 수 있습니다. Server Action과 유사하게 `zod`를 이용하여 이메일과 비밀번호를 통해 데이터베이스 내에 해당 유저가 존재하는지 유효성 검사를 할 수 있습니다.

<div class="code-with-file">

**/auth.ts**
```
import NextAuth from 'next-auth';
import { authConfig } from './auth.config';
import Credentials from 'next-auth/providers/credentials';
import { z } from 'zod';
 
export const { auth, signIn, signOut } = NextAuth({
  ...authConfig,
  providers: [
    Credentials({
      async authorize(credentials) {
        const parsedCredentials = z
          .object({ email: z.string().email(), password: z.string().min(6) })
          .safeParse(credentials);
      },
    }),
  ],
});
```
</div>

신원을 확인한 후, 데이터베이스로부터 유저를 질의하는 `getUser`함수를 생성합니다.

<div class="code-with-file">

**/auth.ts**
```
import NextAuth from 'next-auth';
import Credentials from 'next-auth/providers/credentials';
import { authConfig } from './auth.config';
import { z } from 'zod';
import { sql } from '@vercel/postgres';
import type { User } from '@/app/lib/definitions';
import bcrypt from 'bcrypt';
 
async function getUser(email: string): Promise<User | undefined> {
  try {
    const user = await sql<User>`SELECT * FROM users WHERE email=${email}`;
    return user.rows[0];
  } catch (error) {
    console.error('Failed to fetch user:', error);
    throw new Error('Failed to fetch user.');
  }
}
 
export const { auth, signIn, signOut } = NextAuth({
  ...authConfig,
  providers: [
    Credentials({
      async authorize(credentials) {
        const parsedCredentials = z
          .object({ email: z.string().email(), password: z.string().min(6) })
          .safeParse(credentials);
 
        if (parsedCredentials.success) {
          const { email, password } = parsedCredentials.data;
          const user = await getUser(email);
          if (!user) return null;
        }
 
        return null;
      },
    }),
  ],
});
```
</div>

그다음, `bcrypt.compare`를 호출해 비밀번호가 맞는지 확인하세요

<div class="code-with-file">

**/auth.ts**
```
import NextAuth from 'next-auth';
import Credentials from 'next-auth/providers/credentials';
import { authConfig } from './auth.config';
import { sql } from '@vercel/postgres';
import { z } from 'zod';
import type { User } from '@/app/lib/definitions';
import bcrypt from 'bcrypt';
 
// ...
 
export const { auth, signIn, signOut } = NextAuth({
  ...authConfig,
  providers: [
    Credentials({
      async authorize(credentials) {
        // ...
 
        if (parsedCredentials.success) {
          const { email, password } = parsedCredentials.data;
          const user = await getUser(email);
          if (!user) return null;
          const passwordsMatch = await bcrypt.compare(password, user.password);
 
          if (passwordsMatch) return user;
        }
 
        console.log('Invalid credentials');
        return null;
      },
    }),
  ],
});
```
</div>

마지막으로 비밀번호가 일치하면 유저를 반환하고, 아니면 `null`을 반환하여 유저가 로그인 되는걸 막습니다.

### 로그인 폼(form) 업데이트 하기

이제 인증 로직과 우리의 로그인 폼(form)을 연결해야 합니다. `actions.ts` 파일 내에 `authenticate`라는 이름의 새로운 액션을 추가합니다. 해당 액션은 `auth.ts`에서 `signIn` 함수를 불러(import)와야 합니다.

<div class="code-with-file">

**/app/lib/actions.ts**
```
import { signIn } from '@/auth';
import { AuthError } from 'next-auth';
 
// ...
 
export async function authenticate(
  prevState: string | undefined,
  formData: FormData,
) {
  try {
    await signIn('credentials', formData);
  } catch (error) {
    if (error instanceof AuthError) {
      switch (error.type) {
        case 'CredentialsSignin':
          return 'Invalid credentials.';
        default:
          return 'Something went wrong.';
      }
    }
    throw error;
  }
}
```
</div>

만약 `'CredentialsSignin'` 오류가 있다면, 적절한 오류 메시지를 띄워야 할겁니다. NextAuth.js 관련 오류에 대해서는 [이 문서](https://authjs.dev/reference/core/errors)에서 찾아볼 수 있습니다.

마지막으로, `login-form.tsx` 컴퍼넌트 내에서, `useFormState`를 사용해 Server Action을 호출하고 폼(form) 에러를 처리할 수 있습니다. 그리고 `useFormStatus`를 사용해서 폼(form)의 지연 상태를 처리할 수 있습니다.

<div class="code-with-file">

**app/ui/login-form.tsx**
```
'use client';
 
import { lusitana } from '@/app/ui/fonts';
import {
  AtSymbolIcon,
  KeyIcon,
  ExclamationCircleIcon,
} from '@heroicons/react/24/outline';
import { ArrowRightIcon } from '@heroicons/react/20/solid';
import { Button } from '@/app/ui/button';
import { useFormState, useFormStatus } from 'react-dom';
import { authenticate } from '@/app/lib/actions';
 
export default function LoginForm() {
  const [errorMessage, dispatch] = useFormState(authenticate, undefined);
 
  return (
    <form action={dispatch} className="space-y-3">
      <div className="flex-1 rounded-lg bg-gray-50 px-6 pb-4 pt-8">
        <h1 className={`${lusitana.className} mb-3 text-2xl`}>
          Please log in to continue.
        </h1>
        <div className="w-full">
          <div>
            <label
              className="mb-3 mt-5 block text-xs font-medium text-gray-900"
              htmlFor="email"
            >
              Email
            </label>
            <div className="relative">
              <input
                className="peer block w-full rounded-md border border-gray-200 py-[9px] pl-10 text-sm outline-2 placeholder:text-gray-500"
                id="email"
                type="email"
                name="email"
                placeholder="Enter your email address"
                required
              />
              <AtSymbolIcon className="pointer-events-none absolute left-3 top-1/2 h-[18px] w-[18px] -translate-y-1/2 text-gray-500 peer-focus:text-gray-900" />
            </div>
          </div>
          <div className="mt-4">
            <label
              className="mb-3 mt-5 block text-xs font-medium text-gray-900"
              htmlFor="password"
            >
              Password
            </label>
            <div className="relative">
              <input
                className="peer block w-full rounded-md border border-gray-200 py-[9px] pl-10 text-sm outline-2 placeholder:text-gray-500"
                id="password"
                type="password"
                name="password"
                placeholder="Enter password"
                required
                minLength={6}
              />
              <KeyIcon className="pointer-events-none absolute left-3 top-1/2 h-[18px] w-[18px] -translate-y-1/2 text-gray-500 peer-focus:text-gray-900" />
            </div>
          </div>
        </div>
        <LoginButton />
        <div
          className="flex h-8 items-end space-x-1"
          aria-live="polite"
          aria-atomic="true"
        >
          {errorMessage && (
            <>
              <ExclamationCircleIcon className="h-5 w-5 text-red-500" />
              <p className="text-sm text-red-500">{errorMessage}</p>
            </>
          )}
        </div>
      </div>
    </form>
  );
}
 
function LoginButton() {
  const { pending } = useFormStatus();
 
  return (
    <Button className="mt-4 w-full" aria-disabled={pending}>
      Log in <ArrowRightIcon className="ml-auto h-5 w-5 text-gray-50" />
    </Button>
  );
}
```
</div>

## 로그아웃 기능 추가하기

`<SideNav />`에 로그아웃 기능을 추가하기 위해 `<form>` 요소 내에서 `auth.ts`의 `signOut` 함수를 호출하세요.

<div class="code-with-file">

**/ui/dashboard/sidenav.tsx**
```
import Link from 'next/link';
import NavLinks from '@/app/ui/dashboard/nav-links';
import AcmeLogo from '@/app/ui/acme-logo';
import { PowerIcon } from '@heroicons/react/24/outline';
import { signOut } from '@/auth';
 
export default function SideNav() {
  return (
    <div className="flex h-full flex-col px-3 py-4 md:px-2">
      // ...
      <div className="flex grow flex-row justify-between space-x-2 md:flex-col md:space-x-0 md:space-y-2">
        <NavLinks />
        <div className="hidden h-auto w-full grow rounded-md bg-gray-50 md:block"></div>
        <form
          action={async () => {
            'use server';
            await signOut();
          }}
        >
          <button className="flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3">
            <PowerIcon className="w-6" />
            <div className="hidden md:block">Sign Out</div>
          </button>
        </form>
      </div>
    </div>
  );
}
```
</div>

## 시도해보기

이제 한번 로그인해봅시다. 다음 정보로 로그인과 로그아웃이 가능할겁니다.

- Email: `user@nextmail.com` 
- Passworkd: `123456`

<div class="finish">
  <p class="finish__title">챕터 15를 완료했습니다.</p>
  <p>어플리케이션에 인증을 추가하여 대시보드 경로를 보호했습니다.</p>
  <div class="next-box">
    <p class="next">다음</p>    
    <p class="next__title">16. 메타데이터 추가하기</p>
    <p>이제 공유를 위한 메타데이터 추가하는 방법을 배우면서 어플리케이션을 완성하세요.</p>
    <a id="next__btn" href="https://thewys.tistory.com/entry/NextJS-튜토리얼-챕터-16-메타데이터-추가하기">챕터 16 시작하기
    <svg data-testid="geist-icon" height="16" stroke-linejoin="round" viewBox="0 0 16 16" width="16" style="color: currentcolor;"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.53033 2.21968L9 1.68935L7.93934 2.75001L8.46967 3.28034L12.4393 7.25001H1.75H1V8.75001H1.75H12.4393L8.46967 12.7197L7.93934 13.25L9 14.3107L9.53033 13.7803L14.6036 8.70711C14.9941 8.31659 14.9941 7.68342 14.6036 7.2929L9.53033 2.21968Z" fill="currentColor"></path></svg>
    </a>
  </div>
</div>

## Ref
- <https://nextjs.org/learn/dashboard-app/adding-authentication#adding-the-credentials-provider>


<link rel="stylesheet" href="https://eso0117.github.io/web-practice/public/next-js-tutorial/css.css">
<script type="text/javascript" src="https://eso0117.github.io/web-practice/public/next-js-tutorial/js.js"></script>
