---
status: accepted
date: 2022-11-04
---
# Deploy pipeline along with the operator

## Context and Problem Statement

The Custom Notebook Image (CNBi) feature of Open Data Hub (ODH) includes OpenShift Pipelines manifests that are used to implement the CNBi functionality.
The pipelines are not entirely dependent on the [CNBi Operator](https://github.com/thoth-station/meteor-operator).
However the CNBi Operator triggers the pipelines execution, so there is a dependency from CNBi Operator's end.

## Decision Drivers

* Consistency in deployment
* Avoid spreading resources in several locations

## Considered Options

* Deploy the OpenShift pipeline definition manifests using Helm charts, and maintain them on a different repository.
* Have the Operator deploy the pipeline manifests using the tarball of a separate pipeline repository.
* Include the pipeline manifests within the CNBi Operator repository, allow users to deploy it using kfdef.

## Decision Outcome

Chosen option: "Include the pipelines with the CNBi Operator, allow users to deploy it using kfdef", because it provides consistency on deployment.


### Positive Consequences

* Including the pipelines with the CNBi Operator simplify packaging and referencing.
* Users could use one reference to deploy both components.

### Negative Consequences

* This makes versioning of the pipelines dependent on the versioning of CNBi operator.

## Pros and Cons of the Options

### Deploy the OpenShift pipelines using helm charts

* Good, because it allows developers to work without having additional dependency.
* Bad, because the helm chart is yet to be accepted via OpenDatahub deployments.
* Bad, because it requires versioning of helm chart and keeping track of what getting deployed with respect to  the Operator version.

### Have the Operator deploy the pipeline using the tarball of the pipeline repository

* Good, because it provide easy packaging along with operator.
* Bad, because it would require Devops to continuously release the pipelines repository.

## More Information

We might want to revisit the decision if we find out that doing it that way slow us down.
