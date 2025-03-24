# MMCP (Monetizable MCP) Specification

## Terminology

We're using the same terminology as defined in the original [MCP Specification](https://spec.modelcontextprotocol.io/)

## Overview
MMCP (Monetizable MCP) is an extension to the Model Context Protocol (MCP) that standardizes **billing, authentication, and metering aggregation** for MCP-enabled APIs. MMCP acts as a **transparent proxy**, allowing MCP clients to prepay for access while ensuring MCP server providers can monetize their services efficiently.

To achieve that we introduce a new role, the MCP Toll (API gateway), which is responsible for proxying requests to participating MCP servers, aggregating usage metrics, and enforcing billing rules. 

## Rationale

Currently when using MCP servers that are not "free-for-all" you need to manually sign up with each one of them, provide your payment details, etc. It's a cumbersome process, that prevents AI agents from making trivial decisions on your behalf.
Eventually autonomous agents may want to pay for services with Bitcoin, which would be ineficient to do on a per-API basis. We don't want to stifle singularity by onboarding flows, do we?

## Key Components
1. [MCP Toll](mcp-toll-api-draft.md) - A service that handles authentication, billing, and metering aggregation by proxying requests between AI agents and MCP servers.
2. Additional requirements to **MCP servers** - registering with MCP Toll to let it know it trusts post-payments, and providing metering data in response headers.
3. Additional requirements to **MCP clients** - establish an account with MCP Toll and direct the user to pay for future services.
4. Feedback mechanism - AI agents can provide feedback on individual requests to let the MCP Toll know if MCP servers are providing a good service.


## Details

### Metering & Usage Aggregation

MCP server providers must include metering data in response headers:
```
X-MMCP-Metering: request=1, token=345
```
If none provided, the MCP Toll will assume the default `request=1`.

MCP server may define custom metrics, whatever is more appropriate for the service: `request`, `token`, `byte`, `second`, etc.

MCP server must provide a price for each metric or quota for a fixed price, so the MCP Toll can calculate the cost for the MCP client.

Additionally, MCP Toll should include a user identifier in requests:
```
X-MMCP-User: <uuid>
```
This allows MCP servers to distinguish between different users.

### Feedback Mechanism

MCP client may provide a header
```
X-MMCP-Request: <uuid>
```
If provided MCP Toll should forward it to the MCP server and include it in the response headers.

If not provided MCP Toll must generate a UUID, provide it to the MCP server and include it in the response headers.

After the original request MCP client can send an additional request to MCP Toll `/client/feedback` endpoint with the following payload:
```json
{
  "request_id": "<uuid>",
  "discarded": true
}
```
Which means either AI agent or end-user ended up discarding the result of API call. Such requests are still counted towards the usage, but MCP Toll can now compare similar MCP servers and prioritize the ones that provide more useful results.

TODO: Verify it can't be abused by MCP servers against competitors.
