# Buildah ClusterBuildStrategy

The `buildah` ClusterBuildStrategy uses [buildah](https://github.com/containers/buildah) to build
and push a container image, out of a `Dockerfile` or `Containerfile`. The `Dockerfile` should be
specified using the `dockerfile` parameter in the `Build` resource.

## Install the Strategy

```
$ oc apply -f https://raw.githubusercontent.com/redhat-developer/openshift-builds-catalog/main/clusterBuildStrategy/buildah/buildah.yaml
```

## Usage

This example uses the buildah strategy to build an image using a Dockerfile, and pushes the image
to OpenShift's internal registry (`output.image`). The following example assumes the OpenShift
internal registry is enabled and the `BuildRun` executes in the `buildah-sample` namespace:

```yaml
apiVersion: shipwright.io/v1beta1
kind: Build
metadata:
  name: buildah-golang-build
spec:
  source:
    git: 
      url: https://github.com/shipwright-io/sample-go
    contextDir: docker-build
  strategy:
    name: buildah
    kind: ClusterBuildStrategy
  paramValues:
  - name: dockerfile
    value: Dockerfile
  output:
    image: image-registry.openshift-image-registry.svc:5000/buildah-example/sample-go-app
```

## Parameters

| Name | Type | Description | Default |
| ---- | ---- | ----------- | ------- |
| build-args | array | Key-Value pair of the args required by the Dockerfile used during the build | [] |
| registries-block | array | List of registries that needs to be blocked | [] |
| registries-insecure | array | FQDN of required insecure registries | [] |
| registries-search | array | List of registries that are preferred when short name images are specified | ["registry.redhat.io", "quay.io"] |
| dockerfile | string | Path of the Dockerfile to be used during the build | "Dockerfile" |
| storage-driver | string | The storage drivers to be used by buildah ("overlay" or "vfs") | "vfs" |
