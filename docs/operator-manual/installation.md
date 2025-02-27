# Installation

Argo CD has two type of installations: multi-tenant and core.

## Multi-Tenant

The multi-tenant installation is the most common way to install Argo CD. This type of installation is typically used to service multiple application developer teams
in the organization and maintained by a platform team.

The end-users can access Argo CD via the API server using the Web UI or `argocd` CLI. The `argocd` CLI has to be configured using `argocd login <server-host>` command
(learn more [here](../user-guide/commands/argocd_login.md)).

Two types of installation manifests are provided:

### Non High Availability:

Not recommended for production use. This type of installation is typically used during evaluation period for demonstrations and testing.

* [install.yaml](https://github.com/argoproj/argo-cd/blob/master/manifests/install.yaml) - Standard Argo CD installation with cluster-admin access. Use this
  manifest set if you plan to use Argo CD to deploy applications in the same cluster that Argo CD runs
  in (i.e. kubernetes.svc.default). It will still be able to deploy to external clusters with inputted
  credentials.

* [namespace-install.yaml](https://github.com/argoproj/argo-cd/blob/master/manifests/namespace-install.yaml) - Installation of Argo CD which requires only
  namespace level privileges (does not need cluster roles). Use this manifest set if you do not
  need Argo CD to deploy applications in the same cluster that Argo CD runs in, and will rely solely
  on inputted cluster credentials. An example of using this set of manifests is if you run several
  Argo CD instances for different teams, where each instance will be deploying applications to
  external clusters. It will still be possible to deploy to the same cluster (kubernetes.svc.default)
  with inputted credentials (i.e. `argocd cluster add <CONTEXT> --in-cluster --namespace <YOUR NAMESPACE>`).

  > Note: Argo CD CRDs are not included into [namespace-install.yaml](https://github.com/argoproj/argo-cd/blob/master/manifests/namespace-install.yaml).
  > and have to be installed separately. The CRD manifests are located in the [manifests/crds](https://github.com/argoproj/argo-cd/blob/master/manifests/crds) directory.
  > Use the following command to install them:
  > ```
  > kubectl apply -k https://github.com/argoproj/argo-cd/manifests/crds\?ref\=stable
  > ```

### High Availability:

High Availability installation is recommended for production use. This bundle includes the same components but tuned for high availability and resiliency.

* [ha/install.yaml](https://github.com/argoproj/argo-cd/blob/master/manifests/ha/install.yaml) - the same as install.yaml but with multiple replicas for
  supported components.

* [ha/namespace-install.yaml](https://github.com/argoproj/argo-cd/blob/master/manifests/ha/namespace-install.yaml) - the same as namespace-install.yaml but
  with multiple replicas for supported components.

## Core

The Argo CD Core installation is primarily used to deploy Argo CD in
headless mode. This type of installation is most suitable for cluster
administrators who independently use Argo CD and don't need
multi-tenancy features. This installation includes fewer components
and is easier to setup. The bundle does not include the API server or
UI, and installs the lightweight (non-HA) version of each component.

Installation manifest is available at [core-install.yaml](https://github.com/argoproj/argo-cd/blob/master/manifests/core-install.yaml).

For more details about Argo CD Core please refer to the [official
documentation](./core.md)

## Kustomize

The Argo CD manifests can also be installed using Kustomize. It is recommended to include the manifest as a remote resource and apply additional customizations
using Kustomize patches.


```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

namespace: argocd
resources:
- github.com/argoproj/argo-cd/manifests/ha?ref=v2.6.2
```

## Helm

The Argo CD can be installed using [Helm](https://helm.sh/). The Helm chart is currently community maintained and available at
[argo-helm/charts/argo-cd](https://github.com/argoproj/argo-helm/tree/main/charts/argo-cd).

## Supported versions

Similar to the Kubernetes project, the supported versions of Argo CD at any given point in time are the latest patch releases for the N 
and N - 1 minor versions.
These Argo CD versions are supported on the same versions of Kubernetes that are supported by Kubernetes itself (normally the last 3 released versions).

Essentially the Argo CD project follows the same support scheme as Kubernetes but for N, N-1 while Kubernetes supports N, N-1, N-2 versions.

For example if the latest minor version of ArgoCD are 2.4.3 and 2.3.5  while supported Kubernetes versions are 1.24, 1.23 and 1.22 then the following combinations are supported:

* Argo CD 2.4.3 on Kubernetes 1.24
* Argo CD 2.4.3 on Kubernetes 1.23
* Argo CD 2.4.3 on Kubernetes 1.22
* Argo CD 2.3.5 on Kubernetes 1.24
* Argo CD 2.3.5 on Kubernetes 1.23
* Argo CD 2.3.5 on Kubernetes 1.22
