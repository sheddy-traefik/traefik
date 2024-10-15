---
title: "Traefik Kubernetes IngressRoute Documentation"
description: "Learn about the Kubernetes IngressRoute in Traefik Proxy. Read the technical documentation."
---

IngressRoute is the CRD implementation of a [Traefik HTTP router](../../../../routing/routers/index.md#configuring-http-routers).

Register the IngressRoute [kind](https://doc.traefik.io/traefik/reference/dynamic-configuration/kubernetes-crd/#definitions) in the Kubernetes cluster before creating IngressRoute objects.

## Configuration example

```yaml tab="IngressRoute"
apiVersion: traefik.io/v1alpha1
kind: IngressRoute
metadata:
  name: test-name
  namespace: apps
spec:
  entryPoints:
    - web
  routes:
  - kind: Rule
    # Rule on the Host
    match: Host(`test.example.com`)
    # Attach a middleware
    middlewares:
    - name: middleware1
      namespace: apps
    # Set a pirority
    priority: 10
    services:
    # Target a Kubernetes Support
    - kind: Service
      name: foo
      namespace: apps
      # Customize the connection between Hub API Gateway and the backend
      passHostHeader: true
      port: 80
      responseForwarding:
        flushInterval: 1ms
      scheme: https
      sticky:
        cookie:
          httpOnly: true
          name: cookie
          secure: true
      strategy: RoundRobin
      weight: 10
  tls:
    # Generate a TLS certificate using a certificate resolver
    certResolver: foo
    domains:
    - main: example.net
      sans:
      - a.example.net
      - b.example.net
    # Customize the TLS options
    options:
      name: opt
      namespace: apps
    # Add a TLS certificate from a Kubernetes Secret
    secretName: supersecret
```

```yaml tab="Middleware"
  # All resources definition must be declared
  # Prefixing with /foo
  apiVersion: traefik.io/v1alpha1
  kind: Middleware
  metadata:
    name: middleware1
    namespace: apps
  spec:
    addPrefix:
      prefix: /foo
```

```yaml tab="TLSOption"
  apiVersion: traefik.io/v1alpha1
  kind: TLSOption
  metadata:
    name: opt
    namespace: apps

  spec:
    minVersion: VersionTLS12
```

```yaml tab="Secret"
  apiVersion: v1
  kind: Secret
  metadata:
    name: supersecret
    namespace: apps
  data:
    tls.crt: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0=
    tls.key: LS0tLS1CRUdJTiBQUklWQVRFIEtFWS0tLS0tCi0tLS0tRU5EIFBSSVZBVEUgS0VZLS0tLS0=
```

## Configuration Options

<!-- markdownlint-disable MD013 -->

| Field | Description                                               | Default              | Required |
|:------|:----------------------------------------------------------|:---------------------|:---------|
| `entryPoints` | List of [entry points](../../../../routing/routers/index.md#entrypoints) names.<br />If not specified, HTTP routers will accept requests from all EntryPoints in the list of default EntryPoints. |  | No |
| `routes`        | List of routes. | | Yes |
| `routes[n].kind`        | Kind of router matching, only `Rule` is allowed yet. | "" | Yes |
| `routes[n].match`       | Defines the [rule](../../../../routing/routers/index.md#rule) corresponding to an underlying router. | "" | No |
| `routes[n].priority`    | Defines the [priority](../../../../routing/routers/index.md#priority) to disambiguate rules of the same length, for route matching.<br />If not set, the priority is directly equal to the length of the rule, and so the longest length has the highest priority.<br />A value of `0` for the priority is ignored, the default rules length sorting is used. | 0  | No |
| `routes[n].middlewares` | List of middlewares to attach to the IngressRoute. <br /> |  | No |
| `routes[n].`<br />`middlewares[m].`<br />`name` | Middleware name.<br />The character `@` is not authorized. <br /> | "" | Yes |
| `routes[n].`<br />`middlewares[m].`<br />`namespace` | Middleware namespace.<br />Can be empty if the middleware belongs to the same namespace as the IngressRoute. <br /> | "" | No |
| `routes[n].`<br />`services` | List of any combination of TraefikService and [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service/). <br /> |  | No |
| `routes[n].`<br />`services[m].`<br />`kind` | Kind of the service targeted.<br />Two values allowed:<br />- **Service**: Kubernetes Service<br /> **TraefikService**: Traefik Service.<br />  | "" | No |
| `routes[n].`<br />`services[m].`<br />`name` | Service name.<br />The character `@` is not authorized. | "" | Yes |
| `routes[n].`<br />`services[m].`<br />`namespace` | Service namespace.<br />Can be empty if the service belongs to the same namespace as the IngressRoute. | "" | No |
| `routes[n].`<br />`services[m].`<br />`port` | Service port (number or port name).<br />Evaluated only if the kind is **Service**. | "" | No |
| `routes[n].`<br />`services[m].`<br />`responseForwarding.`<br />`flushInterval` | Interval, in milliseconds, in between flushes to the client while copying the response body.<br />A negative value means to flush immediately after each write to the client.<br />This configuration is ignored when a response is a streaming response; for such responses, writes are flushed to the client immediately.<br />Evaluated only if the kind is **Service**. | 100ms | No |
| `routes[n].`<br />`services[m].`<br />`scheme` | Scheme to use for the request to the upstream Kubernetes Service.<br />Evaluated only if the kind is **Service**. | "http"<br />"https" if `port` is 443 or contains the string *https*. | No |
| `routes[n].`<br />`services[m].`<br />`serversTransport` | Name of ServersTransport resource to use to configure the transport between Traefik and your servers.<br />Evaluated only if the kind is **Service**. | "" | No |
| `routes[n].`<br />`services[m].`<br />`passHostHeader` | Forward client Host header to server.<br />Evaluated only if the kind is **Service**. | true | No |
| `routes[n].`<br />`services[m].`<br />`healthCheck.scheme` | Server URL scheme for the health check endpoint.<br />Evaluated only if the kind is **Service**.<br />Only for [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service/) of type [ExternalName](https://kubernetes.io/docs/concepts/services-networking/service/#externalname). | "" | No |
| `routes[n].`<br />`services[m].`<br />`healthCheck.mode` | Health check mode.<br /> If defined to grpc, will use the gRPC health check protocol to probe the server.<br />Evaluated only if the kind is **Service**.<br />Only for [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service/) of type [ExternalName](https://kubernetes.io/docs/concepts/services-networking/service/#externalname). | "http" | No |
| `routes[n].`<br />`services[m].`<br />`healthCheck.path` | Server URL path for the health check endpoint.<br />Evaluated only if the kind is **Service**.<br />Only for [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service/) of type [ExternalName](https://kubernetes.io/docs/concepts/services-networking/service/#externalname). | "" | No |
| `routes[n].`<br />`services[m].`<br />`healthCheck.interval` | Frequency of the health check calls.<br />Evaluated only if the kind is **Service**.<br />Only for [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service/) of type [ExternalName](https://kubernetes.io/docs/concepts/services-networking/service/#externalname). | "100ms" | No |
| `routes[n].`<br />`services[m].`<br />`healthCheck.method` | HTTP method for the health check endpoint.<br />Evaluated only if the kind is **Service**.<br />Only for [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service/) of type [ExternalName](https://kubernetes.io/docs/concepts/services-networking/service/#externalname). | "GET" | No |
| `routes[n].`<br />`services[m].`<br />`healthCheck.status` | Expected HTTP status code of the response to the health check request.<br />Only for [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service/) of type [ExternalName](https://kubernetes.io/docs/concepts/services-networking/service/#externalname).<br />If not set, expect a status between 200 and 399.<br />Evaluated only if the kind is **Service**. |  | No |
| `routes[n].`<br />`services[m].`<br />`healthCheck.port` | URL port for the health check endpoint.<br />Evaluated only if the kind is **Service**.<br />Only for [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service/) of type [ExternalName](https://kubernetes.io/docs/concepts/services-networking/service/#externalname). | | No |
| `routes[n].`<br />`services[m].`<br />`healthCheck.timeout` | Maximum duration to wait before considering the server unhealthy.<br />Evaluated only if the kind is **Service**.<br />Only for [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service/) of type [ExternalName](https://kubernetes.io/docs/concepts/services-networking/service/#externalname). | "5s" | No |
| `routes[n].`<br />`services[m].`<br />`healthCheck.hostname` | Value in the Host header of the health check request.<br />Evaluated only if the kind is **Service**.<br />Only for [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service/) of type [ExternalName](https://kubernetes.io/docs/concepts/services-networking/service/#externalname). | "" | No |
| `routes[n].`<br />`services[m].`<br />`healthCheck.`<br />`followRedirect` | Follow the redirections during the healtchcheck.<br />Evaluated only if the kind is **Service**.<br />Only for [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service/) of type [ExternalName](https://kubernetes.io/docs/concepts/services-networking/service/#externalname). | true | No |
| `routes[n].`<br />`services[m].`<br />`healthCheck.headers` | Map of header to send to the health check endpoint<br />Evaluated only if the kind is **Service**.<br />Only for [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service/) of type [ExternalName](https://kubernetes.io/docs/concepts/services-networking/service/#externalname. |  | No |
| `routes[n].`<br />`services[m].`<br />`sticky.`<br />`cookie.name` | Name of the cookie used for the stickiness.<br />When sticky sessions are enabled, a `Set-Cookie` header is set on the initial response to let the client know which server handles the first response.<br />On subsequent requests, to keep the session alive with the same server, the client should send the cookie with the value set.<br />If the server pecified in the cookie becomes unhealthy, the request will be forwarded to a new server (and the cookie will keep track of the new server).<br />Evaluated only if the kind is **Service**. | "" | No |
| `routes[n].`<br />`services[m].`<br />`sticky.`<br />`cookie.httpOnly` | Allow the cookie can be accessed by client-side APIs, such as JavaScript.<br />Evaluated only if the kind is **Service**. | false | No |
| `routes[n].`<br />`services[m].`<br />`sticky.`<br />`cookie.secure` | Allow the cookie can only be transmitted over an encrypted connection (i.e. HTTPS).<br />Evaluated only if the kind is **Service**. | false | No |
| `routes[n].`<br />`services[m].`<br />`sticky.`<br />`cookie.sameSite` | [SameSite](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie/SameSite) policy<br />Allowed values:<br />-`none`<br />-`lax`<br />`strict`<br />Evaluated only if the kind is **Service**. | "" | No |
| `routes[n].`<br />`services[m].`<br />`sticky.`<br />`cookie.maxAge` | Number of seconds until the cookie expires.<br />Negative number, the cookie expires immediately.<br />0, the cookie never expires.<br />Evaluated only if the kind is **Service**. | 0 | No |
| `routes[n].`<br />`services[m].`<br />`strategy` | Load balancing strategy between the servers.<br />RoundRobin is the only supported value yet.<br />Evaluated only if the kind is **Service**. | "RoundRobin" | No |
| `routes[n].`<br />`services[m].`<br />`weight` | Service weight.<br />To use only to refer to WRR TraefikService | "" | No |
| `routes[n].`<br />`services[m].`<br />`nativeLB` | Allow using the Kubernetes Service load balancing between the pods instead of the one provided by Traefik Hub API Gateway.<br />Evaluated only if the kind is **Service**. | false | No |
| `routes[n].`<br />`services[m].`<br />`nodePortLB` | Use the nodePort IP address when the service type is NodePort.<br />It allows services to be reachable when Traefik Hub APi Gateway runs externally from the Kubernetes cluster but within the same network of the nodes.<br />Evaluated only if the kind is **Service**. | false | No |
| `tls`        | TLS configuration.<br />Can be an empty value(`{}`):<br />A self signed is generated in such a case<br />(or the [default certificate](../tls/tlsstore.md) is used if it is defined.) |  | No |
| `tls.secretName`        | [Secret](https://kubernetes.io/docs/concepts/configuration/secret/) name used to store the certificate (in the same namesapce as the `IngressRoute`) | "" | No |
| `tls.`<br />`options.name`        | Name of the `TLSOption` to use.<br /> | "" | No |
| `tls.`<br />`options.namespace`        | Namespace of the `TLSOption` to use. | "" | No |
| `tls.certResolver`        | Name of the [Certificate Resolver](../../../../routing/routers/index.md#certresolver) to use to generate automatic TLS certificates. | "" | No |
| `tls.domains`        | List of domains to serve using the certificates generates (one `tls.domain`= one certificate).<br />More information in the [dedicated section](../../../../routing/routers/index.md#domains). | | No |
| `tls.`<br />`domains[n].main`        | Main domain name | "" | Yes |
| `tls.`<br />`domains[n].sans`        | List of alternative domains (SANs) |  | No |

<!-- markdownlint-enable MD013 -->
