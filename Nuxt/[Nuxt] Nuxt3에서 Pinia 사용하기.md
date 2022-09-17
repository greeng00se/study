### Pinia

- Vuex와 유사한 Vue의 상태관리 프레임워크입니다.
- Pinia를 사용한다면 구성 요소 및 페이지 간 상태를 공유할 수 있습니다.

### Pinia 설치

- `pinia`와 `@pinia/nuxt`를 설치해야 합니다.

```bash
yarn add pinia @pinia/nuxt
```

### nuxt.config 설정

- modules에 `@pinia/nuxt`만 추가하면 됩니다.

```tsx
import { defineNuxtConfig } from 'nuxt'

export default defineNuxtConfig({
  modules: [
    '@pinia/nuxt',
  ],
})
```

### pinia store 생성

- pinia는 기본적으로 state, actions, getters의 구조로 되어있습니다.
    - vuex의 mutations이 제거되었습니다.
- Vue3의 Composition API 형식도 지원합니다.

```tsx
import { defineStore } from "pinia"

export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  function increment() {
    count.value++
  }
  const doubleCount = computed(() => count.value * 2)

  return { count, increment, doubleCount }
})
```

- stores 폴더 아래 counter.ts 파일을 생성하여 Composition API 형식으로 작성한 예제 코드입니다.

### pinia store 사용

- pages/index.vue 파일에 아래와 같이 코드를 작성하여 스토어를 사용할 수 있습니다.

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

### 내용 수정

- 2022-09-17 → pinia 설치 및 적용 방법 수정

### 참고 자료

- [Getting Started, Pinia](https://pinia.vuejs.org/getting-started.html)