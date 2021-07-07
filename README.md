[![SWR](https://assets.vercel.com/image/upload/v1572289618/swr/banner.png)](https://swr.vercel.app)

<p align="center">

  <a aria-label="Vercel logo" href="https://vercel.com">
    <img src="https://badgen.net/badge/icon/Made%20by%20Vercel?icon=zeit&label&color=black&labelColor=black">
  </a>
  <br/>

  <a aria-label="NPM version" href="https://www.npmjs.com/package/swr">
    <img alt="" src="https://badgen.net/npm/v/swr">
  </a>
  <a aria-label="Package size" href="https://bundlephobia.com/result?p=swr">
    <img alt="" src="https://badgen.net/bundlephobia/minzip/swr">
  </a>
  <a aria-label="License" href="https://github.com/zeit/swr/blob/master/LICENSE">
    <img alt="" src="https://badgen.net/npm/license/swr">
  </a>
</p>

## 소개

[swr.vercel.app](https://swr.vercel.app)

SWR은 원격 데이터를 가져오기 위한 React Hooks 라이브러리 입니다.

'**SWR**'이라는 이름은 [HTTP RFC 5861](https://tools.ietf.org/html/rfc5861)에서 널리 사용되는 캐시 무효화 전략인 'stale-while-revalidate'에서 파생되었습니다.
**SWR**은 먼저 캐시에서 데이터를 반환 (stale) 한 다음, 가져오기 요청을 전송(revalidate)하고, 마지막으로 최신 데이터를 다시 제공합니다.

특징:

- 전송 및 프로토콜에 구애받지 않는 데이터 가져오기 (Transport and protocol agnostic data fetching)
- 빠른 페이지 탐색
- 초점 재 검증
- 인터벌 폴링
- 중복 제거 요청(Request deduplication)
- Local mutation
- 페이지네이션(Pagination)
- TypeScript 준비
- SSR 지원
- 서스펜스 모드
- React Native 지원
- 최소한의 API


...외에도 많이 있습니다.

SWR을 사용하면 구성 요소가 **데이터 스트림을 지속적으로 자동 업데이트**합니다. 따라서 UI는 항상 **빠르고** ** 반응적**입니다.

<br/>

## 빠른 시작

```js
import useSWR from 'swr'

function Profile() {
  const { data, error } = useSWR('/api/user', fetcher)

  if (error) return <div>failed to load</div>
  if (!data) return <div>loading...</div>
  return <div>hello {data.name}!</div>
}
```

이 예제에서 React Hook `useSWR`은 `key`와 `fetcher` 함수를 받습니다.
`key`는 요청의 고유 식별자이며, 일반적으로 API의 URL입니다. 그리고 `fetcher`는 `key`를 매개 변수로 받아들이고, 데이터를 비동기적으로 반환합니다.

`useSWR`은 `data`와 `error`의 2개 값도 반환합니다. 요청(fetcher)이 아직 완료되지 않은 경우 `data`는 `undefined`가 됩니다. 그리고 응답을 받으면 `fetcher`의 결과에 따라 `data`와 `error`를 설정하고 구성 요소를 다시 렌더링합니다.

`fetcher`는 모든 비동기 함수가 될 수 있으므로, 선호하는 데이터 가져오기(data-fetching) 라이브러리를 사용하여 해당 부분을 처리 할 수 있습니다.

SWR의 더 많은 데모는 [swr.vercel.app](https://swr.vercel.app)을 확인하고 권장 사항은 [예제](#examples)를 확인하세요.

<br/>

## 용례

React 프로젝트 디렉토리 내에서 다음을 실행하십시오.

```
yarn add swr
```

npm으로는 다음과 같이 실행 가능합니다.

```
npm install swr
```

### API

```js
const { data, error, isValidating, mutate } = useSWR(key, fetcher, options)
```

#### 매개 변수 (Parameters)

- `key`: 요청에 대한 고유 키 문자열 (또는 함수 / 배열 / null) [(고급 용례)](#conditional-fetching)
- `fetcher`: (선택적) 데이터를 가져오는 Promise 반환 함수 [(details)](#data-fetching)
- `options`: (선택적) 이 SWR 훅에 대한 옵션 객체

#### 반환 값

- `data`: 주어진 키에 대한 데이터는 `fetcher`에 의해 확인됩니다 (로드되지 않은 경우 undefined).
- `error`: `fetcher`에서 발생한 오류 (또는 undefined)
- `isValidating`: 요청 또는 재검증 로딩이 있는 경우
- `mutate(data?, shouldRevalidate?)`: 캐시된 데이터를 변경하는 함수

#### 옵션

- `suspense = false`: React Suspense 모드 활성화 [(details)](#suspense-mode)
- `fetcher = window.fetch`: 기본(default) fetcher 함수
- `initialData`: 반환할 초기 데이터 (참고: This is per-hook)
- `revalidateOnMount`: 구성 요소가 마운트 될 때 자동 재검증 활성화 또는 비활성화 (기본적으로 재검증은 initialData가 설정되지 않은 경우 마운트시 발생합니다. 이 플래그를 사용하여 동작을 강제하십시오.)
- `revalidateOnFocus = true`: 창에 초점이 맞춰지면 자동 재 검증
- `revalidateOnReconnect = true`: 브라우저가 네트워크 연결을 회복하면 자동으로 재 검증 (`navigator.onLine` 를 통해)
- `refreshInterval = 0`: 폴링 간격 (기본적으로 비활성화 됨)
- `refreshWhenHidden = false`: 창이 보이지 않을 때 폴링 (`refreshInterval`이 활성화 된 경우)
- `refreshWhenOffline = false`: 브라우저가 오프라인일 때 폴링 (`navigator.onLine`에 의해 결정됨)
- `shouldRetryOnError = true`: fetcher가 error가 있을 때 재시도 [(details)](#error-retries)
- `dedupingInterval = 2000`: 이(this) 시간 범위에서 동일한 키를 사용하여 중복 제거 요청
- `focusThrottleInterval = 5000`: 기간 동안 한 번만 재 검증
- `loadingTimeout = 3000`: onLoadingSlow 이벤트를 트리거하는 시간 초과
- `errorRetryInterval = 5000`: 오류 재시도 간격 [(details)](#error-retries)
- `errorRetryCount`: 최대 오류 재시도 횟수 [(details)](#error-retries)
- `onLoadingSlow(key, config)`: 요청이 로드되는데 너무 오래 걸리는 때 콜백 함수 (see `loadingTimeout`)
- `onSuccess(data, key, config)`: 요청이 성공적으로 완료될 때 콜백 함수
- `onError(err, key, config)`: 요청이 오류를 반환 할 때 콜백 함수
- `onErrorRetry(err, key, config, revalidate, revalidateOps)`: [오류 재시도](#error-retries) 를 위한 핸들러
- `compare(a, b)`: 스퓨리어스 리-렌더를 피하기 위해 반환된 데이터가 언제 변경되었는지 감지하는 데 사용되는 비교 함수. 기본적으로 [`dequal/lite`](https://github.com/lukeed/dequal)이 사용됩니다.
- `isPaused()`: 재 검증 일시 중지 여부를 감지하는 함수는 `true`를 반환 할 때 가져온 데이터 및 오류를 무시합니다. 기본적으로 `false`를 반환합니다.

느린 네트워크 (2G, <= 70Kbps)에서 `errorRetryInterval`은 10초가 됩니다. `loadingTimeout`은 기본적으로 5초 입니다.

[글로벌 구성](#global-configuration)을 사용하여 기본 옵션을 제공할 수도 있습니다.

<br/>

## 예시

- [Global Configuration](#global-configuration)
- [Data Fetching](#data-fetching)
- [Conditional Fetching](#conditional-fetching)
- [Dependent Fetching](#dependent-fetching)
- [Multiple Arguments](#multiple-arguments)
- [Manually Revalidate](#manually-revalidate)
- [Mutation and Post Request](#mutation-and-post-request)
- [Mutate Based on Current Data](#mutate-based-on-current-data)
- [Returned Data from Mutate](#returned-data-from-mutate)
- [SSR with Next.js](#ssr-with-nextjs)
- [Suspense Mode](#suspense-mode)
- [Error Retries](#error-retries)
- [Prefetching Data](#prefetching-data)
- [Request Deduplication](#request-deduplication)

### 전역 구성 (Global Configuration)

The context `SWRConfig` can provide global configurations (`options`) for all SWR hooks.
context `SWRConfig`는 모든 SWR Hooks에 대한 전역 구성(`options`)을 제공 할 수 있습니다.

이 예에서 모든 SWR은 제공된 동일한 fetcher를 사용하여 JSON 데이터를 로드하고, 기본적으로 3초마다 새로고침을 합니다.

```js
import useSWR, { SWRConfig } from 'swr'

function Dashboard() {
  const { data: events } = useSWR('/api/events')
  const { data: projects } = useSWR('/api/projects')
  const { data: user } = useSWR('/api/user', { refreshInterval: 0 }) // don't refresh
  // ...
}

function App() {
  return (
    <SWRConfig
      value={{
        refreshInterval: 3000,
        fetcher: (...args) => fetch(...args).then(res => res.json())
      }}
    >
      <Dashboard />
    </SWRConfig>
  )
}
```

### 데이터 가져오기 (Data Fetching)

`fetcher`는 SWR의 **`key`**를 받아들이고 값 또는 Promise를 반환하는 함수입니다.
모든 라이브러리를 사용하여 데이터 가져 오기를 처리 할 수 있습니다. 예를 들면 다음과 같습니다.

```js
import fetch from 'unfetch'

const fetcher = url => fetch(url).then(r => r.json())

function App() {
  const { data } = useSWR('/api/data', fetcher)
  // ...
}
```

또는 GraphQL에서:

```js
import { request } from 'graphql-request'

const fetcher = query => request('/api/graphql', query)

function App() {
  const { data, error } = useSWR(
    `{
      Movie(title: "Inception") {
        releaseDate
        actors {
          name
        }
      }
    }`,
    fetcher
  )
  // ...
}
```

GraphQL 쿼리에 변수를 전달하려면 [Multiple Arguments](#multiple-arguments)를 확인하십시오.

'fetcher'가 전역적으로 제공되는 경우, 매개 변수에서 생략할 수 있습니다.

### 조건부 가져 오기 (Conditional Fetching)

`null`을 사용하거나, `useSWR`에 `key`로 함수를 전달하여 조건부로 데이터를 가져옵니다. 함수에서 오류가 발생하거나 잘못된 값을 반환하면, SWR이 요청을 취소합니다.

```js
// conditionally fetch
const { data } = useSWR(shouldFetch ? '/api/data' : null, fetcher)

// ...or return a falsy value
const { data } = useSWR(() => shouldFetch ? '/api/data' : null, fetcher)

// ... or throw an error when user.id is not defined
const { data } = useSWR(() => '/api/data?uid=' + user.id, fetcher)
```

### 종속 가져 오기(Dependent Fetching)

SWR을 사용하면 다른 데이터에 의존하는 데이터를 가져올 수도 있습니다. 다음 데이터 가져 오기를 위해 동적 데이터가 필요할 때, 직렬 가져 오기뿐 아니라 (waterfalls을 방지하는) 가능한 최대 병렬 처리를 보장합니다.

```js
function MyProjects() {
  const { data: user } = useSWR('/api/user')
  const { data: projects } = useSWR(() => '/api/projects?uid=' + user.id)
  // When passing a function, SWR will use the return
  // value as `key`. If the function throws or returns
  // falsy, SWR will know that some dependencies are not
  // ready. In this case `user.id` throws when `user`
  // isn't loaded.

  if (!projects) return 'loading...'
  return 'You have ' + projects.length + ' projects'
}
```

### 여러 인수 (Multiple Arguments)

일부 시나리오에서, `fetcher` 함수에 여러 인수(모든 값 또는 객체일 수 있음)를 전달하는 것이 유용합니다. 예를 들면:

```js
useSWR('/api/user', url => fetchWithToken(url, token))
```

이것은 **틀렸습니다**. 데이터의 식별자(이며 캐시의 인덱스)가 `'/api/user'`이기 때문에, `토큰`이 변경되더라도 SWR은 여전히 동일한 키를 가지고 잘못된 데이터를 반환합니다.

대신, `fetcher`의 여러 인수를 포함하는 **배열**을 `key` 매개 변수로 사용할 수 있습니다.

```js
const { data: user } = useSWR(['/api/user', token], fetchWithToken)

// ... 그리고 다른 쿼리에 인수로 전달
const { data: orders } = useSWR(user ? ['/api/orders', user] : null, fetchWithUser)
```

이제 요청의 핵심은 두 값의 조합입니다. SWR **shallowly**는 모든 렌더에서 인수를 비교하고, 변경된 항목이 있으면 재 검증을 트리거합니다.
렌더링 할 때마다 개체가 다른 개체로 취급되므로, 개체를 다시 만들지 않아야 합니다.

```js
// 이러지 마세요! 렌더링 할 때마다 Deps가 변경됩니다.
useSWR(['/api/user', { id }], query)

// 대신, "안정된" 값만 전달해야 합니다.
useSWR(['/api/user', id], (url, id) => query(url, { id }))
```

Dan Abramov는 [다음 블로그 게시물](https://overreacted.io/a-complete-guide-to-useeffect/#but-i-cant-put-this-function-inside-an-effect)에서 종속성을 매우 잘 설명합니다.

### 수동 재 검증 (Manually Revalidate)

`mutate(key)`를 호출하여 동일한 키를 가진 모든 SWR에 재 검증 메시지를 전역적으로 브로드 캐스트 할 수 있습니다.

이 예제는 사용자가 "로그 아웃" 버튼을 클릭 할 때 로그인 정보 (예 :`<Profile />` 내부)를 자동으로 다시 가져 오는 방법을 보여줍니다.

```js
import useSWR, { mutate } from 'swr'

function App() {
  return (
    <div>
      <Profile />
      <button onClick={() => {
        // 쿠키를 만료로 설정
        document.cookie = 'token=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;'

        // 이 키가 있는 모든 SWR에 재 검증하도록 지시
        mutate('/api/user')
      }}>
        Logout
      </button>
    </div>
  )
}
```

### 변경과 Post 요청 (Mutation and Post Request)

대부분의 경우, 데이터에 로컬 변경을 적용하는 것은 변화를 더 빠르게 느끼게 만드는 좋은 방법입니다. - 데이터의 원격 소스를 기다릴 필요가 없기 때문입니다.

`mutate`를 사용하면, 프로그래밍 방식으로 로컬 데이터를 업데이트하는 동시에 유효성을 다시 검사하고, 최종적으로 (기존) 데이터를 최신 데이터로 바꿀 수 있습니다.

```js
import useSWR, { mutate } from 'swr'

function Profile() {
  const { data } = useSWR('/api/user', fetcher)

  return (
    <div>
      <h1>My name is {data.name}.</h1>
      <button onClick={async () => {
        const newName = data.name.toUpperCase()
        
        // 로컬 데이터를 즉시 업데이트하되, 재 검증을 비활성화합니다.
        mutate('/api/user', { ...data, name: newName }, false)
        
        // API에 소스 업데이트 요청을 보냅니다.
        await requestUpdateUsername(newName)
        
        // 로컬 데이터가 올바른지 확인하기 위해 재 검증 (refetch)을 트리거합니다.
        mutate('/api/user')
      }}>Uppercase my name!</button>
    </div>
  )
}
```

위의 예에서 버튼을 클릭하면 원격 데이터를 수정하고, 클라이언트 데이터를 로컬로 업데이트하고, 최신 데이터를 가져오도록 (재 검증) POST 요청을 보냅니다.

그러나 많은 POST API는 업데이트된 데이터를 직접 반환하기 때문에 유효성을 재 검증 할 필요가 없습니다. 다음은 "local mutate-request-update" 사용법을 보여주는 예입니다.

```js
mutate('/api/user', newUser, false)      // 재 검증없이 변경하려면 `false` 사용
mutate('/api/user', updateUser(newUser)) // `updateUser` 는 업데이트 된 문서를 반환하는 
                                         // request의 Promise 입니다.
```

### 현재 데이터 기반 뮤테이트 (Mutate Based on Current Data)

대부분의 경우, API에서 단일 값을 받고 그 목록을 업데이트 하려고 합니다.

`mutate`를 사용하면, 현재 캐시된 값을 수신하고 업데이트 된 문서를 반환 할 수 있는 비동기 함수를 전달할 수 있습니다.

```js
mutate('/api/users', async users => {
  const user = await fetcher('/api/users/1')
  return [user, ...users.slice(1)]
})
```

### Mutate에서 반환된 데이터

대부분의 경우, 캐시를 업데이트 하려면 데이터가 필요합니다. 데이터는 `mutate`에 전달한 promise 또는 비동기 함수에서 확인되거나 반환됩니다.

이 함수는 `mutate`가 해당 캐시 값을 업데이트 할 수 있도록 업데이트된 문서를 반환합니다. 호출할 때마다, 어떤 방식으로도 오류가 발생할 수 있습니다.

```js
try {
  const user = await mutate('/api/user', updateUser(newUser))
} catch (error) {
  // 여기에서 사용자를 업데이트하는 동안 오류 처리
}
```

### 바인딩 된 `mutate()`

`useSWR`이 반환하는 SWR 객체에는 SWR의 키에 미리 바인딩 된 `mutate()` 함수도 포함되어 있습니다.

전역 `mutate` 함수와 기능적으로 동일하지만, `key` 매개 변수가 필요하지 않습니다.

```js
import useSWR from 'swr'

function Profile() {
  const { data, mutate } = useSWR('/api/user', fetcher)

  return (
    <div>
      <h1>My name is {data.name}.</h1>
      <button onClick={async () => {
        const newName = data.name.toUpperCase()
        // 즉시 로컬 데이터 업데이트
        mutate({ ...data, name: newName }, false)
        // API에 데이터 업데이트 요청을 보냅니다.
        await requestUpdateUsername(newName)
        // 재검증 (refetch)
        // 참고: useSWR의 mutate를 사용할 때는 키가 필요하지 않습니다.
        mutate()
      }}>Uppercase my name!</button>
    </div>
  )
}
```

### Next.js를 사용한 SSR

`initialData` 옵션을 사용하면 초기 값을 Hook에 전달합니다. [Next.js](https://github.com/zeit/next.js)의 `getServerSideProps`와 같은 많은 SSR 솔루션과 완벽하게 작동합니다.

```js
export async function getServerSideProps() {
  const data = await fetcher('/api/data')
  return { props: { data } }
}

function App(props) {
  const initialData = props.data
  const { data } = useSWR('/api/data', fetcher, { initialData })

  return <div>{data}</div>
}
```

여전히 서버 사이드 렌더링 사이트이지만, 클라이언트 측의 SWR에 의해 완전히 구동됩니다.
이는 데이터가 동적일 수 있으며 시간 및 사용자 상호 작용에 따라 자체적으로 업데이트 될 수 있음을 의미합니다.

### 서스펜스 모드 (Suspense Mode)

React Suspense와 함께 SWR을 사용하려면`suspense` 옵션을 활성화 할 수 있습니다.

```js
import { Suspense } from 'react'
import useSWR from 'swr'

function Profile() {
  const { data } = useSWR('/api/user', fetcher, { suspense: true })
  return <div>hello, {data.name}</div>
}

function App() {
  return (
    <Suspense fallback={<div>loading...</div>}>
      <Profile/>
    </Suspense>
  )
}
```

서스펜스 모드에서 `data`는 항상 fetch response 이므로, `undefined` 인지 확인할 필요가 없습니다.
그러나 오류가 발생하면 오류를 포착하기 위해 [error boundary](https://reactjs.org/docs/concurrent-mode-suspense.html#handling-errors)를 사용해야합니다.

참고: SSR 모드에서는 서스펜스가 지원되지 않습니다.

### 오류 재시도 (Error Retries)

기본적으로 SWR은 [exponential backoff algorithm](https://en.wikipedia.org/wiki/Exponential_backoff)을 사용하여 오류 재 시도를 처리합니다. 소스 코드에서 더 많은 것을 읽을 수 있습니다.

동작을 재정의(override) 할 수도 있습니다.

```js
useSWR(key, fetcher, {
  onErrorRetry: (error, key, option, revalidate, { retryCount }) => {
    if (retryCount >= 10) return
    if (error.status === 404) return

    // 5초 후 재시도
    setTimeout(() => revalidate({ retryCount: retryCount + 1 }), 5000)
  }
})
```

### 데이터 미리 가져 오기 (Prefetching Data)

SWR에 대한 데이터를 미리 가져 오는 방법은 여러 가지가 있습니다. 최상위 요청의 경우 [`rel="preload"`](https://developer.mozilla.org/en-US/docs/Web/HTML/Preloading_content)를 적극 권장합니다.

```html
<link rel="preload" href="/api/data" as="fetch" crossorigin="anonymous">
```

이것은 JavaScript가 다운로드를 시작하기 전에 데이터를 미리 가져옵니다. 그리고 들어오는 fetch requests는 (물론 SWR를 포함하여) 결과를 재사용합니다

또 다른 선택은 조건부로 데이터를 prefetch 하는 것입니다. 캐시를 다시 가져오고 설정하는 함수를 가질 수 있습니다.

```js
function prefetch() {
  mutate('/api/data', fetch('/api/data').then(res => res.json()))
  // 두 번째 매개 변수는 Promise 입니다.
  // SWR will use the result when it resolves
}
```

그리고 **리소스**를 미리 로드해야 할 때 사용합니다 (예: [hovering](https://github.com/GoogleChromeLabs/quicklink) [a](https://github.com/guess-js/guess) [link](https://instant.page)).
Next.js의 [페이지 미리 가져 오기]와 같은 기술과 함께 다음 페이지와 데이터를 즉시 로드할 수 있습니다.

### 중복 제거 요청 (Request Deduplication)

SWR은 기본적으로 요청을 중복 제거합니다. 동일한 키로 Hook을 여러 번 호출하면, 요청이 하나만 생성됩니다. 중복된 호출은 캐시에서 값을 받습니다.
여기, `api/user` 키는 두 가지 요청에 사용됩니다.

```js
import useSWR from 'swr'

function UserProfileName() {
  const { data, error } = useSWR('/api/user', fetcher)

  if (error) return <div>failed to load</div>
  if (!data) return <div>loading...</div>
  return <p>Name: {data.name}!</p>
}

function UserProfileAvatar() {
  const { data, error } = useSWR('/api/user', fetcher)

  if (error) return <div>failed to load</div>
  if (!data) return <div>loading...</div>
  return <img src={data.avatarUrl} alt="Profile image" />
}

export default function App() {
  return (
    <div>
      <UserProfileName />
      <UserProfileAvatar />
    </div>
  )
}
```

기본적으로 2초 이내에 이루어진 요청은 중복 제거됩니다. `dedupingInterval` 옵션을 설정하여 변경할 수 있습니다.

```js
const { data, error } = useSWR('/api/user', fetcher, { dedupingInterval: 1000 })
```

이렇게 하면 1초 간격으로 요청이 중복 제거됩니다.
<br/>

## Authors

- Shu Ding ([@shuding_](https://twitter.com/shuding_)) – [Vercel](https://vercel.com)
- Guillermo Rauch ([@rauchg](https://twitter.com/rauchg)) – [Vercel](https://vercel.com)
- Joe Haddad ([@timer150](https://twitter.com/timer150)) - [Vercel](https://vercel.com)
- Paco Coursey ([@pacocoursey](https://twitter.com/pacocoursey)) - [Vercel](https://vercel.com)

멋진 `swr` npm 패키지 이름을 제공해 주신 Ryan Chen에게 감사드립니다!

<br/>

## 라이센스 (License)

The MIT License.
