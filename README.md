# 1.Turbopack

Turbopack은 Next.js의 개발 팀에 의해 만들어진 새로운 JavaScript 번들러로, 웹 애플리케이션의 빌드 성능을 크게 향상시키는 것을 목표로 하고 있다. 
빠른 빌드 속도, HMR 지원, Zero Configuration 등 여러 강력한 기능을 통해 개발자 경험을 향상시키고, 더 나은 개발 환경을 제공한다.

### 1. 빠른 빌드 성능:
Turbopack은 Rust로 작성된 번들러로, 빠른 빌드 성능을 제공한다. Webpack에 비해 훨씬 더 빠른 속도로 파일을 처리할 수 있으며, 이를 통해 개발자들이 더 효율적으로 작업할 수 있도록 돕는다.

### 2. HMR(Hot Module Replacement):
Turbopack은 HMR을 지원하여, 코드 변경 시 전체 페이지를 새로 고침하지 않고도 변경 사항을 즉시 반영할 수 있다. 이는 개발 중에 더 빠른 피드백 루프를 제공한다.

### 3. Incremental Build:
Turbopack은 증분 빌드를 지원하여, 변경된 파일만 다시 빌드합니다. 이를 통해 전체 빌드 시간을 줄이고, 대규모 프로젝트에서 특히 유용하다.

### 4. Zero Configuration:
Turbopack은 가능한 한 많은 기본 설정을 제공하여, 개발자가 복잡한 설정 없이 쉽게 사용할 수 있도록 합니다. 즉, 기본적인 사용에 있어 별도의 설정 없이도 작동한다.
Turbopack에 대한 설정을 수정하고 싶으면 next.config.js 파일에서 수정해주면 된다.

```javascript
// next.config.js

/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    turbopack: true, // Turbopack 활성화
  },
  turbopack: {
    // 필요한 경우 추가 설정

    // 예시: 특정 플러그인 사용
    plugins: [/* 추가 플러그인 */],

    // 예시: 특정 폴더를 제외할 경우
    exclude: ['node_modules', '.next'],

    // 예시: 특정 파일 형식에 대한 설정
    fileExtensions: ['js', 'ts', 'jsx', 'tsx', 'css', 'scss'],
  },
};

module.exports = nextConfig;
```

# 2.App Router, Page Router
Next.js 14에서는 앱 라우터와 페이지 라우터 모두 강력한 기능을 제공하며, 각 라우팅 방식은 특정 요구 사항에 따라 선택하여 사용할 수 있다. 
앱 라우터는 새로운 기능과 유연성을 제공하는 반면, 페이지 라우터는 기존의 안정성을 유지한다.


## App Router
앱 라우터는 Next.js의 새로운 라우팅 시스템으로, 더 나은 모듈화와 재사용성을 제공한다.

### 디렉토리 기반 라우팅:
앱 라우터는 파일 시스템을 기반으로 한 라우팅 방식을 사용한다. app 디렉토리 내에 생성된 파일과 폴더 구조에 따라 자동으로 라우트가 생성된다.
예를 들어, app/about/page.js 파일은 /about 경로에 매핑된다.


### 서버 컴포넌트와 클라이언트 컴포넌트:
앱 라우터는 서버 컴포넌트와 클라이언트 컴포넌트를 혼합하여 사용할 수 있다. 서버 컴포넌트는 서버에서 렌더링되고, 클라이언트 컴포넌트는 클라이언트에서 실행된다.
이를 통해 데이터 가져오기와 UI 렌더링을 최적화할 수 있다.

```javascript
app/
├── layout.js
├── ClientComponent.js
└── page.js
```

```javascript
export default function Layout({ children }) {
  return (
    <div>
      <header>
        <h1>My Website</h1>
      </header>
      <main>{children}</main>
    </div>
  );
}
```
```javascript
import ClientComponent from './ClientComponent';

export default function HomePage() {
  return (
    <div>
      <h1>Home Page</h1>
      <ClientComponent />
    </div>
  );
}
```
```javascript
// 클라이언트 컴포넌트
'use client';

export default function ClientComponent() {
  return <p>This is a client-side rendered component.</p>;
}
```

### 중첩 라우팅:
중첩된 레이아웃을 통해 복잡한 사용자 인터페이스를 쉽게 구성할 수 있다. 각 레이아웃은 독립적으로 관리되며, 재사용이 가능하다.

```javascript
app/
├── dashboard/
│   ├── layout.js
│   └── page.js
└── layout.js
```

```javascript
//app/layout.js:
export default function AppLayout({ children }) {
  return (
    <html>
      <body>
        <header>App Header</header>
        <main>{children}</main>
        <footer>App Footer</footer>
      </body>
    </html>
  );
}
```

