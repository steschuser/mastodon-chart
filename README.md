# Mastodon chart

A Helm chart for [Mastodon](https://github.com/tootsuite/mastodon).
Mastodon is a free, open-source social network server.

This Helm chart is designed/tested with:

| Package | Version |
| ------- | ------- |
| Mastodon | `tootsuite/mastodon:v3.5.2` |
| Kubernetes | v1.20 |

running on microk8s

Still work in progress, the original assumes ReadWriteMany, which has been changed to ReadWriteOnce, breaking the upgrade process

## How to Install?
Copy and edit `secrets.yaml.sample` and `values.yaml.sample` to provide your
own `secrets.yaml` and `values.yaml` files, and then deploy the Helm chart
with: 

```
helm upgrade --install -f secrets.yaml mastodon .
```

### Maintainer & License

Maintained by Steffen Schwebel <steffen@schwebel.online>

Based on the version of timwalls, see here: https://snowgoons.ro/posts/2020-08-29-mastodon-on-kubernetes/
Based on the original chart developed by Ladicle - https://github.com/Ladicle/mastodon-chart
