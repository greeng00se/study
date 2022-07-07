### Windi CSS

- Windy CSS는 CSS 프레임워크로 Tailwind CSS의 대안으로 소개되었다.
- 클래스 그루핑과 공통 클래스 선언을 할 수 있고 Tailwind보다 빌드 타임이 빠르다고 한다.
- 아래 설정한 내용은 [Github](https://github.com/greeng00se/nuxt3-starter)에서 확인할 수 있다.

### Windi CSS 설치

- nuxt-windicss와 animation을 위해 플러그인도 같이 설치했다.

```bash
yarn add nuxt-windicss @windicss/plugin-animations
```

### nuxt.config 설정

- buildModules에 nuxt-windicss를 추가해주면 된다.

```tsx
import { defineNuxtConfig } from 'nuxt3'

export default defineNuxtConfig({
  buildModules: [
    'nuxt-windicss',
  ],
})
```

### 스캐닝 설정

- 스캐닝 설정을 통해 필요한 파일만 Windi CSS가 적용될 수 있게 할 수 있다.
- 프로젝트 폴더에 windi.config.js 파일을 생성하고 아래와 같이 설정하면 된다.

```tsx
import { defineConfig } from 'windicss/helpers'

export default defineConfig({
  extract: {
    include: ['**/*.{vue,html,jsx,tsx}'],
    exclude: ['node_modules', '.git'],
  },
})
```

### iconify 설치

- @iconify/vue를 설치한다.

```bash
yarn add @iconify/vue
```

### 사용

- iconify를 사용하려면 Icon을 import 해야 한다.
- 사용할 수 있는 아이콘의 경우 다음 사이트에서 확인할 수 있다.
    - [https://icones.js.org/](https://icones.js.org/)

```tsx
<template>
  <div>
    <h1 class="text-orange-600 font-mono">Hello Windy CSS</h1>
    <Icon icon="carbon:align-horizontal-center" />
  </div>
</template>

<script setup lang="ts">
import { Icon } from "@iconify/vue";
</script>

<style scoped></style>
```

## 참고 자료

- [windi CSS Install, Windi CSS](https://windicss.org/integrations/nuxt.html)
- [iconify](https://github.com/iconify/iconify)