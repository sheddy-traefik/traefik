---
title: "Traefik AddPrefix Documentation"
description: "Learn how to implement the HTTP AddPrefix middleware in Traefik Proxy to updates request paths before being forwarded. Read the technical documentation."
---

![AddPrefix](../../../../assets/img/middleware/addprefix.png)

The `addPrefix` middleware updates the path of a request before forwarding it.

## Configuration Examples

```yaml tab="File (YAML)"
# Prefixing with /foo
http:
  middlewares:
    add-foo:
      addPrefix:
        prefix: "/foo"
```

```toml tab="File (TOML)"
# Prefixing with /foo
[http.middlewares]
  [http.middlewares.add-foo.addPrefix]
    prefix = "/foo"
```

```yaml tab="Kubernetes"
# Prefixing with /foo
apiVersion: traefik.io/v1alpha1
kind: Middleware
metadata:
  name: add-foo
spec:
  addPrefix:
    prefix: /foo
```

```yaml tab="Consul Catalog"
# Prefixing with /foo
- "traefik.http.middlewares.add-foo.addprefix.prefix=/foo"
```

```yaml tab="Docker & Swarm"
# Prefixing with /foo
labels:
  - "traefik.http.middlewares.add-foo.addprefix.prefix=/foo"
```

## Configuration Options

| Field  | Description                                                                                                                                                                                                | Default | Required |
|:-----------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------|:---------|
| `prefix` | String to add **before** the current path in the requested URL. It should include a leading slash (`/`). | | Yes |