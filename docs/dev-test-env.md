
This document describes how to set up or access environments to develop and/or test the Custom Notebook Image (CNBi) functionality.

## Test/Demo environment

### Target cluster and context

The target cluster for CNBi CD is OS-Climate's Cluster1 (`odh-cl1`) cluster. The end goal is to use that environment to deploy *Custom Notebook Images* for the various data science-related repositories from the OS-Climate (OSC) project into the [OpenDataHub (ODH)](https://opendatahub.io) cluster in `odh-cl1`.

In general, the deployment of opendatahub is driven by the [odh-manifests](https://github.com/opendatahub-io/odh-manifests/) repo.

Operate First maintains a [fork of odh-manifests](https://github.com/operate-first/odh-manifests) where the manifests are customized in branches that correspond to the target clusters. The [osc-cl1-byon branch on that fork](https://github.com/operate-first/odh-manifests/tree/osc-cl1-byon) is used for ODH deployment to the OSC cluster1.

The ODH components to be deployed are specified in <https://github.com/operate-first/apps/tree/master/kfdefs/overlays/osc/osc-cl1>

The target namespace for the deployment is `opf-jupyterhub-stage`.

ArgoCD [deploys BYON's version of the odh-dashboard](https://github.com/operate-first/odh-manifests/blob/34b43f6b2e2fe1195b37709736a89d64c3bf4411/odh-dashboard/base/deployment.yaml#L30) in the opf-jupyterhub-stage namespace ([as configured](https://github.com/operate-first/apps/blob/92c9d16c1d69e2a87075e2cf45f03dbf55324aa5/kfdefs/overlays/osc/osc-cl1/jupyterhub-stage/kfdef.yaml) in the apps repo). That image is built from <https://github.com/opendatahub-io/odh-dashboard/tree/BYON> and available at `quay.io/repository/operate-first/odh-dashboard:byon`.

Note that, as of this writing, the stage namespace also has a manifest of the BYON validation/import pipeline (whose canonical source is in the [Thoth helm-charts repo](https://github.com/thoth-station/helm-charts/blob/08e78c0bd12cfe85cad65d5d1b6979ddb45ee1fb/charts/meteor-pipelines/templates/byon-import-jupyterhub-image.yaml)):

    $ tkn p ls
    NAME                           AGE            LAST RUN                                 STARTED        DURATION   STATUS
    byon-import-jupyterhub-image   15 weeks ago   byon-import-jupyterhub-image-run-r8jc6   14 weeks ago   1 minute   Succeeded

That pipeline definition, as deployed in that environment, (and the associated `byon-validate-jupyterhub-image` Task) comes from [ODH BYON development](https://github.com/operate-first/odh-manifests/tree/osc-cl1-byon/jupyterhub/jupyterhub/overlays/byon). That pipeline is **not** meant to be used for CNBi.

### CNBi pipeline manifests

TBD

### CNBi Operator deployment

The operator can be built and packaged into an image with:

    IMAGE_VERSION="v0.0.5"
    IMAGE="quay.io/thoth-station/cnbi-operator"
    make docker-build docker-push IMG="$IMAGE:$IMAGE_VERSION"

The deployment of the operator into the cluster is TBD - it would involve a new CNBi component in https://github.com/operate-first/apps/tree/master/kfdefs/overlays/osc/osc-cl1

## Local dev environment

Iterative development using the test/demo environment is not too practical.

Here are pointers on how to set up a local development environment for the CNBi feature.

### CNBi Operator and pipelines

For a local dev environment of the CNBi operator, check the [README of the CNBi operator](https://github.com/goern/meteor-operator/blob/spike-cnbi-crd/README.md)

### OpenDataHub

Integrating a local ODH deployment is TBD - for the time being, see test/demo env below.
