
# Defend for Containers (D4C)

Elastic Defend for Containers provides cloud-native runtime protections for containerized environments by identifying and/or blocking unexpected system behavior in Kubernetes environments.  

As a general principle, cloud-native containers are ‘[immutable](https://kubernetes.io/docs/concepts/containers/)’, meaning that changes to the container filesystem are unexpected during the course of normal operations. Leveraging this principle allows application and security teams the ability to detect unusual system behavior with a high degree of accuracy— without relying on more techniques like memory scanning or attack signatures which can consume more system resources.

When this integration is used alongside containers built with this philosopy, security teams can enjoy
* **Restricted lateral movement**: LSM blocking does not rely on system call interpolation. This means that this integration is not subject to scaling limitations in multiprocessor systems nor [TOCTOU](https://en.wikipedia.org/wiki/Time-of-check_to_time-of-use) race conditions. 
*  **Reduced attack surface**: Leaner containers give attackers less room to maneuver and hide. Filessytem blocking makes containers protected by D4C hostile to attackers. 
* **Easily identify unauthorized activities**: When a properly configured system alerts, the policy protecting the system either needs to be updated, or unauthorized behavior has been identified. 
* **Enforce cloud-native security posture**: Enforcing the principle of immutability isn’t something that can be easily achieved without enforcing read-only filesystems, which can be too restrictive for many customers.  D4C allows teams the ability to enforce immutability centrally, but allow for enough flexbility to enable productivity. 

## Features

### Drift Prevention
The Drift Prevention feature of D4C is enabled via YAML drift prevention policies. These policies specify which containers, system operations and portions of the container filesystem that a specified action(s) should be taken on. 

The system is controlled via a powerful and flexible policy engine which allows users to specify system and Kubernetes attributes as `selectors` (specific `activities` and portions of the system that a policy is applied to) and `responses` (actions the user would like to take when a selector is matched with attempted system operations). 

## Getting Started
For step-by-step instructions on how to set up this integration, see http://this-does-not-exist.elastic.co/d4c/help/guide/getting-started.html 

### Deployment
The system is deployed using the Elastic Agent, as a daemonset on each node in a Kubernetes cluster.

Policies that are unique to a given environment are crafted and applied to a given agent policy. This agent policy can be unique to a nodes in a specific Kubernetes cluster, or can apply to nodes spanning multiple Kubernetes clusters.

Policies can be applied to subsets of containers deployed on a given node using policy selectors.  

### Policies
A policy governs allowable system behaviors and response across a number of nodes within an Elastic agent policy. 

A given policy must contain at least one `selector` element and one `response` element.

#### Selectors
A selector tells the system what system operations to take action on and has a number of parameters that can grouped together (using a logical AND operation) to provide precise control. 

```
  - name: exampleSelector #a name for this grouping of attributes
    activity: [createExecutable] # which activity this selector 
    containerImageName: [nginx]
    containerImageTag: [latest]
    filePath: [/usr/bin]
    orchestratorClusterId: [cluster1]
    orchestratorClusterName: [kgCluster]
    orchestratorNamespace: [default]
    orchestratorResourceLabel: [‘production:*’]
    orchestratorResourceName: [‘nginx-pod-*’]
    orchestratorType: [kubernetes]
```

**activity [required]:** A list of system operations that can trigger a system action when paired with a `response`. Only  `createExecutable` and `modifyExecutable`  activities are supported. Wildcards are not supported. 

**containerImageName [optional]:** A list of of a container image names to match on. Substrings of container image names are supported using wildcards (for example `containerImageName: elastic-a*` will match on `elastic-agent` as well as `elastic-agent-complete`)

**containerImageTag [optional]:** A list of container image tags to match on. Wildcards are allowed. 

**filePath [optional]:** A list of file paths to include.  Paths are absolute and wildcards are supported. 

Consider the following policy example:
```
 - name:
    activity: [createExecutable]
    filePath: [/usr/bin/echo, /usr/sbin*, /usr/local/**]
```

In this example,
-  `/usr/bin/echo` will match on the `echo` binary, and only this binary
-  `/usr/local/**` will match on everything recursively under `/usr/local` including `/usr/local/bin/something`
-  `/usr/bin/*` includes everything that’s a direct child of /usr/bin 

**orchestratorClusterId [optional]:** TBD

**orchestratorClusterName [optional]:** TBD

**orchestratorNamespace [optional]:** A list of cluster namespaces to match on. Wildcards are supported. 

**orchestratorResourceLabel [optional]:** A list of resource labels. Wildcards are supported on label values, but not on label keys.

For example, the following policy will match attempst to create executables on any portion of a filesystem, in any containers as long as those containers are annotated with the `evironment` key,  and have the  `owner:drohan` label fully defined: 

```
 - name:
    activity: [createExecutable]
    orchestratorResourceLabel: [environment:*, owner:drohan ]
```

orchestratorResourceName [optional]: A list of resource names that the selector will match on. TBD. 

orchestratorType [optional]: A list defining which orchestrator engine type the policy and activity should match on.  `kubernetes` is the only supported orchestratorType at this time. 

#### Responses
Responses instruct the system on what `actions` to take when system operations match `selectors`. 

Supported actions today include `alert` and `block`.

`alert` actions will send alerts to the _____ index via the Elastic Agent shipper service. 

`block` actions will _always_ create an alert, but will also prevent the system operation from proceeding. This blocking action happens *prior* to the execution of the event. 

## Data streams

The Cloud Defend for Containers integration collects two type{s} of data streams: logs and metrics.

Log data streams collected by the {name} integration include {sample data stream(s)} and more. See more details in the [Logs](#logs-reference). -->
Metric data streams collected by the {name} integration include {sample data stream(s)} and more. See more details in the [Metrics](#metrics-reference). -->

## Requirements

You need Elasticsearch for storing and searching your data and Kibana for visualizing and managing it.
You can use our hosted Elasticsearch Service on Elastic Cloud, which is recommended, or self-manage the Elastic Stack on your own hardware.

TBD. 


<!--
	Optional: Other requirements including:
	* System compatibility
	* Supported versions of third-party products
	* Permissions needed
	* Anything else that could block a user from successfully using the integration
-->

#### Exported fields

{insert table}