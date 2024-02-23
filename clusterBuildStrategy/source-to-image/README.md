# Source-To-Image (S2I) ClusterBuildStrategy

The `source-to-image` ClusterBuildStrategy allows source code to be compiled and packaged into a
container image using the [source-to-image](https://github.com/openshift/source-to-image) tool.
Its functionality is similar to the `Source` build strategy using
[OpenShift BuildConfigs](https://docs.openshift.com/container-platform/latest/cicd/builds/build-strategies.html#builds-strategy-s2i-build_build-strategies-docker)

Developers must provide an s2i-enabled builder image as the base image, using the `builder-image` parameter.

## Install the Strategy

```
$ oc apply -f https://raw.githubusercontent.com/redhat-developer/openshift-builds-catalog/main/clusterBuildStrategy/source-to-image/source_to_image.yaml
```

## Usage

This example uses the source-to-image strategy to build an image, and pushes the built image to a quay repository (`output.image`).
It assumes the following:

- The Samples Operator is enabled and installed the default OpenShift ImageStreams.
- `<my-repo>` is a quay.io user or organization repository, and the secret
  `registry-credential` contains credentials that allow the build to push images to the repository.

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
    value: image-registry.openshift-image-registry.svc:5000/openshift/nodejs:16-ubi9
  - name: registries-block
    values:
    - value: docker.io 
  output:
    image: quay.io/<my-repo>/s2i-nodejs-example
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
