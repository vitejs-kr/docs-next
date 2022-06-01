# 빌드 옵션 {#build-options}

## build.target {#build-target}

- **타입:** `string | string[]`
- **기본값:** `'modules'`
- **참고:** [브라우저 지원 현황](/guide/build#browser-compatibility)

최종 번들을 위한 브라우저 호환성 타깃입니다. 기본값은 Vite 특수 값으로, [네이티브 ES 모듈](https://caniuse.com/es6-module) 및 [네이티브 ESM의 동적 Import](https://caniuse.com/es6-module-dynamic-import)를 지원하는 브라우저 타깃으로 하는 `'module'` 입니다.

또 다른 특수 값은 `'esnext'`입니다. 이는 네이티브 동적 가져오기가 지원되는 것으로 가정하고 가능한 한 적게 트랜스파일 됩니다:

- [`build.minify`](#build-minify) 옵션이 `'terser'` 이라면, `'esnext'`는 `'es2021'`로 다운 설정됩니다.
- 다른 경우에는, 전혀 트랜스파일이 수행되지 않습니다.

변환은 esbuild로 수행되며, 값은 유효한 [esbuild 타깃 옵션](https://esbuild.github.io/api/#target)이어야 합니다. 사용자 지정 타깃은 ES 버전 (예: `es2015`)이나 버전이 있는 브라우저 (예: `chrome58`) 또는 다중 타깃 문자열의 배열이 될 수 있습니다.

코드안에 esbuild로 안전하게 트랜스파일 할 수 없는 기능이 포함된 경우 빌드는 실패할 것입니다. 자세한 점은 [esbuild 문서](https://esbuild.github.io/content-types/#javascript)를 확인하세요.

## build.polyfillModulePreload {#build-polyfillmodulepreload}

- **타입:** `boolean`
- **기본값:** `true`

[모듈 미리로드 폴리필](https://guybedford.com/es-module-preloading-integrity#modulepreload-polyfill)을 자동으로 주입할지 여부입니다.

true로 설정하면 폴리필이 각 `index.html` 항목의 프록시 모듈에 자동으로 주입됩니다. 빌드가 `build.rollupOptions.input`을 통해 비 html 사용자 지정 진입점을 사용하도록 구성된 경우, 사용자 지정 진입점에 폴리필을 수동으로 가져와야 합니다:

```js
import 'vite/modulepreload-polyfill'
```

참고: 폴리필은 [라이브러리 모드](/guide/build#library-mode)에 적용되지 **않습니다**. 네이티브 동적 가져오기 없이 브라우저를 지원해야 한다면, 아마도 라이브러리에서 이것을 사용하지 않는 것이 좋습니다.

## build.outDir {#build-outdir}

- **타입:** `string`
- **기본값:** `dist`

([프로젝트 루트](/guide/#index-html-and-project-root)에 상대적인) 출력 디렉터리를 지정합니다.

## build.assetsDir {#build-assetsdir}

- **타입:** `string`
- **기본값:** `assets`

생성된 에셋을 (`build.outDir`에 상대적으로) 저장할 디렉터리를 지정합니다.

## build.assetsInlineLimit {#build-assetsinlinelimit}

- **타입:** `number`
- **기본값:** `4096` (4kb)

이 값보다 작은 크기로 import 되거나 참조된 에셋은 부가적인 http 요청을 피하고자 base64 URL로 인라인 처리됩니다. 만일 인라인 변환을 사용하지 않으려면 `0`으로 설정하세요.

::: tip 참고
`build.lib`를 지정하면, `build.assetsInlineLimit`는 무시되며 파일 크기와 관계없이 에셋이 항상 인라인 처리됩니다.
:::

## build.cssCodeSplit {#build-csscodesplit}

- **타입:** `boolean`
- **기본값:** `true`

CSS 코드 분할을 활성화/비활성화합니다. 활성화된 경우 비동기 청크로 가져온 CSS가 비동기 청크 안으로 인라인 처리되고 청크가 로드될 때 삽입됩니다.

비활성화된 경우, 전체 프로젝트의 모든 CSS가 단일 CSS 파일로 추출됩니다.

::: tip 참고
만약 `build.lib`으로 지정하게 되면, `build.cssCodeSplit`이 기본적으로 `false`가 됩니다.
:::

## build.cssTarget {#build-csstarget}

- **타입:** `string | string[]`
- **기본값:** [`build.target`](/config/#build-target)과 동일합니다.

이 옵션을 사용하면 CSS 압축(Minification) 시 타깃이 되는 브라우저를 설정할 수 있습니다.

일반적으로 비주류 브라우저를 대상으로 하는 경우에만 사용되며, Android WeChat WebView를 예로 들 수 있습니다.
이 브라우저는 대부분의 최신 JavaScript 문법을 지원하지만 [16진수 CSS 색상 표기법인 `#RGBA`](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value#rgb_colors)를 지원하지 않습니다.
이 경우, Vite가 `rgba()` 색상을 `#RGBA` 16진수 표기법으로 변환하는 것을 방지하기 위해 `build.cssTarget`을 `chrome61`로 설정해줘야 합니다.

## build.sourcemap {#build-sourcemap}

- **타입:** `boolean | 'inline' | 'hidden'`
- **기본값:** `false`

프로덕션에서 소스 맵을 생성합니다. `true`인 경우 별도의 소스 맵 파일이 생성됩니다. `'inline'`인 경우 소스 맵이 결과 출력 파일에 데이터 URI로 추가됩니다. `'hidden'`은 번들 파일의 해당 소스 맵 설명이 표시되지 않는 경우를 제외하고 `true`와 같이 작동합니다.

## build.rollupOptions {#build-rollupoptions}

- **타입:** [`RollupOptions`](https://rollupjs.org/guide/en/#big-list-of-options)

기존 Rollup 번들을 커스텀합니다. 이는 Rollup 설정 파일에서 내보낼 수 있는 옵션과 같으며 Vite의 내부 Rollup 옵션과 병합됩니다. 더 자세한 점은 [Rollup 옵션 문서](https://rollupjs.org/guide/en/#big-list-of-options)를 참고하세요.

## build.commonjsOptions {#build-commonjsoptions}

- **타입:** [`RollupCommonJSOptions`](https://github.com/rollup/plugins/tree/master/packages/commonjs#options)

[@rollup/plugin-commonjs](https://github.com/rollup/plugins/tree/master/packages/commonjs)에 전달할 옵션입니다.

## build.dynamicImportVarsOptions {#build-dynamicimportvarsoptions}

- **타입:** [`RollupDynamicImportVarsOptions`](https://github.com/rollup/plugins/tree/master/packages/dynamic-import-vars#options)
- **참고:** [동적 Import](/guide/features#dynamic-import)

[@rollup/plugin-dynamic-import-vars](https://github.com/rollup/plugins/tree/master/packages/dynamic-import-vars)에 전달할 옵션입니다.

## build.lib {#build-lib}

- **타입:** `{ entry: string, name?: string, formats?: ('es' | 'cjs' | 'umd' | 'iife')[], fileName?: string | ((format: ModuleFormat) => string) }`
- **참고:** [라이브러리 모드](/guide/build#library-mode)

라이브러리로 빌드합니다. 라이브러리에서 HTML을 진입점으로 사용할 수 없으므로, `entry`가 필요합니다. `name`은 노출된 전역 변수이며 `formats`이 `'umd'` 또는 `'iife'`를 포함할 때 필요합니다. 기본 `formats`은 `['es', 'umd']` 입니다. `fileName`은 패키지 파일 출력의 이름이며, 기본값은 package.json 파일의 name 옵션입니다. 또한 `format`을 인수로 취하는 함수로도 정의될 수 있습니다.

## build.manifest {#build-manifest}

- **타입:** `boolean | string`
- **기본값:** `false`
- **참고:** [백엔드 프레임워크와 함께 사용하기](/guide/backend-integration)

`true`로 설정하면, 빌드는 해시되지 않은 에셋 파일 이름을 해시된 버전으로의 매핑이 포함된 `manifest.json` 파일도 생성합니다. 이 파일은 서버 프레임워크에서 올바른 에셋 링크를 렌더링하는 데 사용할 수 있습니다. 문자열 타입의 값은 매니페스트 파일의 이름으로 사용됩니다.

## build.ssrManifest {#build-ssrmanifest}

- **타입:** `boolean | string`
- **기본값:** `false`
- **참고:** [서버 측 렌더링](/guide/ssr)

`true`로 설정하면, 빌드는 스타일 링크와 사전 로드된 에셋 디렉티브를 결정하기 위한 SSR 매니페스트 파일을 생성합니다. 문자열 타입의 값은 매니페스트 파일의 이름으로 사용됩니다.

## build.ssr {#build-ssr}

- **타입:** `boolean | string`
- **기본값:** `undefined`
- **참고:** [서버 측 렌더링](/guide/ssr)

서버 측 렌더링으로 빌드합니다. 설정 값은 SSR 항목을 직접 지정하는 문자열이거나, `rollupOptions.input`을 통해 SSR 항목을 지정해야 하는 `true`가 될 수 있습니다.

## build.minify {#build-minify}

- **타입:** `boolean | 'terser' | 'esbuild'`
- **기본값:** `'esbuild'`

코드 경량화를 사용하지 않으려면 `false`로 설정하거나, 사용할 코드 경량화 도구를 지정하세요. 기본값은 [Esbuild](https://github.com/evanw/esbuild)로, Terser보다 20에서 40배가량 빠르며 압축률 또한 1 ~ 2%밖에 떨어지지 않습니다.

참고로 `'es'`를 사용하는 Lib 모드에서 `build.minify` 옵션은 동작하지 않습니다.

## build.terserOptions {#build-terseroptions}

- **타입:** `TerserOptions`

Terser로 전달할 추가적인 [경량화 옵션](https://terser.org/docs/api-reference#minify-options)입니다.

## build.write {#build-write}

- **타입:** `boolean`
- **기본값:** `true`

번들을 생성할 때 디스크에 기록하지 않으려면 `false`로 설정하세요. 이것은 디스크에 쓰기 전에 번들의 추가 후처리가 필요한 [프로그래밍 방식 `build()` 호출](/guide/api-javascript#build)에서 주로 사용됩니다.

## build.emptyOutDir {#build-emptyoutdir}

- **타입:** `boolean`
- **기본값:** `outDir`이 `root` 안에 있다면 `true`

기본적으로 Vite는 `outDir`이 프로젝트 루트 내부에 있는 경우 빌드할 때 이 곳을 비웁니다. `outDir`가 루트 외부에 있으면 실수로 중요한 파일을 제거하지 않도록 경고 메시지가 표시됩니다. 경고를 표시하지 않도록 이 옵션을 명시적으로 설정할 수 있습니다. 명령 줄에서는 `--emptyOutDir`로 이를 사용할 수 있습니다.

## build.reportCompressedSize {#build-reportcompressedsize}

- **타입:** `boolean`
- **기본값:** `true`

gzip 압축 크기 보고를 활성화/비활성화합니다. 큰 출력 파일을 압축하는 경우 속도가 느릴 수 있으므로, 이를 사용하지 않도록 설정하면 대규모 프로젝트의 빌드 성능이 향상될 수 있습니다.

## build.chunkSizeWarningLimit {#build-chunksizewarninglimit}

- **타입:** `number`
- **기본값:** `500`

청크 크기 경고를 위한 제한값 입니다. (단위: KB)

## build.watch {#build-watch}

- **타입:** [`WatcherOptions`](https://rollupjs.org/guide/en/#watch-options)`| null`
- **기본값:** `null`

Rollup 감시자를 사용하려면 `{}`로 설정하세요. 이는 대부분 빌드 전용 플러그인 또는 통합 프로세스를 포함하는 경우에 사용됩니다.