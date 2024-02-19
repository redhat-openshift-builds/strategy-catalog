# Buildpacks ClusterBuildStrategy
The `buildpacks` ClusterBuildStrategy uses [buildpacks](https://buildpacks.io/) to efficiently build and push a container image based on the source code, without the need for a traditional Dockerfile. Instead of a Dockerfile, configuration details are provided through parameters in the Build resource.

## Install the Strategy

```
$ oc apply -f https://raw.githubusercontent.com/redhat-developer/openshift-builds-catalog/main/clusterBuildStrategy/buildpacks/buildpacks.yaml
```

## Usage
This Build uses buildpacks strategy to build an image , and pushes the built image to OpenShift's internal registry (`output.image`).

```yaml
apiVersion: shipwright.io/v1beta1
kind: Build
metadata:
  name: buildpack-nodejs-build
spec:
  source:
    type: Git
    git: 
      url: https://github.com/ayushsatyam146/node-example.git
    contextDir: source-build
  strategy:
    name: buildpacks
    kind: ClusterBuildStrategy
  retention:
    atBuildDeletion: true
  paramValues:
    - name: run-image
      value: paketocommunity/run-ubi-base:latest
    - name: cnb-builder-image
      value: paketobuildpacks/builder-jammy-tiny:0.0.176
  output:
    image: image-registry.openshift-image-registry.svc:5000/buildpacks-example/taxi-app
```

## Parameters
| Name               | Type   | Description                                           | Default                                       |
| ------------------ | ------ | ----------------------------------------------------- | --------------------------------------------- |
| cnb-platform-api   | string | Platform API Version supported                        | "0.12"                                        |
| cnb-builder-image  | string | Builder image containing the buildpacks               | ""                                            |
| cnb-lifecycle-image | string | Image to use when executing Lifecycle phases          | "docker.io/buildpacksio/lifecycle:0.17.0"      |                                          |
| run-image          | string | Reference to a run image to use                        | ""                                            |
| cache-image        | string | Name of the persistent app cache image                 | ""                                            |
| cache-dir-name     | string | Directory to cache files                               | "cache"                                       |
| process-type       | string | Default process type to set on the image               | ""                                            |
| source-subpath     | string | Subpath within the `source` input where the source to build is located | ""                             |
| env-vars           | array  | Environment variables to set during _build-time_      | []                                            |
| platform-dir       | string | Name of the platform directory                         | "empty-dir"                                   |
| user-id            | string | User ID of the builder image user                      | "1001"                                        |
| group-id           | string | Group ID of the builder image user                     | "1000"                                        |
| user-home          | string | Absolute path to the user's home directory             | "/tekton/home"                                |
| cache-pvc-name     | string | Name of the Persistent Volume Claim for cache          | "ws-pvc"                                      |