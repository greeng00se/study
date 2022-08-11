# [Nuxt] Nuxt3 TypeError: Cannot read properties of null (reading 'parentNode') 문제 해결하기

### 개요

- Nuxt3로 프론트엔드를 개발하던 중 다음과 같은 에러가 발생했습니다.
    - `TypeError: Cannot read properties of null (reading 'parentNode')`
- 루트 페이지가 아닌 다른 페이지에서 새로고침을 한 다음에 vue-router를 사용(to 이용)할 때 해당 오류가 발생했습니다. 오류에 대한 자세한 내용은 [nuxt3 issue](https://github.com/nuxt/framework/issues/2985)에 있습니다.
- Nuxt3를 정적 웹 페이지 생성기로 사용하고 있습니다.
    - SSR을 사용할 때는 아직 해당 이슈가 해결되지 않은 것으로 보입니다.

### 해결 방법

- Nuxt3에는 정적 호스팅을 하는 2가지 방법이 있습니다.
    - Prerendering
    - client-side only rendering
- client-side only rendering 방식을 사용한다면 해당 오류가 발생하지 않게 할 수 있습니다.
    - ssr 옵션을 false로 설정하여 사용할 수 있습니다.

```tsx
// nuxt.config.ts
export default defineNuxtConfig({
  ssr: false,
});
```

## 참고 자료

- [https://github.com/nuxt/framework/issues/2985](https://github.com/nuxt/framework/issues/2985)
- [https://v3.nuxtjs.org/guide/deploy/static-hosting#static-hosting](https://v3.nuxtjs.org/guide/deploy/static-hosting#static-hosting)