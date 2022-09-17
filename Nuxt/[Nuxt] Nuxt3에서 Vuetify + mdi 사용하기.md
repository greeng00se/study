## Vuetify

- Vue.js 기반의 UI 프레임워크다.
- Material Design Spec에 따라 개발되었다.
- 현재 2.x.x 버전은 Vue3를 지원하지 않기 때문에 Nuxt3에서는 Vuetify 3버전 이상을 사용해야 한다.
- 아래 설정한 내용은 [Github](https://github.com/greeng00se/nuxt3-vuetify-axios)에서 확인할 수 있다.

### Vuetify 설치

- sass와 vuetify 최신 버전을 설치해야 한다.

```bash
yarn add vuetify@next sass
```

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
    "sass": "^1.53.0",
    "vuetify": "^3.0.0-beta.4"
  }
}
```

### 플러그인 설정

- nuxt3에서 사용하려면 플러그인 설정을 해야 한다.
- nuxt에서 플러그인의 경우 plugins 폴더 아래 위치한다.
- plugins/vuetify.js 파일을 생성 한 후 아래와 같이 입력하고 저장한다.

```tsx
import { createVuetify } from 'vuetify'
import * as components from 'vuetify/components'
import * as directives from 'vuetify/directives'

export default defineNuxtPlugin(nuxtApp => {
  const vuetify = createVuetify({
    components,
    directives,
  })

  nuxtApp.vueApp.use(vuetify)
})
```

### nuxt 설정

- nuxt.config.ts를 아래와 같이 설정해주면 vuetify를 사용할 수 있다.

```tsx
import { defineNuxtConfig } from 'nuxt'

export default defineNuxtConfig({
  css: ['vuetify/lib/styles/main.sass'],
  build: {
    transpile: ['vuetify'],
  },
  vite: {
    define: {
      'process.env.DEBUG': false,
    },
  },
})
```

### mdi 설정

- Material Design Icon을 사용하려면 다음과 같이 mdi를 설치해야 한다.
- 기존 mdi 패키지의 경우 deprecated 되었기 때문에 @mdi/font를 설치해야 한다.

```bash
yarn add @mdi/font
```

### nuxt mdi 설정

- nuxt 설정파일의 css 부분에 경로만 추가해주면 된다.
    - `@mdi/font/css/materialdesignicons.min.css`

```tsx
import { defineNuxtConfig } from 'nuxt'

// https://v3.nuxtjs.org/api/configuration/nuxt.config
export default defineNuxtConfig({
  css: ['vuetify/lib/styles/main.sass', '@mdi/font/css/materialdesignicons.min.css'],
  build: {
    transpile: ['vuetify'],
  },
  vite: {
    define: {
      'process.env.DEBUG': false,
    },
  },
})
```

### Vuetify 사용

- pages 폴더 아래 index.vue 파일을 생성해서 동작하는지 확인할 수 있다.

```tsx
<template>
  <div class="d-flex align-center flex-column">
    <v-icon large color="green darken-2">mdi-domain</v-icon>
    <v-btn
      color="primary"
      elevation="2"
      outlined
      rounded
    >Click</v-btn>
  </div>
</template>
```

## 참고 자료

- [vuetify installation, Vuetify](https://next.vuetifyjs.com/en/getting-started/installation/)
- [v-icon, nuxt discussions](https://github.com/nuxt/framework/discussions/1183#discussioncomment-2682117)
- [How to use Vuetify with Nuxt3](https://codybontecou.com/how-to-use-vuetify-with-nuxt-3.html#integrate-vuetify-s-mdi-icons)