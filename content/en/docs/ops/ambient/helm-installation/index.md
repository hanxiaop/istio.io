---
title: Installing Ambient Mesh with Helm
description: How to install Ambient Mesh with Helm.
weight: 4
owner: istio/wg-environments-maintainers
test: n/a
---

This guide shows you how to install Ambient Mesh with Helm.
Besides the demo in [Getting Started with Ambient Mesh](/docs/ops/ambient/getting-started/),
we **encourage** you to follow this guide to install Ambient Mesh.
Helm helps you manage components separately, and you can easily upgrade the components to the latest version.

## Prerequisites

1. Perform any necessary [platform-specific setup](/docs/setup/platform-setup/).

1. Check the [Requirements for Pods and Services](/docs/ops/deployment/requirements/).

1. [Install the Helm client](https://helm.sh/docs/intro/install/), version 3.6 or above.

1. Configure the Helm repository:

    {{< text bash >}}
    $ helm repo add istio https://istio-release.storage.googleapis.com/charts
    $ helm repo update
    {{< /text >}}

*See [helm repo](https://helm.sh/docs/helm/helm_repo/) for command documentation.*

## Installing the Components

### Installing Base Component

The **Base** chart contains the basic CRDs and cluster roles required to set up Istio.
This should be installed prior to any other Istio component.

{{< text bash >}}
$ helm install istio-base istio/base
{{< /text >}}

### Installing CNI Component

The **CNI** chart installs the Istio CNI Plugin. It is responsible for detecting the pods that belong to the ambient mesh,
and configuring the traffic redirection between the ztunnels - which will be installed later.

{{< text bash >}}
$ helm install istio-cni istio/cni -n kube-system \
  -f @manifests/charts/istio-cni/ambient-values.yaml@
{{< /text >}}

### Installing Istiod Component

The **Istiod** chart installs a revision of Istiod. Istiod is the control plane component that manages and
configures the proxies to route traffic within the mesh.

{{< text bash >}}
$ helm install istiod istio/istiod --namespace istio-system --create-namespace \
  -f @manifests/charts/istio-control/istio-discovery/ambient-values.yaml@
{{< /text >}}

### Installing Ztunnel Component

The **Ztunnel** chart installs a ztunnel, which is the node-proxy component in Ambient.

{{< text bash >}}
$ helm install ztunnel istio/ztunnel -n istio-system
{{< /text >}}

## Verifying the Installation

After installing all the components, you can check the helm deployment status:

{{< text bash >}}
$ helm list -n istio-system
{{< /text >}}

You can check the status of the pods deployed:

{{< text bash >}}
$ kubectl get pods -n istio-system
{{< /text >}}

## Configuration

To view support configuration options and documentation, run:

{{< text bash >}}
$ helm show values istio/istiod
{{< /text >}}
