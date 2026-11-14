# Preserve the stages and encode their guarantees

An F# module can control construction and compose stages without a coordinator class. This complete example preserves parsing, enrichment, and routing; a failure prevents later stages from running.

```fsharp
module OrderProcessing

open System

type ProductCode = private ProductCode of string
type Order = { Code: ProductCode }
type EnrichedOrder = { Order: Order; Warehouse: string }
type RoutedOrder = { Order: EnrichedOrder; Destination: string }

type ProcessingError =
    | MissingProductCode
    | ProductNotFound of ProductCode
    | RouteUnavailable of string

let parseOrder (rawCode: string) : Result<Order, ProcessingError> =
    if String.IsNullOrWhiteSpace rawCode then Error MissingProductCode
    else Ok { Code = ProductCode (rawCode.Trim()) }

let enrich (warehouses: Map<ProductCode, string>) (order: Order)
    : Result<EnrichedOrder, ProcessingError> =
    match Map.tryFind order.Code warehouses with
    | Some warehouse -> Ok { Order = order; Warehouse = warehouse }
    | None -> Error (ProductNotFound order.Code)

let route (destinations: Map<string, string>) (order: EnrichedOrder)
    : Result<RoutedOrder, ProcessingError> =
    match Map.tryFind order.Warehouse destinations with
    | Some destination -> Ok { Order = order; Destination = destination }
    | None -> Error (RouteUnavailable order.Warehouse)

let processOrder warehouses destinations rawCode =
    parseOrder rawCode
    |> Result.bind (enrich warehouses)
    |> Result.bind (route destinations)
```

The module controls `ProductCode` construction. Parsing proves a non-empty code, not that the product exists. Enrichment and routing establish later facts with their own outcomes. `Result.bind` keeps a failure and skips subsequent stages. Database and network effects would supply the evidence outside these pure transformations; mutable permissions still need checking at the authoritative operation.

Verify successful routing and each meaningful failure. In languages without closed unions, do not assume nested subclasses enforce exhaustive case handling. In TypeScript, use a discriminated union and an exhaustive check when appropriate; treat assertions as bypasses, not parsers.
