# Behavior-focused test

## 推荐形式

稳定的测试应当通过 public interface 描述调用者可见的行为。测试名称说明能力或结果，assertion 只观察调用者无需进入内部实现即可获得的结果。

```typescript
test("user can checkout with a valid cart", async () => {
  const cart = createCart();
  cart.add(product);

  const result = await checkout(cart, paymentMethod);

  expect(result.status).toBe("confirmed");
});
```

当 integration-style test 能以合理成本覆盖有意义的 seam 时，优先采用这种形式。保持行为不变的 refactoring 通常不应要求修改这些测试。

## 排除 implementation coupling

依赖 private method、内部调用顺序、协作者调用次数或存储细节的测试，描述的是实现当前如何工作，而不是它对外保证什么。

当契约要求数据可被再次获取时，应通过公开的 retrieval behavior 验证 persistence：

```typescript
test("created user can be retrieved", async () => {
  const user = await createUser({ name: "Alice" });

  const retrieved = await getUser(user.id);

  expect(retrieved.name).toBe("Alice");
});
```

## 排除 tautology

Expected value 必须来自 PRD、code spec、经过推导的示例或其他独立的 source of truth。不要在 assertion 中重复实现所使用的计算过程。

```typescript
test("calculates the total of line items", () => {
  expect(calculateTotal([{ price: 10 }, { price: 5 }])).toBe(15);
});
```

只有当测试能够与错误实现产生分歧时，它才有价值。
