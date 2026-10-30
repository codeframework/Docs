# ServiceClient Events

The current `CODE.Framework.Services.Client.ServiceClient` does not expose the static events described by older versions of this article. In particular, the pending options-based `Call` and `CallAsync` APIs do not introduce channel-opening or per-operation events. This page keeps its existing URL so older links lead to the current guidance.

## Historical Event APIs

Earlier full-framework/WCF versions documented these hooks:

| Historical event | Original purpose |
| --- | --- |
| `BeforeChannelOpens` | Customize a channel as it opens. |
| `BeforeEndpointAdded` | Customize WCF endpoints and bindings. |
| `ChannelException` | Receive a channel error notification. |
| `BeforeServiceOperationCall` | Inspect or adjust operation inputs before invocation. |
| `AfterServiceOperationCall` | Observe an operation after invocation. |

These names describe historical behavior, not members available on the current Services client. Older examples setting `NetTcpBinding` or attaching these events should be used only with the framework version that actually supplies them. A protocol name or historical documentation link does not establish current WCF support.

## Observing Current Calls

Use application code around the client call or within the awaited callback to perform application-specific response processing. For failures, options-based synchronous calls and `CallAsync` propagate exceptions by default; application failure messages remain responses whose fields the caller inspects. Legacy `Call` can suppress callback failures, so code surrounding that overload alone is not a reliable failure notification mechanism.

See [Calling REST Services through ServiceClient](Calling-REST-Services-through-ServiceClient), [Calling Services Asynchronously](Calling-Services-Asynchronously), and [Service Call Options, Errors, and Retries](Service-Call-Options-Errors-and-Retries). `ServiceClient.LogCommunicationErrors` enables logging of final transport failures on the options-based REST path; it is not an event covering every application failure.

If an application needs shared request enrichment, use an application-owned calling helper or populate the request before invoking the service. Enforce authorization, including tenant access, at the service boundary; the current client supplies no documented universal event that guarantees every request has been enriched or authorized.

## In-Process Registration Notification

`CODE.Framework.Services.Contracts.IServiceEvents` is a separate implementation-side interface with `OnInProcessHostLaunched()`. If the registered implementation supports it, `ServiceGardenLocal` invokes that method after registering the local service. It is not a static `ServiceClient` event and does not run before or after each operation. See [Client-Side In-Process Service Hosting](Hosting-In-Process-Services).
