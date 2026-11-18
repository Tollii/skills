# .NET reference

Use this file when the request involves C# or .NET code and the design question is about seams, collaborator roles, or interaction tests.

## Preferred shape

- Define small application-owned interfaces around collaborators
- Inject those collaborators into the object under test
- Stub queries that provide information
- Expect commands that represent meaningful effects
- Wrap framework or vendor APIs before mocking them

## Example: use roles, not framework types

### Poor direction

- `CheckoutService` talks directly to `HttpClient`, a payment SDK, and an email sender implementation
- Tests mock `HttpMessageHandler` or vendor SDK internals
- Expectations mirror JSON payload construction and incidental retries

### Better direction

The service depends on narrow roles it actually needs:

```csharp
public interface IInventoryPolicy
{
    bool CanFulfill(Order order);
}

public interface IBillingGateway
{
    Task ChargeAsync(CustomerId customerId, Money amount, CancellationToken cancellationToken);
}

public interface IReceiptSink
{
    Task SendAsync(OrderId orderId, CancellationToken cancellationToken);
}

public sealed class CheckoutService
{
    private readonly IInventoryPolicy inventoryPolicy;
    private readonly IBillingGateway billingGateway;
    private readonly IReceiptSink receiptSink;

    public CheckoutService(
        IInventoryPolicy inventoryPolicy,
        IBillingGateway billingGateway,
        IReceiptSink receiptSink)
    {
        this.inventoryPolicy = inventoryPolicy;
        this.billingGateway = billingGateway;
        this.receiptSink = receiptSink;
    }

    public async Task CheckoutAsync(Order order, CancellationToken cancellationToken)
    {
        if (!inventoryPolicy.CanFulfill(order))
        {
            return;
        }

        await billingGateway.ChargeAsync(order.CustomerId, order.Total, cancellationToken);
        await receiptSink.SendAsync(order.Id, cancellationToken);
    }
}
```

## Example test with xUnit and NSubstitute

```csharp
public sealed class CheckoutServiceTests
{
    [Fact]
    public async Task Charges_and_sends_receipt_when_inventory_allows_checkout()
    {
        var inventory = Substitute.For<IInventoryPolicy>();
        var billing = Substitute.For<IBillingGateway>();
        var receipts = Substitute.For<IReceiptSink>();
        var service = new CheckoutService(inventory, billing, receipts);
        var order = OrderSamples.Pending();

        inventory.CanFulfill(order).Returns(true);

        await service.CheckoutAsync(order, CancellationToken.None);

        await billing.Received(1)
            .ChargeAsync(order.CustomerId, order.Total, Arg.Any<CancellationToken>());

        await receipts.Received(1)
            .SendAsync(order.Id, Arg.Any<CancellationToken>());
    }
}
```

Why this matches the skill:

- `CanFulfill` is a **stubbed query**
- `ChargeAsync` and `SendAsync` are **expected commands**
- The test does not care how the gateway talks to Stripe or how receipts are formatted

## Only assert order if it matters

If the behaviour requires billing before receipt delivery, assert that explicitly. Otherwise do not hard-code order.

```csharp
Received.InOrder(async () =>
{
    await billing.ChargeAsync(order.CustomerId, order.Total, Arg.Any<CancellationToken>());
    await receipts.SendAsync(order.Id, Arg.Any<CancellationToken>());
});
```

## Own the seam to foreign infrastructure

Wrap foreign APIs in a thin adapter you own:

```csharp
public sealed class StripeBillingGateway : IBillingGateway
{
    private readonly StripeClient client;

    public StripeBillingGateway(StripeClient client)
    {
        this.client = client;
    }

    public Task ChargeAsync(CustomerId customerId, Money amount, CancellationToken cancellationToken)
    {
        return client.ChargeAsync(customerId.Value, amount.MinorUnits, cancellationToken);
    }
}
```

Then:

- unit tests for `CheckoutService` mock `IBillingGateway`
- adapter tests cover the `StripeBillingGateway` translation itself

## Common .NET review comments from this skill

- "Wrap `HttpClient`, EF Core, or vendor SDK access behind an application role before mocking."
- "This test is using mocks to mirror implementation chatter; keep only the interaction that proves behaviour."
- "The service depends on too many neighbours; introduce an intermediate role or split responsibilities."
- "This object is mostly value logic. Prefer state/value assertions over interaction testing here."