```javascript
//app/dashboard/layout.js:
export default function DashboardLayout({ children }) {
  return (
    <div>
      <h2>Dashboard Menu</h2>
      <div>{children}</div>
    </div>
  );
}
```

```javascript
//app/dashboard/page.js:
export default function DashboardPage() {
  return <p>Welcome to the Dashboard!</p>;
}
```

의 구조에서 DashboardPage가 렌더링될 때, 최종적으로 다음과 같은 구조로 렌더링된다:
```javascript
<html>
  <body>
    <header>App Header</header>
    <main>
      <div>
        <h2>Dashboard Menu</h2>
        <div>
          <p>Welcome to the Dashboard!</p>
        </div>
      </div>
    </main>
    <footer>App Footer</footer>
  </body>
</html>
```
### 병렬 라우팅:
병렬 라우팅은 여러 개의 라우트를 동시에 로드하고 렌더링할 수 있는 기능이다. 이를 통해 서로 다른 레이아웃이나 페이지를 독립적으로 관리하면서도 동시에 표시할 수 있다. 이 방식은 복잡한 사용자 인터페이스를 구성할 때 유용하며, 다양한 상태를 동시에 보여줄 수 있다.
병렬 라우팅은 대시보드와 같은 복잡한 인터페이스에서 유용하다. 예를 들어, 대시보드의 왼쪽 사이드바와 오른쪽 콘텐츠 영역을 독립적으로 로드하고 업데이트할 수 있다. 이를 통해 사용자 경험을 향상시키고, 각 부분의 상태를 개별적으로 관리할 수 있다.
병렬 라우팅을 구현하기 위해서는 라우트 그룹을 정의해야 한다. 이때, 폴더 이름 앞에 @ 기호를 붙여야 한다. @로 시작하는 폴더는 병렬 라우트 그룹으로 인식되며, 이 그룹 내의 모든 페이지가 동시에 로드될 수 있다.

```javascript
/app
  ├── @dashboard
  │   ├── layout.tsx
  │   ├── page.tsx
  │   └── sidebar
  │       └── page.tsx
  ├──@settings
  │   ├── layout.tsx
  │   └── page.tsx
  ├──layout.tsx


@dashboard: 대시보드와 관련된 페이지와 컴포넌트를 포함하는 병렬 라우트 그룹.
@settings: 설정과 관련된 페이지와 컴포넌트를 포함하는 병렬 라우트 그룹.
```

```javascript
// app/@dashboard/layout.js
import Sidebar from './sidebar/page';

export default function DashboardLayout({ children }) {
  return (
    <div>
      <Sidebar /> {/* 사이드바 컴포넌트 */}
      <main>{children}</main> {/* 메인 콘텐츠 */}
    </div>
  );
}
```

```javascript
// app/@settings/layout.js
export default function SettingsLayout({ children }) {
  return (
    <div>
      <h2>Settings Header</h2>
      <main>{children}</main> {/* 메인 콘텐츠 */}
    </div>
  );
}
```

```javascript
// app/layout.js

Case.1
export default function RootLayout({ children }) {
  return (
    <div>
      <h1>Application Header</h1>
      <div className="main-content">
        {children} {/* 자식 컴포넌트 */}
      </div>
    </div>
  );
}

Case.2
export default function RootLayout({ dashboard, settings }) {
  return (
    <div>
      <h1>Application Header</h1>
      <div className="main-content">
        <div className="dashboard-section">
          {dashboard} {/* 대시보드 내용 */}
        </div>
        <div className="settings-section">
          {settings} {/* 설정 내용 */}
        </div>
      </div>
    </div>
  );
}

```

#### 동작 방식

병렬 라우팅:
사용자가 /dashboard 또는 /settings 경로로 접근할 때, 해당 라우팅 그룹의 레이아웃과 페이지가 동시에 렌더링된다.

```javascript
Case.1

* Dashboard:
/dashboard로 접근하면 DashboardLayout이 렌더링되고, 그 안에 Sidebar와 Main Dashboard Content가 표시

* 결과:
Application Header
Sidebar Content
Main Dashboard Content

==========================================================================================

* Settings:
/settings로 접근하면 SettingsLayout이 렌더링되고, 그 안에 Settings Header와 Settings Content가 표시.

* 결과:
Application Header
Settings Header
Settings Content
```

```javascript
Case.2
/dashboard 또는 /settings로 접근하면

결과:
Application Header
Dashboard Content (대시보드 관련 내용)
Settings Content (설정 관련 내용)
```

### 중첩 라우팅 vs 병렬 라우팅 차이점 요약

