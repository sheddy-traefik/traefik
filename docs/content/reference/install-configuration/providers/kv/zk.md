---
title: "Traefik ZooKeeper Documentation"
description: "For configuration discovery in Traefik Proxy, you can store your configurations in ZooKeeper. Read the technical documentation."
---

# Traefik & ZooKeeper

A Story of KV Store & Containers
{: .subtitle }

Store your configuration in ZooKeeper and let Traefik do the rest!

## Configuration Example

```yaml tab="File (YAML)"
providers:
  zooKeeper: {}
```

```toml tab="File (TOML)"
[providers.zooKeeper]
```

```bash tab="CLI"
--providers.zookeeper
```

## Configuration Options

| Field | Description                                               | Default              | Required |
|:------|:----------------------------------------------------------|:---------------------|:---------|
| `zooKeeper.endpoints` | Defines the endpoint to access ZooKeeper. |  `127.0.0.1:2181`     | Yes   |
| `zooKeeper.rootKey` | Defines the root key for the configuration. |  `traefik`     | Yes   |
| `zooKeeper.username` | Defines a username with which to connect to zooKeeper. |  ""   | No   |
| `zooKeeper.password` | Defines a password for connecting to zooKeeper. |  ""    | No   |
| `zooKeeper.tls` | Defines the TLS configuration used for the secure connection to zooKeeper. |  N/A    | No   |
| `zooKeeper.tls.ca` | Defines the path to the certificate authority used for the secure connection to zooKeeper, it defaults to the system bundle.  |  N/A   | No   |
| `zooKeeper.tls.cert` | Defines the path to the public certificate used for the secure connection to zooKeeper. When using this option, setting the `key` option is required. |  N/A   | Yes   |
| `zooKeeper.tls.key` | Defines the path to the private key used for the secure connection to zooKeeper. When using this option, setting the `cert` option is required. |  N/A   | Yes   |
| `zooKeeper.tls.insecureSkipVerify` | Instructs the provider to accept any certificate presented by etcd when establishing a TLS connection, regardless of the hostnames the certificate covers. | false   | No   |

## Routing Configuration

See the dedicated section in [routing](../routing/providers/kv.md).
