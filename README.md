# Vault demo

## Prerequisites

- Helm. `Helm init`.
- Etcd cluster, see below.

### Etcd installation

The etcd is installed using the [etcd-operator](https://github.com/helm/charts/tree/master/stable/etcd-operator) from CoreOS

1. Install the helm chart

    `helm install stable/etcd-operator --name etcd-operator --values etcd-operator/values.yaml --namespace etcd`

1. Install an ectd-cluster `kubectl apply -f etcd-operator/etcd-cluster.yaml`

> NB: it _should_ be possible to do this with the chart, but
>
> - A: installing with custom resource definitions (crd), complain because they CRDs
> are added from the application.
> - B: installing without crd and afterwards doing a `helm upgrade` to add the crd
> results in no crd being added, because it has a `"helm.sh/hook": post-install`
> which is only triggered after a `helm install` and not an `helm upgrade`.
>
> For now just apply the `etcd-cluster.yaml` manually.

## Installation

1. Add `incubator` repo

    `helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator`

1. Install helm chart:

    `helm install incubator/vault --name vault --namespace vault --values .\vault\values.yaml`

## Initialize

1. Exec into a vault container.
1. Init vault with

    `vault operator init --key-shares=1 --key-threshold=1 --address=http://127.0.0.1:8200`

    > We specify the address because it defaults to https. See:
    > [tls_disable not disabling, hashicorp/vault#1147](https://github.com/hashicorp/vault/issues/1147)

1. Receive keys from output, sth like:

        Key 1: <some-key>
        Operation nonce: <some-nonce>

## TODO

- Vault HA
  - ~~Use etcd HA backend~~
- Busybox secret initcontainer example
- Busybox secret sidecar example
- Everything TLS / HTTPS