| 항목                       | 중첩 라우팅 (Nested Routing)                     | 병렬 라우팅 (Parallel Routing)                     |
|--------------------------|------------------------------------------------|------------------------------------------------|
| **구조**                   | 부모와 자식 관계를 통해 레이아웃을 중첩함         | 여러 경로를 독립적으로 동시에 로드함              |
| **렌더링 방식**            | 자식 라우트가 부모 라우트의 레이아웃을 상속받아 함께 렌더링 | 각 라우트가 독립적으로 렌더링되며 동시에 표시됨     |
| **사용 사례**              | 같은 레이아웃을 공유하는 여러 페이지가 있을 때 사용    | 복잡한 UI를 구성할 때, 여러 상태를 동시에 보여줄 때 사용 |
| **재사용성**               | 레이아웃과 UI 요소의 재사용이 용이                   | 각 부분이 독립적으로 관리되므로 유연성이 높음       |


### Catch-All Routes
Next.js의 Catch-All Routes를 사용하여 URL 경로를 처리할 때, 인자의 개수에 따라 다양한 방식으로 경로를 매핑할 수 있다. 예를 들어, /archive와 같은 경로는 인자가 없고, /archive/2024와 같은 경로는 하나의 인자를, /archive/2024/3과 같은 경로는 두 개의 인자를 캐치할 수 있
다. 이를 통해 복잡한 URL 구조를 유연하게 처리할 수 있다.

#### Catch-All Route 구조
* 폴더 구조:
/pages/archive/[...params].js와 같은 구조를 사용하여 Catch-All Route를 정의한다.

* 예시 코드:
```javascript
// pages/archive/[...params].js
import { useRouter } from 'next/router';

const ArchivePage = () => {
  const router = useRouter();
  const { params } = router.query; // params는 배열입니다.

  return (
    <div>
      <h1>Archive Page</h1>
      {params ? (
        <div>
          <p>Number of parameters: {params.length}</p>
          <ul>
            {params.map((param, index) => (
              <li key={index}>{param}</li>
            ))}
          </ul>
        </div>
      ) : (
        <p>No parameters</p>
      )}
    </div>
  );
};

export default ArchivePage;
```

#### 인자에 따른 캐치 차이

* 인자 없음 (/archive):
이 경우, params가 정의되지 않거나 비어 있는 배열이 되며, URL 경로에 아무런 추가 정보가 없음을 나타냄

* 인자 하나 (/archive/2024):
하나의 인자가 캐치된다. 이는 특정 연도(예: 2024)와 같은 의미를 가질 수 있다.

* 인자 두 개 (/archive/2024/3):
두 개의 인자가 캐치된다. 예를 들어, 첫 번째 인자는 연도(2024), 두 번째 인자는 월(3)으로 해석될 수 있다.


## 로딩 및 에러 상태 처리:
로딩 UI와 에러 경계를 설정하여 사용자에게 더 나은 피드백을 제공한다. 페이지 전환 시 로딩 상태를 표시하거나, 특정 페이지에서 발생하는 에러를 처리할 수 있다.

1.사용자가 /user/[id] 경로에 접근 <br/>
2.먼저 loading.js가 렌더링되어 로딩 중 메시지를 표시 <br/>
3.데이터가 성공적으로 로드되면 UserPage가 렌더링 <br/>
4.데이터 로드 중 에러가 발생하면 error.js가 렌더링되어 에러 메시지를 표시 <br/>


```javascript
app/
└── user/
    ├── loading.js
    ├── error.js
    └── [id]/page.js
```

```javascript
//app/user/loading.js:
export default function Loading() {
  return <p>Loading user data...</p>;
}

```

```javascript
//app/user/error.js:
export default function Error() {
  return <p>Failed to load user data.</p>;
}

```

```javascript
//app/user/[id]/page.js:
export default async function UserPage({ params }) {
  const userId = params.id;
  const userData = await fetchUserData(userId); // 사용자 데이터 가져오기

  if (!userData) {
    throw new Error('User not found'); // 에러 발생
  }

  return <div>{userData.name}</div>;
}

```

## Page Router
페이지 라우터는 Next.js의 전통적인 라우팅 시스템 방식이다.

### 파일 기반 라우팅:
pages 디렉토리 내의 파일 구조에 따라 자동으로 라우트가 생성된다. 예를 들어, pages/contact.js 파일은 /contact 경로에 매핑된다.

### 정적 및 동적 페이지 생성:
정적 사이트 생성(SSG) 및 서버 사이드 렌더링(SSR)을 통해 페이지를 생성할 수 있다. getStaticProps, getServerSideProps를 사용하여 데이터를 fetch 한다.

**정적 사이트 생성 (SSG)**
getStaticProps를 사용하여 정적 페이지를 생성하는 예시이다. 이 방법은 빌드 시 데이터를 미리 가져와서 정적 HTML 파일로 생성한다.

```javascript
// 정적 페이지 생성 (SSG)
export default function PostsPage({ posts }) {
  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.map(post => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
}

// 빌드 시 데이터를 가져옴
export async function getStaticProps() {
  const res = await fetch('https://jsonplaceholder.typicode.com/posts');
  const posts = await res.json();

  return {
    props: {
      posts, // 페이지에 전달할 데이터
    },
  };
}
```

