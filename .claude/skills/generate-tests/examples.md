# Generate Tests Examples

## Example: Service layer tests

**Input**: `Generate tests for src/services/OrderService.ts`

**Output**:

```ts
import { describe, it, expect, vi, beforeEach } from "vitest";
import { OrderService } from "./OrderService";
import { createMockPrisma } from "../test/factories";

describe("OrderService", () => {
  let orderService: OrderService;
  let mockPrisma: ReturnType<typeof createMockPrisma>;

  beforeEach(() => {
    mockPrisma = createMockPrisma();
    orderService = new OrderService(mockPrisma);
  });

  describe("create", () => {
    it("should create an order with valid items", async () => {
      const order = await orderService.create({
        userId: "user-1",
        items: [{ productId: "prod-1", quantity: 2 }],
      });

      expect(order.status).toBe("pending");
      expect(order.items).toHaveLength(1);
    });

    it("should throw ValidationError when items array is empty", async () => {
      await expect(
        orderService.create({ userId: "user-1", items: [] })
      ).rejects.toThrow(ValidationError);
    });

    it("should throw NotFoundError when product does not exist", async () => {
      mockPrisma.product.findUnique.mockResolvedValue(null);

      await expect(
        orderService.create({
          userId: "user-1",
          items: [{ productId: "nonexistent", quantity: 1 }],
        })
      ).rejects.toThrow(NotFoundError);
    });

    it("should calculate total from item prices and quantities", async () => {
      mockPrisma.product.findUnique.mockResolvedValue({
        id: "prod-1",
        price: 29.99,
      });

      const order = await orderService.create({
        userId: "user-1",
        items: [{ productId: "prod-1", quantity: 3 }],
      });

      expect(order.total).toBeCloseTo(89.97);
    });
  });

  describe("findById", () => {
    it("should return order when it exists", async () => {
      mockPrisma.order.findUnique.mockResolvedValue({
        id: "order-1",
        status: "pending",
      });

      const order = await orderService.findById("order-1");
      expect(order?.id).toBe("order-1");
    });

    it("should return null when order does not exist", async () => {
      mockPrisma.order.findUnique.mockResolvedValue(null);

      const order = await orderService.findById("nonexistent");
      expect(order).toBeNull();
    });
  });
});
```
