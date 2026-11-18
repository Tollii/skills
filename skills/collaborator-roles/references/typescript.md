# TypeScript reference

Use this file when the request involves TypeScript or JavaScript services, handlers, or orchestration code and the design question is about collaborator roles and interaction tests.

## Preferred shape

- Define app-owned ports and adapters instead of reaching directly into SDKs or `fetch`
- Inject collaborators into the unit under test
- Stub information-providing queries
- Expect meaningful commands or side effects
- Keep Vitest or Jest assertions focused on behaviour, not implementation chatter

## Example: define ports from the service's needs

```ts
export interface InventoryPolicy {
  canFulfill(order: Order): boolean;
}

export interface BillingGateway {
  charge(customerId: string, amount: Money): Promise<void>;
}

export interface ReceiptSink {
  send(orderId: string): Promise<void>;
}

export class CheckoutService {
  constructor(
    private readonly inventoryPolicy: InventoryPolicy,
    private readonly billingGateway: BillingGateway,
    private readonly receiptSink: ReceiptSink,
  ) {}

  async checkout(order: Order): Promise<void> {
    if (!this.inventoryPolicy.canFulfill(order)) {
      return;
    }

    await this.billingGateway.charge(order.customerId, order.total);
    await this.receiptSink.send(order.id);
  }
}
```

## Example test with Vitest

```ts
import { describe, expect, it, vi } from "vitest";

describe("CheckoutService", () => {
  it("charges and sends a receipt when inventory allows checkout", async () => {
    const inventory: InventoryPolicy = {
      canFulfill: vi.fn().mockReturnValue(true),
    };
    const billing: BillingGateway = {
      charge: vi.fn().mockResolvedValue(undefined),
    };
    const receipts: ReceiptSink = {
      send: vi.fn().mockResolvedValue(undefined),
    };

    const service = new CheckoutService(inventory, billing, receipts);
    const order = orderFixture();

    await service.checkout(order);

    expect(inventory.canFulfill).toHaveBeenCalledWith(order);
    expect(billing.charge).toHaveBeenCalledWith(order.customerId, order.total);
    expect(receipts.send).toHaveBeenCalledWith(order.id);
  });
});
```

Why this matches the skill:

- `canFulfill` is a **stubbed query**
- `charge` and `send` are **expected commands**
- The test never mocks `fetch`, `Response`, or vendor client internals

## Do not let mocks mirror the SDK

Avoid this:

- service imports a Stripe SDK client directly
- test stubs nested SDK methods or raw HTTP calls
- assertions lock in request-shaping details that belong inside the adapter

Prefer this:

```ts
export interface InvoicingPort {
  createInvoice(input: InvoiceDraft): Promise<void>;
}

export class StripeInvoicingAdapter implements InvoicingPort {
  constructor(private readonly stripeClient: StripeClient) {}

  async createInvoice(input: InvoiceDraft): Promise<void> {
    await this.stripeClient.invoices.create({
      customer: input.customerId,
      amount_due: input.amount.minorUnits,
    });
  }
}
```

Then:

- unit tests for domain services mock `InvoicingPort`
- adapter tests cover the mapping to Stripe or `fetch`

## Assert order only when it is a real requirement

If the requirement is "send the receipt only after successful billing", order matters. Otherwise avoid nth-call assertions and sequence snapshots.

## Common TypeScript review comments from this skill

- "Create an app-owned port instead of mocking `fetch`, Axios, or the SDK directly in the service test."
- "This mock setup is doing real work; extract a collaborator rather than teaching the mock business rules."
- "The test is coupled to call choreography that the feature does not require."
- "This module is mostly calculation or parsing. Use value assertions instead of interaction testing."
