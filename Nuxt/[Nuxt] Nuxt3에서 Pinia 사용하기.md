### Pinia

- Vuex와 비슷한 Vue의 상태관리 프레임워크다.
- 최근에 Vue의 개발자인 Evan You가 Pinia가 Vuex5와 동일하다고 트위터에서 언급한 적이 있다.
- 아래 설정한 내용은 [Github](https://github.com/greeng00se/nuxt3-starter)에서 확인할 수 있다.

### Pinia 설치

- pinia와 @pinia/nuxt를 설치해야 한다.

```bash
yarn add pinia @pinia/nuxt
```

### nuxt.config 설정

- buildModules에 @pinia/nuxt만 추가해주면 된다.

```tsx
import { defineNuxtConfig } from 'nuxt3'

export default defineNuxtConfig({
  buildModules: [
    '@pinia/nuxt',
  ],
})
```

### pinia store 생성

- pinia는 기본적으로 state, actions, getters의 구조로 되어있다.
    - vuex의 mutations이 제거되었다.
- 또한 Vue3 Composition API 형식도 지원한다.
- stores/counter.ts 파일을 생성하여 Composition API 형식으로 작성한 예제 코드다.

```tsx
import { defineStore } from 'pinia';

export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  function increment() {
    count.value++
  }
  const doubleCount = computed(() => count.value * 2)

  return { count, increment, doubleCount }
})
```

### pinia store 사용

- pages/index.vue 파일에 아래의 코드를 작성하여 테스트했다.
- 아래와 같이 CouterStore를 사용하면된다.

```tsx
<template>
  <div>
    <input type="button" @click="store.increment()" value="increment">
    {{ store.count }}
    {{ store.doubleCount }}
  </div>
</template>

<script setup lang="ts">
import { useCounterStore } from "../stores/counter"

const store = useCounterStore();
</script>

<style scoped></style>
```

## 참고 자료

- [Getting Started, Pinia](https://pinia.vuejs.org/getting-started.html)
- [Setup Pinia in Nuxt3, MANOJ AP](https://dev.to/manojap/setup-pinia-in-nuxt-3-37ia)