**정적 사이트 생성 (SSG)**
getServerSideProps를 사용하여 서버 사이드에서 데이터를 가져오는 예시이다. 이 방법은 페이지 요청 시마다 서버에서 데이터를 가져온다.

```javascript
// 서버 사이드 렌더링 (SSR)
export default function PostPage({ post }) {
  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
    </div>
  );
}

// 페이지 요청 시 데이터를 가져옴
export async function getServerSideProps({ params }) {
  const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${params.id}`);
  const post = await res.json();

  return {
    props: {
      post, // 페이지에 전달할 데이터
    },
  };
}

```

**API Routes:** <br/>
페이지 라우터는 API 라우트를 지원하여, 서버 측에서 데이터를 처리할 수 있다. pages/api 디렉토리 내에 API 핸들러를 정의하여 RESTful API를 구축할 수 있다.

```javascript
pages/
└── api/
    └── posts.js
```

```javascript
// 포스트 API 핸들러
export default function handler(req, res) {
  // GET 요청 처리
  if (req.method === 'GET') {
    const posts = [
      { id: 1, title: 'First Post', content: 'This is the first post.' },
      { id: 2, title: 'Second Post', content: 'This is the second post.' },
    ];
    res.status(200).json(posts); // 포스트 데이터 반환
  } else {
    // 다른 HTTP 메서드에 대한 처리
    res.setHeader('Allow', ['GET']);
    res.status(405).end(`Method ${req.method} Not Allowed`);
  }
}
```

```javascript
import { useEffect, useState } from 'react';

export default function HomePage() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    // 포스트 데이터 가져오기
    fetch('/api/posts')
      .then(response => response.json())
      .then(data => setPosts(data));
  }, []);

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.map(post => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
}

