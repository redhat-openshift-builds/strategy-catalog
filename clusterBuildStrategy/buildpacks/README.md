# Buildpacks ClusterBuildStrategy
The `buildpacks` ClusterBuildStrategy uses [buildpacks](https://buildpacks.io/) to efficiently build and push a container image based on the source code, without the need for a traditional Dockerfile. Instead of a Dockerfile, configuration details are provided through parameters in the `Build` resource.

## Install the Strategy

```
$ oc apply -f https://raw.githubusercontent.com/redhat-developer/openshift-builds-catalog/main/clusterBuildStrategy/buildpacks/buildpacks.yaml
```

## Usage
This example uses the buildpacks strategy to build an image, and pushes the built image to OpenShift's internal registry (`output.image`). The following example assumes the OpenShift internal registry is enabled and the `BuildRun` executes in the `buildpacks-example` namespace:

```yaml
apiVersion: shipwright.io/v1beta1
kind: Build
metadata:
  name: buildpack-nodejs-build
spec:
  source:
    type: Git
    git: 
      url: https://github.com/redhat-openshift-builds/samples.git
  strategy:
    name: buildpacks
    kind: ClusterBuildStrategy
  retention:
    atBuildDeletion: true
  paramValues:
    - name: run-image
      value: paketobuildpacks/run-ubi8-base:latest
    - name: cnb-builder-image
      value: paketobuildpacks/builder-jammy-tiny:0.0.344
    - name: source-subpath
      value: "buildpacks"
  output:
    image: image-registry.openshift-image-registry.svc:5000/buildpacks-example/taxi-app
```

## Parameters

| Name                      | Type   | Description                                                                      | Default                     |
|---------------------------|--------|----------------------------------------------------------------------------------|----------------------------|
| cnb-platform-api         | string | Platform API Version supported                                                  | "0.12"                     |
| cnb-builder-image         | string | Builder image containing the buildpacks                                         | ""                         |
| cnb-lifecycle-image       | string | Image to use when executing Lifecycle phases                                    | "buildpacksio/lifecycle:0.20.8" |
| cnb-log-level             | string | Logging levels                                                                   | "debug"                    |
| run-image                 | string | Reference to a run image to use                                                 | ""                         |
| cache-image               | string | Name of the persistent app cache image                                          | ""                         |
| process-type              | string | Default process type to set on the image                                        | ""                         |
| source-subpath            | string | Subpath within the `source` input where the source to build is located         | ""                         |
| env-vars                  | array  | Environment variables to set during _build-time_                                | []                         |
| platform-dir             | string | Name of the platform directory                                                  | "empty-dir"                |
| user-id                   | string | User ID of the builder image user                                               | "1001"                     |
| group-id                  | string | Group ID of the builder image user                                              | "1000"                     |
| user-home                 | string | Absolute path to the user's home directory                                      | "/tekton/home"             |
| cache-pvc-name            | string | Name of the Persistent Volume Claim for cache                                   | "ws-pvc"                   |
| cnb-extender-kind         | string | The kind of image to extend ('build' or 'run')                                  | "build"                    |
| cnb-extended-dir-exporter | string | Directory of extended layers, passed as -extended flag to the exporter         | "/layers/extended"         |
