---
status: accepted
date: 2022-11-02
---
# Specify pipeline selection in the operator code

## Context and Problem Statement

CNBi resources each have a `buildType`.
The process to produce a working notebook image is different depending on that `buildType`.
We need some logic to select the correct process ; where should that logic lives ?
Besides, different build process needs different inputs ; we also have to determine when we need which inputs.

## Decision Drivers

* Fastest done
* Does not impede progress

## Considered Options

* Query the CNBi resource from the pipeline to get the `buildType`
* Select a pipeline per `buildType` during the reconcile loop inside the operator code
* Set the `buildType` as a pipeline parameter + all possible build parameters

## Decision Outcome

Chosen option: "Select a pipeline per `buildType` during the reconcile loop inside the operator code", because it works good enough as far as we can see and is not blocking.

## Pros and Cons of the Options

### Query the CNBi resource from the pipeline to get the `buildType`

* Good, because potentially allow to add `buildType` without modifying the operator code
* Neutral, we trade go complexity against pipelines complexity
* Bad, because previous point implies more generality which may be unneeded
* Bad, because requires back and forth between the pipeline and the Openshift API

### Select a pipeline per `buildType` during the reconcile loop inside the operator code

* Good, because the conditions are closer to the reconciliation logic

## More Information

We might want to revisit the decision if we find out that doing it that way slow us down.