```

**Custom 404 페이지:** <br/>
사용자가 잘못된 경로로 접근할 경우 표시할 커스텀 404 페이지를 쉽게 설정할 수 있다. pages/404.js 파일을 생성하여 사용자 정의 에러 페이지를 제공한다.

**Link 컴포넌트:** <br/>
내비게이션을 위한 Link 컴포넌트를 사용하여 페이지 간의 전환을 부드럽게 처리할 수 있다. Link 컴포넌트는 페이지 전환 시 성능을 최적화 한다.




# 3.서버 컴포넌트, 클라이언트 컴포넌트, SSR, CSR 총정리

### 서버 컴포넌트 (Server Component)
* 정의: 서버에서 렌더링되는 컴포넌트<br/>
* 서버에서 실행: 요청 시 서버에서 데이터를 가져와 HTML을 생성하고 클라이언트에 전달<br/>
* 상태 관리 불가: 클라이언트 상태(예: useState, useEffect)를 사용할 수 없다.<br/>
* SEO 최적화: 서버에서 생성된 HTML은 검색 엔진에 잘 노출된다.<br/>
* 비동기 데이터 패칭: API 호출을 통해 필요한 데이터를 요청할 수 있다.<br/>


### 클라이언트 컴포넌트 (Client Component)
* 정의: 클라이언트 브라우저에서 실행되는 컴포넌트<br/>
* 클라이언트에서 실행: 클라이언트 측에서 JavaScript가 실행되어 사용자 인터페이스를 동적으로 업데이트한다.<br/>
* 상태 관리 가능: useState, useEffect와 같은 훅을 사용하여 상태를 관리하고 사용자와 상호작용할 수 있다.<br/>
* API 호출: 필요한 데이터를 비동기적으로 가져오기 위해 API를 호출할 수 있다.<br/>
* 상호작용 중심: 사용자와의 상호작용(버튼 클릭, 폼 제출 등)을 처리하는 데 적합하다.<br/>

### 서버 사이드 렌더링 (SSR)
* 정의: 페이지가 요청될 때마다 서버에서 HTML을 생성하여 클라이언트에 전달하는 방식<br/>
* 요청 시 데이터 패칭: getServerSideProps를 사용하여 요청 시 필요한 데이터를 API 서버에서 가져온다<br/>
* 최신 데이터 제공: 매 요청마다 최신 데이터를 렌더링하여 사용자에게 제공한다.<br/>
* SEO 최적화: 서버에서 렌더링된 HTML은 검색 엔진이 쉽게 크롤링할 수 있다.<br/>

### 클라이언트 사이드 렌더링 (CSR)
* 정의: 클라이언트 측에서 JavaScript가 실행되어 페이지를 렌더링하는 방식<br/>
* 초기 로딩 후 렌더링: 초기 페이지 로드 후, JavaScript가 실행되어 필요한 데이터를 API를 통해 비동기적으로 가져온다.<br/>
* 상태 관리 및 상호작용: 클라이언트 컴포넌트를 통해 사용자와의 상호작용을 처리하고 상태를 관리한다.<br/>
* SEO 문제: 클라이언트 측에서 렌더링되기 때문에 초기 HTML이 검색 엔진에 노출되지 않을 수 있어 SEO에 불리할 수 있다.<br/>



| 개념               | 정의                                             | 특징                                                     |
|------------------|------------------------------------------------|--------------------------------------------------------|
| 서버 컴포넌트        | 서버에서 렌더링되는 컴포넌트                           | 서버에서 실행, 상태 관리 불가, SEO 최적화                |
| 클라이언트 컴포넌트   | 클라이언트 브라우저에서 실행되는 컴포넌트                | 클라이언트에서 실행, 상태 관리 가능, 사용자 상호작용 처리      |
| SSR              | 요청 시 서버에서 HTML을 생성하여 클라이언트에 전달하는 방식 | 요청 시 데이터 패칭, 최신 데이터 제공, SEO 최적화          |
| CSR              | 클라이언트 측에서 JavaScript가 실행되어 렌더링하는 방식   | 초기 로딩 후 렌더링, API 호출로 데이터 가져옴, SEO 문제 가능   |



## 서버 컴포넌트 vs SSR

### 차이점

| 항목                  | 서버 컴포넌트                               | 서버 사이드 렌더링 (SSR)                     |
|---------------------|------------------------------------------|-------------------------------------------|
| **정의**               | 서버 컴포넌트는 Next.js에서 제공하는 React 컴포넌트로, 서버에서만 실행되며 클라이언트에 HTML을 전달합니다. 이 컴포넌트는 클라이언트 측의 상태 관리나 상호작용을 지원x | SSR은 클라이언트의 요청이 있을 때마다 Next.js 서버에서 HTML을 생성하여 클라이언트에 전달하는 방식. 이 과정에서 getServerSideProps와 같은 메서드를 사용하여 요청 시 필요한 데이터를 패칭 |
| **실행 위치**           | Next.js서버에서 실행되며, 클라이언트에 HTML 형태로 전달된다. 서버 컴포넌트는 클라이언트 측의 자바스크립트와는 분리되어 실행된다. 즉, 클라이언트에서 React의 useState나 useEffect와 같은 훅을 사용할 수 없다 | 사용자가 특정 페이지를 요청할 때마다 Next.js 서버에서 실행된다. 이 요청은 특정 URL에 대한 HTTP 요청으로, 서버가 클라이언트의 요청에 응답하기 위해 HTML을 생성하는 과정을 포함 |
| **상태 관리**           | 클라이언트 상태 관리 불가 (React Hooks 사용 불가) | 클라이언트 측에서 상태 관리 가능 (React Hooks 사용 가능) |
| **데이터 패칭**        | API 호출을 통해 데이터를 가져와 렌더링           | 요청 시마다 Next.js서버에서 데이터를 패칭             |
| **최신 데이터 제공**    | 주로 정적 데이터 제공                          | 매 요청마다 최신 데이터를 제공                |
| **SEO 최적화**         | SEO에 유리                                   | SEO에 유리                                   |
| **사용 사례**          | 정적 콘텐츠, 블로그 포스트 등                   | 사용자 맞춤형 데이터, 실시간 피드 등          |

### 공통점

| 항목                  | 설명                                         |
|---------------------|--------------------------------------------|
| **서버에서 렌더링**     | 두 방식 모두 Next.js 서버에서 HTML을 생성하여 클라이언트에 전달됨. |
| **SEO 최적화**         | 두 방식 모두 Next.js 서버에서 생성된 HTML이 검색 엔진에 잘 노출되어 SEO에 유리함. |



### 서버 컴포넌트:
장점:<br/>
* 서버에서 직접 렌더링되므로 클라이언트로 보내는 데이터의 양이 줄어들 수 있다.
* 클라이언트 측에서 상태를 관리할 필요가 없으므로, 코드가 간결해질 수 있다.

단점:
* 클라이언트 측에서 상호작용이 필요한 경우에는 적합하지 않으며, 컴포넌트 내에서 상태를 관리할 수 없다.


### 서버 사이드 렌더링 (SSR):
장점:<br/>
* 매 요청마다 최신 데이터를 반영할 수 있으며, 사용자 맞춤형 정보를 제공할 수 있다.
* 클라이언트 측에서 상태 관리가 가능하므로, 사용자와의 상호작용이 필요한 경우 유용하다.

단점:<br/>
* 각 요청마다 서버에서 데이터를 패칭하므로, 서버 부하가 증가하고 응답 시간이 느려질 수 있다.

### 결론
서버 컴포넌트와 SSR은 각각의 용도와 특성에 따라 적절하게 선택하여 사용할 수 있다. 서버 컴포넌트는 정적 콘텐츠에 적합하고, SSR은 사용자 맞춤형 데이터나 동적 콘텐츠에 적합하다.


## 클라이언트 컴포넌트 vs CSR


| 항목                  | CSR (클라이언트 사이드 렌더링)                | 클라이언트 컴포넌트                             |
|---------------------|------------------------------------------|---------------------------------------------|
| **정의**               | 클라이언트에서 JavaScript를 통해 HTML을 생성하는 방식으로, 초기 로딩 후 모든 렌더링과 데이터 패칭이 클라이언트에서 이루어짐. | 클라이언트 브라우저에서 실행되는 React 컴포넌트로, 사용자 상호작용 및 상태 관리를 지원함. |
| **실행 위치**           | 클라이언트 브라우저에서 JavaScript가 실행되어 HTML을 렌더링. 초기 페이지 로드 이후 모든 작업은 클라이언트에서 수행됨. | 클라이언트에서만 실행되며, React의 다양한 훅을 사용하여 사용자와의 상호작용을 처리. |
| **데이터 패칭**        | 클라이언트 측에서 API 호출을 통해 데이터를 가져오며, 사용자 행동에 따라 동적으로 데이터를 업데이트. | API 호출 또는 부모 컴포넌트로부터 props를 통해 데이터를 전달받아 사용. |
| **SEO 최적화**         | 초기 페이지에는 HTML 콘텐츠가 없기 때문에 검색 엔진 최적화(SEO)에 불리함. JavaScript가 실행된 후에 콘텐츠가 생성됨. | 클라이언트 컴포넌트도 CSR의 일환이므로 SEO에 불리함. 초기 HTML에는 콘텐츠가 포함되지 않음. |
| **사용 사례**          | SPA(Single Page Application)에서 주로 사용되며, 동적 페이지 업데이트가 필요한 애플리케이션에 적합. | 사용자 상호작용이 필요한 동적 UI 컴포넌트에 적합. 예: 폼 입력, 버튼 클릭 등의 이벤트 처리. |
| **장점**               | 동적 콘텐츠 렌더링에 유리하고, 사용자 경험을 향상시킬 수 있음. | 사용자의 입력에 즉각적으로 반응하며, 상태 관리가 용이함. |
| **단점**               | SEO에 불리하고, 초기 로딩 속도 문제 발생할 수 있음. | 클라이언트 측에서만 실행되므로, 서버 측 렌더링의 장점이 없음. |

# 4.보호된 파일
보호된 파일(Protected Files)은 각 파일이 특정한 역할을 수행하며, 애플리케이션의 구조와 동작 방식을 정의하는 중요한 요소들이다.

### page.js
* 설명: 해당 라우팅 경로에 접속했을 때 기본적으로 표시되는 유니크한 페이지를 정의한다.
* 예시: app/dashboard/page.js는 /dashboard 경로에 접속할 때 보여지는 대시보드 페이지이다.

### layout.js
* 설명: 여러 하위 페이지에 공통적으로 적용되는 레이아웃을 정의하는 파일이다.
* 예시: app/dashboard/layout.js는 대시보드 하위 페이지들(예: /dashboard/overview, /dashboard/settings)에 공통적으로 적용되는 레이아웃을 제공한다.

### not-found.js
* 설명: 존재하지 않는 페이지에 접근했을 때 표시되는 오류 페이지를 정의한다.
* 예시: 사용자가 /invalid-route에 접근할 때 보여지는 "페이지를 찾을 수 없습니다" 메시지를 포함한다.

### error.js
* 설명: 예상치 못한 오류가 발생했을 때 표시되는 오류 페이지를 정의한다.
* 예시: 서버 오류가 발생했을 때 사용자에게 보여주는 오류 메시지를 포함한다.

### loading.js
* 설명: 페이지 로딩 중에 표시되는 로딩 페이지를 정의한다.
* 예시: 데이터가 로드되는 동안 사용자가 볼 수 있는 스피너나 로딩 메시지를 포함한다.

## route.js
* 설명: 서버 측 라우팅을 위한 코드를 포함하는 파일이다. API 라우트를 정의하는 데 사용된다.
* 예시: app/api/users/route.js는 사용자 정보를 가져오는 API 엔드포인트를 정의한다.

## default.js
* 설명: 특정 라우팅 경로에 대한 기본 페이지 역할을 하는 파일이다.
* 예시: / 경로에 대한 기본 페이지를 설정할 수 있다.

## instrumentation.js
* 설명: 코드 분석 및 디버그, 성능 추적을 위한 코드를 포함하는 파일이다.
* 예시: 앱의 성능을 모니터링하고 분석하기 위한 코드가 포함될 수 있다.

## middleware.js
* 설명: 페이지 요청 및 응답을 가로채고 처리하는 미들웨어 함수를 포함하는 파일이다.
* 예시: 인증 확인, 로깅 또는 요청 변환 등의 작업을 수행할 수 있다.

## template.js
* 설명: 페이지 템플릿을 정의하는 파일이다.
* 예시: 여러 페이지에 공통적으로 적용되는 템플릿을 정의하여 재사용성을 높인다.

## _app.js
* 설명: Next.js 애플리케이션의 전역 레이아웃을 정의하는 파일이다. 모든 페이지에 공통적으로 적용되는 설정이나 레이아웃을 설정한다.
* 예시: 전역 스타일, 테마, 상단 내비게이션 바 등을 포함할 수 있다.

## getStaticProps.js
* 설명: 정적 사이트 생성(SSG)을 위해 서버 측에서 데이터를 패치하고 페이지를 렌더링하는 함수를 정의한다.
* 예시: 빌드 시 API에서 데이터를 가져와 정적 페이지를 생성한다.

## getServerSideProps.js
* 설명: 서버 측에서 데이터를 패치하고 페이지를 렌더링하기 전에 실행되는 함수를 정의한다.
* 예시: 사용자의 요청에 따라 실시간 데이터를 가져와 페이지를 생성한다.

# 5.이미지 최적화
Next.js에서 제공하는 Image 컴포넌트는 이미지 최적화를 위해 여러 가지 기능을 지원한다. 이를 통해 성능을 향상시키고 사용자 경험을 개선할 수 있다.

### 지연 로딩 (Lazy Loading):
loading="lazy" 속성을 통해 이미지가 뷰포트에 들어오기 전까지 로드되지 않도록 설정할 수 있다. 이는 초기 페이지 로딩 속도를 개선하고, 사용자에게 필요한 이미지만 로드하여 대역폭을 절약한다.

### 자동 크기 조정:
srcset 속성은 다양한 화면 크기에 맞게 이미지를 자동으로 조정한다. 예를 들어, 모바일 기기와 데스크톱 기기에서 각각 최적의 크기의 이미지를 로드하도록 지원한다.

### 포맷 변환:
Next.js는 브라우저의 지원 여부에 따라 이미지를 .webp와 같은 최적화된 포맷으로 변환하여 제공할 수 있다. 이는 이미지 로딩 속도를 더 개선할 수 있다.

### 우선순위 설정:
priority 속성을 사용하면 특정 이미지를 우선적으로 로드할 수 있다. 이 속성이 설정된 이미지는 지연 로딩이 적용되지 않으며, 페이지가 로드될 때 즉시 렌더링된다.

### 개발자 도구에서의 렌더링
```javascript
import Image from 'next/image';
import Link from 'next/link';
import classes from './main-header.module.css';

