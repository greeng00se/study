### pinia-plugin-persistedstate

- pinia로 상태 관리를 하는 경우 화면을 새로고침 할 때 store 안에 존재하는 값들이 초기화 된다.
- 이 때 로컬 스토리지를 이용하여 상태를 유지할 수 있는데 이를 간편하게 해주는 플러그인이다.
- vuex의 vuex-persistedstate와 유사하다.

### 설치 및 설정 방법

```json
yarn add pinia-plugin-persistedstate
```

### 로컬 스토리지를 사용하도록 설정

```tsx
// plugins/persistedstate.client.ts
import { createPersistedState } from "pinia-plugin-persistedstate";

export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.$pinia.use(createPersistedState());
});
```

- 설치 후 plugins 폴더안 위와 같이 설정해야 한다.
- 기본적으로 로컬 스토리지를 사용하여 상태유지를 한다.

### 쿠키를 사용하도록 설정

```tsx
// plugins/persistedstate.client.ts
import { useCookie } from '#app';
import { createNuxtPersistedState } from 'pinia-plugin-persistedstate/nuxt';

export default defineNuxtPlugin(nuxtApp => {
  nuxtApp.$pinia.use(createNuxtPersistedState(useCookie, {
    cookieOptions: {
      maxAge: 3600,
      sameSite: 'strict',
    }
  }))
})
```

- 쿠키를 사용하여 상태유지를 하도록 설정할 수 도 있다.
- SSR을 사용하거나, 만료 시간을 설정하고 싶은 경우 위와 같이 설정해야 한다.

### 사용

```tsx
export const useAuthStore = defineStore(
  "auth",
  () => {
    const token = ref("");
    return { token };
  },
  {
    persist: true,
  }
);
```

- Composition API 형식으로 pinia를 사용하는 경우 위와 같이 `persist: true`를 추가해주면 된다.
- 로컬스토리지에서 키의 기본값은 스토어명으로 설정된다.
- 옵션 설정을 통해 세션 스토리지 사용, 직렬화 설정, 상태를 복원할 때 전처리 및 후처리를 위한 Hook function을 사용할 수도 있다.

## 참고 자료

- [pinia-plugin-persistedstate](https://prazdevs.github.io/pinia-plugin-persistedstate/)