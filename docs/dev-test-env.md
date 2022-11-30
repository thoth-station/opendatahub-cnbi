
This document describes the setup to develop and/or test the Custom Notebook Image (CNBi) functionality.

- Stage Setup: [Stage](#stage-demo-environment)
- Local Setup: [Local](#local-dev-environment)


## Stage/Demo environment

### Target cluster and context

Target cluster for CNBi CD: **OS-Climate's Cluster1** (`odh-cl1`) cluster.

The end goal is to use that environment to deploy *Custom Notebook Images* <br> for the various data science-related repositories from the OS-Climate (OSC) project <br>into the [OpenDataHub (ODH)](https://opendatahub.io) cluster in `odh-cl1`.

### Deployment

#### OpenDatahub Components

The deployment of opendatahub components is driven by the [odh-manifests](https://github.com/opendatahub-io/odh-manifests/) and [Opendatahub-operator](https://github.com/opendatahub-io/opendatahub-operator).


* Operator:

Operate First deploys the OpenDatahub operatorhub using manual method.
- [Manifest for operator](https://github.com/operate-first/apps/blob/master/cluster-scope/bundles/opendatahub-operator-manual/kustomization.yaml)
- [Deployment inclusion](https://github.com/operate-first/apps/blob/master/cluster-scope/overlays/prod/osc/osc-cl1/kustomization.yaml#L67)

* Components:

Operate First maintains a [fork of odh-manifests](https://github.com/operate-first/odh-manifests) where the manifests are customized in branches that correspond to the target clusters.

The [osc-cl1-byon branch on that fork](https://github.com/operate-first/odh-manifests/tree/osc-cl1-byon) is used for ODH deployment to the **OSC cluster1**.

- The target namespace for the deployment of Thoth's CNBi functionality components is **cnbi**.
- The Kfdef to allow OpenDataHub operator to deploy the components is available here: <https://github.com/operate-first/apps/tree/master/kfdefs/overlays/osc/osc-cl1/cnbi/kfdef.yaml>. That Kfdef:
  - uses the [meteor-operator repository](https://github.com/thoth-station/meteor-operator) to deploy the CNBi controller and pipeline manifests as defined there
    - the operator's manifests are under `config/`. In particular, the version of the controller to deploy is in the [manager kustomization file](https://github.com/thoth-station/meteor-operator/blob/7f023d4834ada99d25670cf6b4622587f11d0b55/config/manager/kustomization.yaml#L16)
    - the Pipeline manifests are under [`pipelines/`](https://github.com/thoth-station/meteor-operator/tree/95a27eff3213d47fb3c76a2ec4ad5942d397d257/pipelines)
  - deploys [thoth-station's fork of the odh-dashboard](https://github.com/thoth-station/odh-dashboard) as specified in the [odh-manifests for osc-cl1-byon](https://github.com/operate-first/odh-manifests/blob/b75d8167f04e76907238d35aa9e451bbd7f0ca67/odh-dashboard/base/kustomization.yaml#L20)
  - also deploys standard ODH components: *odh-common*, *notebook-images* and *Jupyterhub*.

Operate First's ArgoCD is used to automate this process of deployment.

### CNBi Operator deployment

Each [tag in the meteor-operator repo](https://github.com/thoth-station/meteor-operator/tags) is automatically built by aicoe-ci and pushed to [quay.io](https://quay.io/repository/thoth-station/meteor-operator?tab=tags).

Custom versions of the operator can be built and packaged into an image with:

    VERSION="v0.0.5" IMAGE_TAG_BASE="quay.io/thoth-station/meteor-operator" make docker-build docker-push

The deployment of the CNBI operator into the cluster is designed following opendatahub's pattern.
Kustomize is used to structure the cnbi operator manifests, and the manifests are available
on the [cnbi operator repo](https://github.com/thoth-station/meteor-operator/tree/main/config).

New changes of the CNBi component are deployed to the cluster by updating the version in [the relevant kfdef](https://github.com/operate-first/apps/tree/master/kfdefs/overlays/osc/osc-cl1/cnbi)

ArgoCD syncs the kfdef in the cluster osc-cl1, the opendatahub operator deploys the CNBi components in the aicoe-meteor namespace.

## Local dev environment

Iterative development using the test/demo environment is not too practical.

Here are pointers on how to set up a local development environment for the CNBi feature.

- Require a local cluster.
    - Use Quicklab instance to setup a new cluster. [More detail](https://docs.google.com/document/d/13X5z2dRSe1Aaoj9Z722xul9_1_OWo5-FYyxyZ-y3oZA/edit#heading=h.yz7m8a57wxab)
    - Requires RedHat VPN to access these cluster.

- The `Makefile` helps ensure that the cluster meets the requirements: with a valid `KUBECONFIG` with a user with cluster-admin privileges, you can run `make prerequisites` to install the operators that cnbi depends upon (openshift-pipelines and cert-manager)

### OpenDataHub

As we wouldnt be updating the components of OpenDataHub.
we would use it for verification of CNBi.
- Install the OpenDatahub Operator from OperatorHub.
- Deploy the component required using kfdefs. [ex](https://github.com/operate-first/apps/blob/92c9d16c1d69e2a87075e2cf45f03dbf55324aa5/kfdefs/overlays/osc/osc-cl1/jupyterhub-stage/kfdef.yaml.
)

### CNBi Operator and pipelines

For a local dev environment of the CNBi operator, check the [README of the CNBi operator](https://github.com/goern/meteor-operator/blob/spike-cnbi-crd/README.md)

### CNBi UI: ODH-Dashboard

For Local dev of environment:

- Clone the forked of [ODH-Dashboard](https://github.com/thoth-station/odh-dashboard).

- Pre-requisite:
    - nodejs version: v14.x.x
    - python version: 2.7

- Login to cluster setup via `oc login` as the ODH-Dashboard looks for kfdefs and opendatahub components if available.

- In the cluster, assign the admin user to a group `odh-admins`, as CNBi
feature if available in administar view.

- Use commands:
    - To install node packages: `npm install --python=python2.7`
    - To start the build: `npm run build`
    - To run the dev: `npm run dev`

In Local cluster:

Once OpenDataHub operator is setup. <br>
Use kfdef to deploy the odh-dashboard, same as https://github.com/operate-first/apps/blob/92c9d16c1d69e2a87075e2cf45f03dbf55324aa5/kfdefs/overlays/osc/osc-cl1/jupyterhub-stage/kfdef.yaml.
