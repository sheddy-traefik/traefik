---
title: "Traefik HTTP Documentation"
description: "Provide your dynamic configuration via an HTTP(S) endpoint and let Traefik Proxy do the rest. Read the technical documentation."
---

# Traefik & HTTP

Provide your [install configuration](./overview.md) via an HTTP(S) endpoint and let Traefik do the rest!

## Configuration Example

Enabling the file provider:

```yaml tab="File (YAML)"
providers:
  http: {}
```

```toml tab="File (TOML)"
[providers.http]
```

```bash tab="CLI"
--providers.http
```

## Configuration Options

| Field | Description                                               | Default              | Required |
|:------|:----------------------------------------------------------|:---------------------|:---------|
| `http.endpoint` | Defines the HTTP(S) endpoint to poll. |  N/A    | Yes   |
| `http.pollInterval` | Defines the polling interval. |  5s    | No   |
| `http.pollTimeout` | Defines the polling timeout when connecting to the endpoint. |  5s    | No   |
| `http.headers` | Defines custom headers to be sent to the endpoint. |  5s    | No   |
| `http.tls` | Defines the TLS configuration used for the secure connection to the HTTP endpoint  |  N/A   | No   |
| `http.tls.ca` | Defines the path to the certificate authority used for the secure connection to the endpoint, it defaults to the system bundle.  |  N/A   | No   |
| `http.tls.cert` | Defines the path to the public certificate used for the secure connection to the endpoint. When using this option, setting the `key` option is required. |  N/A   | Yes   |
| `http.tls.key` | Defines the path to the private key used for the secure connection to the endpoint. When using this option, setting the `cert` option is required. |  N/A   | Yes   |
| `http.tls.insecureSkipVerify` | Instructs the provider to accept any certificate presented by endpoint when establishing a TLS connection, regardless of the hostnames the certificate covers. | false   | No   |

## Routing Configuration

The HTTP provider uses the same configuration as the [File Provider](./file.md) in YAML or JSON format.
