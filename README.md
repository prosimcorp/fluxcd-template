# FluxCD Template

## Important

> **ATTENTION**
>
> Before diving deeper in the specific documentation of this repository, you must know that is part of an entire flow
> composed by several repositories. In the following diagram you will have a better insight.

```text
                                                                +----------------------+
                                                         +----->|  3. Tooling Stack    |
                                                         |      +----------------------+
                                                         |
+-----------------------+       +--------------------+   |      +----------------------+
| 1. Automated EKS/GKE  +------>| 2. FluxCD Template +---+----->|  4. Monitoring Stack |
+-----------------------+       +--------------------+   |      +----------------------+
                                                         |
                                                         |      +----------------------+
                                                         +----->|  5. Applications     |
                                                                +----------------------+
```

## Description

This is an template repository to be deployable on any cloud provider's Kubernetes distribution, such as EKS or GKE,
and it shows how to organize files and directories inside for FluxCD to operate a whole cluster using GitOps approach.

## Constraints

The goal of this repository is achieved **following the following constraints**:

- One repository per Kubernetes cluster
- All possible overlays must be allocated in one place
- Flux, Infrastructure and applications' deployments must be separated to allow teams operate faster
- All the overlays must be applied by using Kustomization patches in separated files

   > **ATTENTION**
   >
   > Most tools are previously polished by vendors to be production ready. Because of that, the best practise is to deploy
   > them with the configuration as default as possible. Don't try to reinvent the wheel that huge companies already invented.
   >
   > Your mission with the patches is to make it automated and reliable. If some bug appears, go to the vendor's repository
   > and collaborate there to solve the root cause there for everyone, not here.

## Organization

This organization make it faster to operate the repository. There are several directories you may saw:

- `flux-system/`
   This one is only for Flux usage and will be updated by it. It is isolated because Flux not only interacts with
   the repository but changes it.
- `infrastructure/`
   Directory to deploy the common controllers used as a base for the whole cluster. Basically the things
   that developers will use within their applications
- `applications/`
   Space dedicated to deploy the applications related to the products
- `overlays/`
   Directory to put the patches that will be applied by Kustomization on runtime. They must be separated to control
   the scope of each patch. They must be included on the `kustomization.yaml` file to be applied

> Each directory has its own `kustomization.yaml` file to include the deployments that will be applied for that scope.
>
> `overlays/` directory is the only one that has NOT a `kustomization.yaml` file.
> Done this way to apply patches only when and where needed:
> For example, on Flux deployment time, only one is required, but after it, they all are needed.

## Requirements

### Tooling Stack

Each cluster is bootstrapped with several operators and controllers which are needed, by SREs or developers, and not
included in Kubernetes by default. They are deployed as a package to make them easier to maintain. That package is
called **Tooling Stack** and is better documented inside
[its original repository](https://github.com/prosimcorp/tooling-stack)

### Monitoring Stack

For reliability and observability purposes, all clusters include another package with some other tools, intended to collect
logs, metrics, show them using dashboards or emit alerts to Slack. One more time, they are not included in Kubernetes by default.

This package is called **Monitoring Stack** better documented inside
[its original repository](https://github.com/prosimcorp/monitoring-stack)

## How to operate

You can do the magic in just a few steps:

1. Fork this repository on Git and name it with a descriptive `lower-kebab-case`.
2. Change the patches on `overlays/` folder to fit your needs. You will find some `{{ VARIABLES_TO_CHANGE }}`
3. Point your FluxCD GitRepository `flux-system` to that repository and wait

Drink beers, cheers and enjoy 🍻 🎉 🎉
