# [Nuxt] Nuxt3에서 Element Plus 사용하기

## Element Plus

- Vue3의 UI Framework로 UI 관련해서 다양한 기능을 지원한다.
- 아래 설정한 내용은 [Github](https://github.com/greeng00se/nuxt3-starter)에서 확인할 수 있다.

### 설치

- Element UI를 사용하려면 다음 의존성을 설치해야 한다.
    - `sass`
    - `element-plus`
    - `@element-plus/icons-vue`
    - `unplugin-vue-components`
    - `unplugin-auto-import`

```bash
yarn add sass element-plus @element-plus/icons-vue unplugin-vue-components unplugin-auto-import
```

### nuxt 설정

- Element UI의 설정 파일을 전역 설정을 해줘야 한다.
    - 설정 파일은 assets/scss/index.scss에 생성한다.
- 빌드 시 Element UI에 대한 의존성을 추가하기 위해 transpile에 아래와 같이 설정을 해줘야 된다고 한다.

```tsx
import { defineNuxtConfig } from "nuxt";

export default defineNuxtConfig({
  css: ["~/assets/scss/index.scss"],
  build: {
    transpile: ["element-plus"],
  },
});
```

### Element Plus 설정파일

- Nuxt의 경우 스타일 관련 파일은 assets 폴더 아래에 위치한다.
- assets/scss/index.css 파일을 생성하여 다음과 같이 입력하고 저장한다.

```scss
@use "element-plus/dist/index.css"
```

### Element Plus 사용

- Nuxt에서 기본 생성되는 App.vue 파일을 지우면 기본적으로 pages/index.vue 파일이 메인으로 설정된다.
- pages 폴더 아래 index.vue 파일을 생성해서 간단하게 동작하는지 테스트할 수 있다.
- Element Plus의 경우 사용하려는 컴포넌트를 하나씩 import를 해줘야하는 것 같다.

```tsx
<template>
  <div>
    <el-button>Default</el-button>
  </div>
</template>

<script setup>
import { ElButton } from "element-plus"
</script>

<style scoped>
</style>
```

## 참고 자료

- [Quick Start, Element Plus](https://element-plus.org/en-US/guide/quickstart.html#on-demand-import)