
- **Metadata**
	- Link: [Overview | TanStack Query Docs](https://tanstack.com/query/latest/docs/framework/react/overview)
	- Tags: #Common
	- Date: 2024-03-23
---

## 介绍

TanStack Query (FKA React Query) 是一个 `data-fetching` 库，它使 Web 应用程序中的获取、缓存、同步和更新服务器状态变得轻而易举。

它支持：

- 请求数据缓存
- 错误重试
- 避免重复请求
- 复杂场景请求：无限滚动、窗口焦点重新请求等
- ...

![[Pasted image 20240323111451.png]]

## 使用

### 快速上手

```jsx
import {
  useQuery,
  useMutation,
  useQueryClient,
  QueryClient,
  QueryClientProvider,
} from "@tanstack/react-query";
import { getTodos, postTodo } from "../my-api";

// Create a client
const queryClient = new QueryClient();

function App() {
  return (
    // Provide the client to your App
    <QueryClientProvider client={queryClient}>
      <Todos />
    </QueryClientProvider>
  );
}

function Todos() {
  // Access the client
  const queryClient = useQueryClient();

  // Queries
  const query = useQuery({ queryKey: ["todos"], queryFn: getTodos });

  // Mutations
  const mutation = useMutation({
    mutationFn: postTodo,
    onSuccess: () => {
      // Invalidate and refetch
      queryClient.invalidateQueries({ queryKey: ["todos"] });
    },
  });

  return (
    <div>
      <ul>
        {query.data?.map((todo) => (
          <li key={todo.id}>{todo.title}</li>
        ))}
      </ul>

      <button
        onClick={() => {
          mutation.mutate({
            id: Date.now(),
            title: "Do Laundry",
          });
        }}
      >
        Add Todo
      </button>
    </div>
  );
}

render(<App />, document.getElementById("root"));
```

## 参考

- [React Query 入门教程！ - 掘金](https://juejin.cn/post/7195540736908394556#heading-1)
