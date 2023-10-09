# OpenShift Builds Catalog
This repository contains all the supported ClusterBuildStrategies with Builds for Red Hat OpenShift.
Not sure what a ClusterBuildStrategy is? Let's start with "What is [Builds for Red Hat OpenShift](https://github.com/shipwright-io/operator/)".

There are two types of strategies, the `ClusterBuildStrategy` and the `BuildStrategy`. Both strategies define a shared group of steps, needed to fullfil the application build.
As the name indicates, a `ClusterBuildStrategy` is available cluster-wide, while a `BuildStrategy` is available within a namespace.

## Supportability scope of the ClusterBuildStrategies

| Name | Supported platforms | scope |
| ---- | ------------------- | ----- |
| [buildah](./clusterBuildStrategy/buildah/) | all | Supported |
| [source-to-image](./clusterBuildStrategy/source-to-image/) | linux/amd64 only | Supported |
| [ubi-buildpacks](./clusterBuildStrategy/ubi-buildpacks) | all | Dev Preview |

## Install ClusterBuildStrategies

The following command will install all the available ClusterBuildStrategies in the cluster:
```
$ oc apply -R -f clusterBuildStrategy
```

In order to install a specific ClusterBuildStrategy, perform the following:
```
$ oc apply -f clusterBuildStrategy/<strategy_name>
```