export default function MainHeader() {
  return (
    <header>
      <Link className={classes.logo} href="/">
        <Image
          src={logoImg}
          alt="A plate with food on it"
          loading="lazy" // 지연 로딩 설정
          width={1024} // 이미지 너비
          height={1024} // 이미지 높이
          priority // 우선 로딩 설정
        />
        NextLevel Food
      </Link>
    </header>
  );
}
```
개발자 도구에서 렌더링된 <img> 요소를 보면 다음과 같은 속성을 확인할 수 있다 <br/>
* loading="lazy": 지연 로딩이 적용되었음을 나타낸다.
* srcset: 다양한 해상도에 맞는 이미지를 제공하는 속성이다.
* src: 최적화된 이미지의 URL이 포함되어 있다.

# 6.Server Actions
Server Actions는 Next.js 13.4에서 도입된 기능으로, 클라이언트에서 서버의 함수를 직접 호출할 수 있게 해주는 기능이다.

```javascript
// app/actions.js
'use server'

export async function createTodo(formData) {
  const title = formData.get('title')
  await db.todo.create({ title })
}
```

```javascript
// app/components/TodoForm.js
'use client'

import { createTodo } from '../actions'

export default function TodoForm() {
  return (
    <form action={createTodo}>
      <input type="text" name="title" />
      <button type="submit">Add Todo</button>
    </form>
  )
}
```
### 장점:
* 보안: 서버 코드가 클라이언트에 노출되지 않음
* 성능: 클라이언트-서버 통신 최적화
* 개발 경험: API 엔드포인트 생성 없이 서버 함수 직접 호출

### 사용 사례:
* 폼 제출
* 데이터베이스 조작
* 파일 업로드
* 인증/인가 처리

# 7.Metadata API
Metadata API는 Next.js 13에서 도입된 새로운 메타데이터 관리 시스템으로, SEO와 소셜 미디어 공유를 위한 메타데이터를 더 쉽게 관리할 수 있게 해준다.

#### 정적 메타데이터
```javascript
// app/layout.js
export const metadata = {
  title: 'My Website',
  description: 'Welcome to my website',
  openGraph: {
    title: 'My Website',
    description: 'Welcome to my website',
    images: [
      {
        url: 'https://example.com/og.jpg',
        width: 1200,
        height: 630,
      },
    ],
  },
  twitter: {
    card: 'summary_large_image',
    title: 'My Website',
    description: 'Welcome to my website',
    images: ['https://example.com/og.jpg'],
  },
}
```

#### 동적 메타데이터

 Next.js 13부터는 generateMetadata 함수를 페이지 컴포넌트와 같은 파일에 export하기만 하면 자동으로 메타데이터가 적용된다.

```javascript
// app/products/[id]/page.tsx
export async function generateMetadata({ params }) {
  const post = await getPost(params.id)
  
  return {
    title: post.title,
    description: post.excerpt,
    openGraph: {
      title: post.title,
      description: post.excerpt,
      images: [post.ogImage],
    },
    twitter: {
      card: 'summary_large_image',
      title: post.title,
      description: post.excerpt,
      images: [post.ogImage],
      creator: '@yourTwitterHandle',   
      site: '@yourWebsiteTwitter',     
    },
  }
}
```

* 1.Next.js가 자동으로 generateMetadata 함수를 인식
* 2.페이지가 요청될 때 메타데이터를 생성
* 3.생성된 메타데이터를 페이지의 <head> 섹션에 자동으로 주입

별도의 호출이나 설정 없이도 메타데이터가 적용된다. 이는 Next.js의 파일 기반 라우팅 시스템과 자동 메타데이터 주입 기능 덕분이다.

# 8.Streaming SSR
Streaming SSR은 Next.js 13에서 도입된 기능으로, 서버에서 렌더링된 HTML을 점진적으로 클라이언트에 전송하는 방식이다.
설명만 보면 기존의 SSR과의 차이점을 느끼기가 힘든데, 차이점은 아래와 같다.

#### 기존 SSR
```javascript
// 기존 SSR 방식
export default async function ProductPage() {
  // 모든 데이터를 기다린 후에야 페이지 렌더링 시작
  const product = await fetchProduct()     // 1초 소요
  const reviews = await fetchReviews()     // 2초 소요
  const recommendations = await fetchRecommendations() // 3초 소요

  // 총 6초 후에야 사용자에게 페이지가 보임
  return (
    <div>
      <ProductInfo product={product} />
      <Reviews reviews={reviews} />
      <Recommendations recommendations={recommendations} />
    </div>
  )
}
```
#### Streaming SSR

```javascript
// Streaming SSR 방식
import { Suspense } from 'react'

