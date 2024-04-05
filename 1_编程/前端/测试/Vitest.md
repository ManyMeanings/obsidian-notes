Vitest 提供了与 Jest 兼容的 API，支持对 Vue、React、Svelte 等框架进行组件测试，并且具有开箱即用的 TypeScript / JSX 支持。

```js
import { describe, expect, it } from "vitest";
import { max } from "../src/intro";

describe("max", () => {
  it("should return the first argument if it is greater", () => {
    expect(max(2, 1)).toBe(2);
  });
});
```

### Vitest UI

```zsh
npm i -D @vitest/ui
vitest --ui
```
