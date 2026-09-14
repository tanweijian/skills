# 在 system seam 使用 mock

只 mock 被测系统之外的 dependency，例如 third-party API、clock、randomness、external queue，或无法合理参与测试的 infrastructure。当真实 test database 或 filesystem fixture 对所选 seam 来说足够快速且隔离时，优先使用真实实现。

内部 module 使用真实实现。Mock 自己控制的 collaborator 会让测试耦合到实现结构，削弱 refactoring safety。

## 让外部 dependency 可替换

通过被测 module 的 interface 接收外部 dependency，而不是在内部创建：

```typescript
function processPayment(order, paymentClient) {
  return paymentClient.charge(order.total);
}
```

优先使用职责明确的 adapter operation，而不是需要条件判断的通用 mock：

```typescript
const api = {
  getUser: (id) => fetch(`/users/${id}`),
  getOrders: (userId) => fetch(`/users/${userId}/orders`),
  createOrder: (data) => fetch("/orders", { method: "POST", body: data }),
};
```

每个 fake 应表示一个 response shape 清晰的外部 contract。如果 production interface 能直接表达这些操作，就不要把条件路由逻辑放进 test setup。
