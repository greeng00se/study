## Element Plus

- Vue3의 UI Framework로 UI 관련해서 다양한 기능을 지원한다.
- 아래 설정한 내용은 [Github](https://github.com/greeng00se/nuxt3-starter)에서 확인할 수 있다.

### 설치 전

- 시작하기 전 다음 명령어로 Nuxt3 프로젝트가 생성되어 있어야 한다.

```bash
npx nuxi init nuxt-app
```

- 그리고 Nuxt3의 프로젝트 폴더에 의존성이 모두 추가되어 있어야 한다.

```bash
cd nuxt-app
yarn
```

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

### package.json

- 설치를 하고 나면 package.json파일이 아래와 유사한 형태가 될 것이다.

```json
{
  "private": true,
  "scripts": {
    "build": "nuxt build",
    "dev": "nuxt dev",
    "generate": "nuxt generate",
    "preview": "nuxt preview"
  },
  "devDependencies": {
    "nuxt": "3.0.0-rc.4"
  },
  "dependencies": {
    "@element-plus/icons-vue": "^2.0.6",
    "element-plus": "^2.2.8",
    "sass": "^1.53.0",
    "unplugin-auto-import": "^0.9.2",
    "unplugin-vue-components": "^0.21.1"
  }
}
```

### nuxt 설정

- Element UI의 설정 파일을 전역 설정을 해줘야 한다.
    - 설정 파일은 assets/scss/index.scss에 생성한다.
- 빌드 시 Element UI에 대한 의존성을 추가하기 위해 transpile에 아래와 같이 설정을 해줘야 된다고 한다.
    - npm_lifecycle_event 변수로 npm이 어떤 모드로 동작중인지 확인한 후 빌드 중인 경우 추가한다.

```tsx
import { defineNuxtConfig } from "nuxt";

const lifecycle = process.env.npm_lifecycle_event;

export default defineNuxtConfig({
  css: ["~/assets/scss/index.scss"],
  build: {
    transpile: lifecycle === "build" ? ["element-plus"] : [],
  },
});
```

### Element Plus 설정파일

- Nuxt의 경우 스타일 관련 파일은 assets 폴더 아래에 위치한다.
- assets/scss/index.scss 파일을 생성하여 다음과 같이 입력하고 저장한다.

```scss
@use "element-plus/dist/index.css"
```

### Element Plus & Icon 전역 설정

- Nuxt는 자동으로 plugins 디렉토리에 있는 파일을 읽고 로드한다.
- vueApp에 대해 전역적으로 등록하는 경우 plugins에서 설정할 수도 있다.
- 전역 설정을 한다면 vue파일에서 따로 Import 하지 않아도 사용할 수 있다.
- plugins/element.ts 파일을 생성하여 아래와 같이 설정한다.

```java
import * as ElementPlusIconsVue from '@element-plus/icons-vue';
import ElementPlus from 'element-plus';

export default defineNuxtPlugin(nuxtApp => {
  // ElementPlus
  nuxtApp.vueApp.use(ElementPlus, { size: 'small', zIndex: 3000 });

  // ElementPlus Icons
  for (const [key, component] of Object.entries(ElementPlusIconsVue)) {
    nuxtApp.vueApp.component(key, component)
  }
})
```

### Element Plus 사용

- Nuxt에서 기본 생성되는 App.vue 파일을 지우면 기본적으로 pages/index.vue 파일이 메인으로 설정된다.
- pages 폴더 아래 index.vue 파일을 생성해서 간단하게 동작하는지 테스트할 수 있다.

```tsx
<script setup>
</script>

<template>
  <div>
    <el-button>Default</el-button>
    <el-icon><User style="font-size:24px;"/></el-icon>
  </div>
</template>

<style scoped>
</style>
```

## 참고 자료

- [Quick Start, Element Plus](https://element-plus.org/en-US/guide/quickstart.html#on-demand-import)