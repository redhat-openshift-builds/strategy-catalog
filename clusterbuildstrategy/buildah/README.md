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

### Environment variables

`build-env` adds an `ENV` instruction at the start of every stage of the Dockerfile,
through `buildah bud --env`. `RUN` instructions see the values, the output image keeps
them, and an `ENV` in the Dockerfile itself still overrides them. `spec.env` on the Build
is different: it only sets the environment of the build container, and `RUN` does not
see it.

A bare name takes its value from the Build's `spec.env`, so a value kept in a ConfigMap
or Secret does not have to be copied into `paramValues`. The build fails with
`InvalidBuildEnv` if the name is not set there:

```yaml
spec:
  env:
  - name: PIP_INDEX_URL
    valueFrom:
      configMapKeyRef:
        name: pip-config
        key: index-url
  paramValues:
  - name: build-env
    values:
    - value: APP_ENV=production
    - value: PIP_INDEX_URL
```

Every value ends up in the output image's configuration and is printed in the build log,
once per stage, as part of the `ENV` step. Do not use `build-env` for credentials.

A `$` in a value is expanded the way it is in a Dockerfile `ENV`, so `KEY=ab$cd` sets
`KEY` to `ab`. OpenShift's Docker strategy did the same with `dockerStrategy.env`. No
escape gets a literal `$` through, so set such a value with an `ENV` line in the
Dockerfile instead.

## Parameters

| Name | Type | Description | Default |
| ---- | ---- | ----------- | ------- |
| build-args | array | Key-Value pair of the args required by the Dockerfile used during the build | [] |
| build-env | array | Environment variables set at the start of every stage, so `RUN` instructions see them and the output image keeps them. Each entry is `KEY=VALUE`, or a bare `NAME` whose value comes from the Build's `spec.env` and must be set there. Wildcard entries such as `*` are rejected | [] |
| registries-block | array | List of registries that needs to be blocked | [] |
| registries-insecure | array | FQDN of required insecure registries | [] |
| registries-search | array | List of registries that are preferred when short name images are specified | ["registry.redhat.io", "quay.io"] |
| dockerfile | string | Path of the Dockerfile to be used during the build | "Dockerfile" |
| storage-driver | string | The storage drivers to be used by buildah ("overlay" or "vfs") | "vfs" |
