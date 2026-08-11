# node-local-dns

A Helm chart for [NodeLocal DNSCache](https://kubernetes.io/docs/tasks/administer-cluster/nodelocaldns/):
a per-node DNS caching DaemonSet, extended with a first-class **cache warmer**
that keeps a configurable list of domains permanently cached and fresh on
every node.

Derived from the Apache-2.0
[deliveryhero/node-local-dns](https://github.com/deliveryhero/helm-charts/tree/master/stable/node-local-dns)
chart (v2.9.2). See `LICENSE`.

## Installing

Chart tarballs are attached to GitHub releases:

    https://github.com/holoplot/node-local-dns/releases/download/node-local-dns-<version>/node-local-dns-<version>.tgz

## Cache warmer

```yaml
cacheWarmer:
  enabled: true
  interval: 30
  domains:
    - registry.example.com
```

When enabled, a sidecar on each node-cache pod queries every listed domain
against the local cache each `interval` seconds. Combined with
`config.prefetch`, this keeps the domains present and fresh in every node's
cache; with `config.serveStale` they remain resolvable during upstream
outages. Changing `domains` re-renders a ConfigMap whose checksum is
annotated on the pod template, so the DaemonSet rolls automatically.

The warmer runs as a native sidecar container (an init container with
`restartPolicy: Always`), which requires Kubernetes >= 1.29.
