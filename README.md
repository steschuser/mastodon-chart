# Mastodon chart

A Helm chart for [Mastodon](https://github.com/tootsuite/mastodon). Mastodon is a free, open-source social network server.

Updated to support the latest version of the Mastodon image on Dockerhub, and for K8s v1.17.

## Snowgoons-specific changes
This chart is optimised for the chronically [underpowered cluster](https://snowgoons.ro/posts/2020-05-11-snowgoonsplatform/)
that I use to host [snowgoons.ro](https://snowgoons.ro).  So, in this branch
we have:

* Separated the Sidekiq deployment into four separate deployments (one for 
  each of the queues - mailer, pull, push and default).  This allows me to
  spread them round the cluster a little and thus spread the load.  The
  pull queue in particular does a load of image processing (resizing images
  to make thumbnails, that sort of thing) when it pulls in toots from other
  servers, which can make it rather heavy.  Left to its own devices, it will
  block all the other queues - this deployment lets it chew CPU on its own
  without killing everything else.
  
* A somewhat 'unique' Ingress arrangement.  I use [Cloudflare Argo](https://www.cloudflare.com/products/argo-smart-routing/)
  to route to my Kubernetes cluster that lives on my home DSL, this has a
  few limitations - the Argo agent runs in a DMZ namespace on my cluster,
  so to let it talk across the namespaces (I like to deploy each application
  in its own namespace) I use a simple HAProxy load-balancer to route the
  traffic.  The ```ingress.yaml``` in this branch can be considered distinctly
  custom, therefore.
  
* A dedicated Apache webserver instance serves up the static content, more efficiently
  than passing those requests through to the main Rail apps - it also adds
  headers which ask Cloudflare (my CDN) to cache those files for a good
  long time.  (Why Apache and not nginx?  Nginx has some SSE instruction
  dependencies which cause it to occasionally coredump on my Intel Atom based
  servers.  I could probably fix this by compiling a new image from source,
  or I can just ignore it and use Apache...)

## How to Install?

``` shell
helm upgrade --install -f secrets.yaml mastodon .
```
