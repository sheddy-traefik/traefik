---
title: "Traefik Redis Documentation"
description: "For configuration discovery in Traefik Proxy, you can store your configurations in Redis. Read the technical documentation."
---

# Traefik & Redis

Store your configuration in Redis and let Traefik do the rest!

## Configuration Example

Enabling the redis provider

```yaml tab="File (YAML)"
providers:
  redis: {}
```

```toml tab="File (TOML)"
[providers.redis]
```

```bash tab="CLI"
--providers.redis=true
```

## Configuration Options

| Field | Description                                               | Default              | Required |
|:------|:----------------------------------------------------------|:---------------------|:---------|
| `redis.endpoints` | Defines the endpoint to access Redis. |  `127.0.0.1:6379`     | Yes   |
| `redis.rootKey` | Defines the root key for the configuration. |  `traefik`     | Yes   |
| `redis.username` | Defines a username for connecting to Redis. |  ""    | No   |
| `redis.password` | Defines a password for connecting to Redis. |  ""    | No   |
| `redis.db` | Defines the database to be selected after connecting to the Redis. |  0    | No   |
| `redis.tls` | Defines the TLS configuration used for the secure connection to Redis. |  N/A    | No   |
| `redis.tls.ca` | Defines the path to the certificate authority used for the secure connection to Redis, it defaults to the system bundle.  |  N/A   | No   |
| `redis.tls.cert` | Defines the path to the public certificate used for the secure connection to Redis. When using this option, setting the `key` option is required. |  N/A   | Yes   |
| `redis.tls.key` | Defines the path to the private key used for the secure connection to Redis. When using this option, setting the `cert` option is required. |  N/A   | Yes   |
| `redis.tls.insecureSkipVerify` | Instructs the provider to accept any certificate presented by Redis when establishing a TLS connection, regardless of the hostnames the certificate covers. | false   | No   |
| `redis.sentinel` | Defines the Sentinel configuration used to interact with Redis Sentinel. | N/A   | No   |
| `redis.sentinel.masterName` | Defines the name of the Sentinel master. | N/A   | Yes   |
| `redis.sentinel.username` | Defines the username for Sentinel authentication. | N/A   | No   |
| `redis.sentinel.password` | Defines the password for Sentinel authentication. | N/A   | No   |
| `redis.sentinel.latencyStrategy` | Defines whether to route commands to the closest master or replica nodes (mutually exclusive with RandomStrategy and ReplicaStrategy). | false   | No   |
| `redis.sentinel.randomStrategy` | Defines whether to route commands randomly to master or replica nodes (mutually exclusive with LatencyStrategy and ReplicaStrategy). | false   | No   |
| `redis.sentinel.replicaStrategy` | Defines whether to route commands randomly to master or replica nodes (mutually exclusive with LatencyStrategy and ReplicaStrategy). | false   | No   |
| `redis.sentinel.useDisconnectedReplicas` | Defines whether to use replicas disconnected with master when cannot get connected replicas. | false   | false   |

## Routing Configuration

See the dedicated section in [routing](../../../../routing/providers/kv.md).
