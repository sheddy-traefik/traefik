---
title: "TLSStore"
description: "TLS Store in Traefik Proxy"
---

In Traefik, certificates are grouped together in certificates stores. 

`TLSStore` is the CRD implementation of a [Traefik TLS Store](./tlsstore.md). Register the `TLSStore` kind in the Kubernetes cluster before creating `TLSStore` objects.

!!! Tip "Default TLS Store"
    Traefik currently only uses the TLS Store named "default". This default `TLSStore` should be in a namespace discoverable by Traefik. Since it is used by default on `IngressRoute` and `IngressRouteTCP` objects, there never is a need to actually reference it. This means that you cannot have two stores that are named default in different Kubernetes namespaces. As a consequence, with respect to TLS stores, the only change that makes sense (and only if needed) is to configure the default `TLSStore`.

## Configuration Example

```yaml tab="TLSStore"
apiVersion: traefik.io/v1alpha1
kind: TLSStore
metadata:
  name: default
  
spec:
  defaultCertificate:
    secretName:  supersecret
```

```yaml tab="IngressRoute"
apiVersion: traefik.io/v1alpha1
kind: IngressRoute # OR IngressRouteTCP
metadata:
  name: ingressroutebar

spec:
  entryPoints:
    - websecure
  routes:
  - match: Host(`example.com`) && PathPrefix(`/stripit`)
    kind: Rule
    services:
    - name: whoami
      port: 80
  tls: {}
```

```yaml tab="Secret"
apiVersion: v1
kind: Secret
metadata:
  name: supersecret

data:
  tls.crt: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0=
  tls.key: LS0tLS1CRUdJTiBQUklWQVRFIEtFWS0tLS0tCi0tLS0tRU5EIFBSSVZBVEUgS0VZLS0tLS0=
```

## Configuration Options

| Field                                  | Description                                                                                                                                                                                                | Default  | Required |
|:---------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------|:---------|
| `name`                                 | Name of the TLS Store. Only the `default` store name is taken into account yet.  |          | Yes      |
| `certificates`                         | List of Kubernetes [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/), each of them holding a key/certificate pair to add to the store. List item format: secretName: $secret_name|          | No      |
| `defaultCertificate.secretName`        | Kubernetes [Secret](https://kubernetes.io/docs/concepts/configuration/secret/) served for connections without a SNI, or without a matching domain.<br /> If no default certificate is provided, Traefik will use the generated one.<br /> Do not use if the option `defaultGeneratedCert` is set. |                                          | No      |
| `defaultGeneratedCert.resolver`        | Name of the ACME resolver to use to generate the default certificate.<br /> Do not use if the option `defaultCertificate` is set.  |          | No      |
| `defaultGeneratedCert.domain.main`     | Main domain used to generate the default certificate.<br /> Do not use if the option `defaultCertificate` is set.   |          | No      |
| `defaultGeneratedCert.domain.sans`     | List of [Subject Alternative Name](https://en.wikipedia.org/wiki/Subject_Alternative_Name) used to generate the default certificate.<br /> Do not use if the option `defaultCertificate` is set.  |          | No      |

!!! note "DefaultCertificate vs DefaultGeneratedCert"
    If both defaultCertificate and defaultGeneratedCert are set, the TLS certificate contained in defaultCertificate.secretName is served. The ACME default certificate is not generated.