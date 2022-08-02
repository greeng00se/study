### pinia-plugin-persistedstate

- pinia로 상태 관리를 하는 경우 화면을 새로고침 할 때 store 안에 존재하는 값들이 초기화 된다.
- 이 때 로컬 스토리지를 이용하여 상태를 유지할 수 있는데 이를 간편하게 해주는 플러그인이다.
- vuex의 vuex-persistedstate와 유사하다.

### 설치 및 설정 방법

```json
yarn add pinia-plugin-persistedstate
```

- 설치 후 plugins 폴더안에 다음과 같이 설정해야 한다.

```tsx
// plugins/persistedstate.cliend.ts
import { createPersistedState } from "pinia-plugin-persistedstate";

export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.$pinia.use(createPersistedState());
});
```

- SSR을 위해 쿠키를 이용하는 방법도 존재한다.
- 현재 Nuxt3를 정적 웹사이트를 생성하는 용도로 사용하고 있기 때문에 위와 같이 설정하였다.

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
- 추가로 옵션 설정을 통해 세션 스토리지를 사용할 수도 있고, 직렬화 설정, 상태를 복원할 때 전처리 및 후처리를 위한 Hook function을 사용할 수도 있다.

## 참고 자료

- [pinia-plugin-persistedstate](https://prazdevs.github.io/pinia-plugin-persistedstate/)