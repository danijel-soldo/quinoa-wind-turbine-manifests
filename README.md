# Wind Turbine GitOps repo

The refactored manifest repository using OpenShift GitOps with ApplicationSets and git file generator for parameters.

The main file here is the `wind-turbine-app.yaml` - file which contains an ApplicationSet using 
- the `helm` chart folder for templates and 
- the `stages/*/config.js` files for parameters.

For easy install there is a shell script which does all kinds of installations that are needed.

## Fun fact

The application code repository is located in https://github.com/gmodzelewski/quinoa-wind-turbine.
The configuration (=manifests) repository is located in https://github.com/gmodzelewski/quinoa-wind-turbine-manifests.

## Installation

1. fork this repo
2. `0-github-secret.yaml`: fill credentials for github from user settings -> Developer settings -> Personal access tokens -> Fine-grained tokens
3. `0-quay-secret.yaml`: fill credentials for quay from Robot Accounts -> Create Robot Account -> Kubernetes Secret
4. copy image to your quay repo (```skopeo copy docker://quay.io/modzelewski/quinoa-wind-turbine:latest docker://quay.io/<yourname>/quinoa-wind-turbine```)
5. run `1-install.sh`
6. wait until everything is deployed
7. start playing

The dashboard is available under the `.../dashboard` route, username `developer`, password is `password` (can be changed in `/helm/templates/env/gitops-demo-<stage>/secret.yaml`).

**INFO: Install script is work-in-progress. Not all resources, that are used in some steps, are created fast enough. If it doesn't work, just run it multiple times.**

## Webhooks

1. Get routes from your stage and dev deployments
2. Create json webhooks in your GitHub application project forks to push to your Event Listener Routes
3. Profit

## Used stuff

This application uses multiple tools which are installed via script on a clean environment. The tools are installed via operator or helm charts. Here's the list:
- operators:
  - AMQ Streams
  - OpenShift Pipelines
  - OpenShift GitOps
- helm packages:
  - Reloader
  - Sealed Secrets

See the install shell script `1-install.sh` for further details`.
The applications can be installed manually via helm install as well. Change the values file according to your needs and install.

## Bugfixing:

#### PVC stuck - delete pvc

The pvcs are configured to be not deleted at helm uninstall. You should always delete them manually.

If some pvc gets stuck you can fix this via
```sh
oc patch pvc <pvc-name> -p '{"metadata":{"finalizers": []}}' --type=merge
```
