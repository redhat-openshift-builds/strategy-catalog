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
    - name: RUN_IMAGE
      value: paketocommunity/run-ubi-base:latest
    - name: CNB_BUILDER_IMAGE
      value: paketobuildpacks/builder-jammy-tiny:0.0.176
    - name: APP_IMAGE
      value: image-registry.openshift-image-registry.svc:5000/buildpacks-example/taxi-app
  output:
    image: image-registry.openshift-image-registry.svc:5000/buildpacks-example/taxi-app
```

## Parameters
| Name               | Type   | Description                                           | Default                                       |
| ------------------ | ------ | ----------------------------------------------------- | --------------------------------------------- |
| CNB_PLATFORM_API   | string | Platform API Version supported                        | "0.12"                                        |
| CNB_BUILDER_IMAGE  | string | Builder image containing the buildpacks               | ""                                            |
| CNB_LIFECYCLE_IMAGE | string | Image to use when executing Lifecycle phases          | "docker.io/buildpacksio/lifecycle:0.17.0"      |
| APP_IMAGE          | string | Name of where to store the app image                   | - (No Default)                                |
| RUN_IMAGE          | string | Reference to a run image to use                        | ""                                            |
| CACHE_IMAGE        | string | Name of the persistent app cache image                 | ""                                            |
| CACHE_DIR_NAME     | string | Directory to cache files                               | "cache"                                       |
| PROCESS_TYPE       | string | Default process type to set on the image               | ""                                            |
| SOURCE_SUBPATH     | string | Subpath within the `source` input where the source to build is located | ""                             |
| ENV_VARS           | array  | Environment variables to set during _build-time_      | []                                            |
| PLATFORM_DIR       | string | Name of the platform directory                         | "empty-dir"                                   |
| USER_ID            | string | User ID of the builder image user                      | "1001"                                        |
| GROUP_ID           | string | Group ID of the builder image user                     | "1000"                                        |
| USER_HOME          | string | Absolute path to the user's home directory             | "/tekton/home"                                |
| CACHE_PVC_NAME     | string | Name of the Persistent Volume Claim for cache          | "ws-pvc"                                      |









