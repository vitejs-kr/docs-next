# 디펜던시 최적화 옵션 {#dep-optimization-options}

- **참고:** [사전 번들링된 디펜던시](/guide/dep-pre-bundling)

## optimizeDeps.entries {#optimizedeps-entries}

- **타입:** `string | string[]`

기본적으로 Vite는 모든 `.html` 파일을 크롤링해 사전 번들링이 필요한 디펜던시를 탐지합니다(`node_modules`, `build.outDir`, `__tests__` 및 `coverage` 디렉터리는 무시). 만약 `build.rollupOptions.input`이 지정된 경우 Vite가 대신 해당 진입점을 탐색합니다.

둘 다 필요에 맞지 않는다면 이 옵션을 사용해 진입점을 지정할 수도 있습니다. 이 값은 [fast-glob 패턴](https://github.com/mrmlnc/fast-glob#basic-syntax) 또는 Vite 프로젝트 루트를 기준으로 상대 경로 패턴의 배열 값이 될 수 있습니다. 참고로 이렇게 값을 지정하게 되면 기존의 진입점 추론 방식을 덮어쓰게 됩니다. `optimizeDeps.entries`가 명시적으로 정의된 경우에는 기본적으로 `node_modules` 및 `build.outDir` 폴더만 무시됩니다. 다른 폴더를 추가로 무시하고자 하는 경우에는 진입점 리스트 요소에 `!` 로 시작하는 무시 패턴을 사용할 수 있습니다.

## optimizeDeps.exclude {#optimizedeps-exclude}

- **타입:** `string[]`

사전 번들링에서 제외할 디펜던시 목록입니다.

:::warning CommonJS
CommonJS 디펜던시는 최적화에서 제외돼서는 안 됩니다. ESM 디펜던시가 최적화에서 제외되었지만 이와 중첩된(Nested) CommonJS 디펜던시가 있는 경우, CommonJS 디펜던시를 `optimizeDeps.include`에 추가해줘야 합니다:

```js
export default defineConfig({
  optimizeDeps: {
    include: ['esm-dep > cjs-dep']
  }
})
```
:::

## optimizeDeps.include {#optimizedeps-include}

- **타입:** `string[]`

기본적으로 `node_modules` 내부에 없는 연결된 패키지들은 미리 번들로 제공되지 않습니다. 이 옵션을 사용하여 연결된 패키지를 미리 번들로 묶을 수 있습니다.

## optimizeDeps.esbuildOptions {#optimizedeps-esbuildoptions}

- **타입:** [`EsbuildBuildOptions`](https://esbuild.github.io/api/#simple-options)

디펜던시 스캐닝 및 최적화 중 Esbuild에 전달할 옵션입니다. 특정 옵션은 Vite의 디펜던시 최적화와 호환되지 않기에 생략되었습니다.

- `external`은 생략됩니다. 이 대신 Vite의 `optimizeDeps.exclude` 옵션을 사용합니다.
- `plugins`는 Vite의 디펜던시 플러그인과 병합됩니다.