export default async function ProductPage() {
  // ProductInfo는 즉시 렌더링 (1초)
  const product = await fetchProduct()

  return (
    <div>
      <ProductInfo product={product} />
      
      {/* Reviews는 독립적으로 로딩 (2초) */}
      <Suspense fallback={<ReviewsSkeleton />}>
        <Reviews />
      </Suspense>

      {/* Recommendations도 독립적으로 로딩 (3초) */}
      <Suspense fallback={<RecommendationsSkeleton />}>
        <Recommendations />
      </Suspense>
    </div>
  )
}
```

#### 렌더링 방식
* 기존 SSR: 모든 데이터가 준비될 때까지 기다린 후 한 번에 페이지 전체를 렌더링
* Streaming SSR: 준비된 컴포넌트부터 순차적으로 렌더링하고 스트리밍

#### 사용자 경험
```javascript
// 기존 SSR의 타임라인
0초: 빈 화면
6초: 전체 페이지 표시

// Streaming SSR의 타임라인
0초: 로딩 UI 표시
1초: ProductInfo 표시
2초: Reviews 표시
3초: Recommendations 표시
```

#### 시각적 비교
```javascript
// 기존 SSR
┌────────────────────┐
│                    │
│     Loading...     │ (6초 동안)
│                    │
└────────────────────┘
           ↓
┌────────────────────┐
│    Product Info    │
│      Reviews       │ (6초 후 한번에 표시)
│  Recommendations   │
└────────────────────┘

// Streaming SSR
┌────────────────────┐
│    Product Info    │ (1초)
│    Loading...      │
│    Loading...      │
└────────────────────┘
           ↓
┌────────────────────┐
│    Product Info    │
│     Reviews        │ (2초)
│    Loading...      │
└────────────────────┘
           ↓
┌────────────────────┐
│    Product Info    │
│     Reviews        │ (3초)
│  Recommendations   │
└────────────────────┘
```

#### 장점:
* 더 빠른 초기 로딩
* 더 나은 사용자 경험
* 서버 리소스 효율적 사용
* 점진적인 페이지 로드

#### 단점:
* 구현 복잡도 증가
* 컴포넌트 설계 신중 필요
* 로딩 상태 관리 필요
* Streaming SSR은 특히 데이터 의존성이 많은 큰 페이지에서 사용자 경험을 크게 개선할 수 있습니다.
