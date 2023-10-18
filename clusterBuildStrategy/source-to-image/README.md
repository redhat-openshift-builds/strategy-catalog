# Source-To-Image (S2I) ClusterBuildStrategy
The `source-to-image` ClusterBuildStrategy is composed of [source-to-image](https://github.com/openshift/source-to-image/) and [buildah](https://github.com/containers/buildah) in order to generate a `Dockerfile` and prepare the application to be built later with a builder-image.

`s2i` requires a specially crafted image, which can be informed as `builder-image` parameter on the `Build` resource.

## Install the Strategy

```
$ oc apply -f https://raw.githubusercontent.com/redhat-developer/openshift-builds-catalog/main/clusterBuildStrategy/source-to-image/source_to_image.yaml
```

## Usage
This Build uses source-to-image-redhat strategy to build an image, and pushes the built image to a quay repository (`output.image`).

```yaml
apiVersion: shipwright.io/v1beta1
kind: Build
metadata:
  name: s2i-nodejs-build
spec:
  source:
    git:
      url: https://github.com/shipwright-io/sample-nodejs
    contextDir: source-build/
  strategy:
    name: source-to-image
    kind: ClusterBuildStrategy
  paramValues:
  - name: builder-image
    value: "quay.io/centos7/nodejs-12-centos7"
  - name: registries-block
    values:
    - value: docker.io 
  output:
    image: quay.io/repo/s2i-nodejs-example
    pushSecret: registry-credential
```

## Parameters

| Name | Type | Description | Default |
| ---- | ---- | ----------- | ------- |
| registries-block | array | List of registries that needs to be blocked | [] |
| registries-insecure | array | FQDN of required insecure registries | [] |
| registries-search | array | List of registries that are preferred when short name images are specified | ["registry.redhat.io", "quay.io"] |
| builder-image | string | Location of the builder-image | "" |
| storage-driver | string | The storage drivers to be used by buildah ("overlay" or "vfs") | "vfs" |
