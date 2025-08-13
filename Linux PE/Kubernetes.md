[Kubernetes](https://kubernetes.io/), also known as `K8s`, stands out as a revolutionary technology that has had a significant impact on the software development landscape

Developed by Google, Kubernetes leverages over a decade of experience in running complex workloads. As a result, it has become a critical tool in the DevOps universe for microservices orchestration. Since its creation, Kubernetes has been donated to the [Cloud Native Computing Foundation](https://www.cncf.io/), where it has become the industry's gold standard.

The K8s architecture comprises a `master node` and `worker nodes`, each with specific roles.

---

## K8s Concept

K8s continues to grow and improve with features like `Role-Based Access Control` (`RBAC`), `Network Policies`, and `Security Contexts`, providing a safer environment for applications.

Differences between K8 and Docker

|**Function**|**Docker**|**Kubernetes**|
|---|---|---|
|`Primary`|Platform for containerizing Apps|An orchestration tool for managing containers|
|`Scaling`|Manual scaling with Docker swarm|Automatic scaling|
|`Networking`|Single network|Complex network with policies|
|`Storage`|Volumes|Wide range of storage options|

Kubernetes architecture is primarily divided into two types of components:

- `The Control Plane` (master node), which is responsible for controlling the Kubernetes cluster
    
- `The Worker Nodes` (minions), where the containerized applications are run

#### Nodes

The master node hosts the Kubernetes `Control Plane`, which manages and coordinates all activities within the cluster and it also ensures that the cluster's desired state is maintained. On the other hand, the `Minions` execute the actual applications and they receive instructions from the Control Plane and ensure the desired state is achieved.

#### Control Plane

The Control Plane serves as the management layer. It consists of several crucial components, including:

|**Service**|**TCP Ports**|
|---|---|
|`etcd`|`2379`, `2380`|
|`API server`|`6443`|
|`Scheduler`|`10251`|
|`Controller Manager`|`10252`|
|`Kubelet API`|`10250`|
|`Read-Only Kubelet API`|`10255`|
#### Minions

Within a containerized environment, the `Minions` (worker nodes) serve as the designated location for running applications. It's important to note that each node is managed and regulated by the Control Plane, which helps ensure that all processes running within the containers operate smoothly and efficiently.

The `Scheduler`, based on the `API server`, understands the state of the cluster and schedules new pods on the nodes accordingly. After deciding which node a pod should run on, the API server updates the `etcd`.

 The API server is the entry point for all the administrative commands, either from users via kubectl or from the controllers. This server communicates with etcd to fetch or update the cluster state.

#### K8's Security Measures

Kubernetes security can be divided into several domains:

- Cluster infrastructure security
- Cluster configuration security
- Application security
- Data security

---
## Kubernetes API

The core of Kubernetes architecture is its API, which serves as the main point of contact for all internal and external interactions. The Kubernetes API has been designed to support declarative control, allowing users to define their desired state for the system.

Within the Kubernetes framework, an API resource serves as an endpoint that houses a specific collection of API objects. These objects pertain to a particular category and include essential elements such as Pods, Services, and Deployments, among others. Each unique resource comes equipped with a distinct set of operations that can be executed, including but not limited to:

|**Request**|**Description**|
|---|---|
|`GET`|Retrieves information about a resource or a list of resources.|
|`POST`|Creates a new resource.|
|`PUT`|Updates an existing resource.|
|`PATCH`|Applies partial updates to a resource.|
|`DELETE`|Removes a resource.|
#### Authentication

In terms of authentication, Kubernetes supports various methods such as client certificates, bearer tokens, an authenticating proxy, or HTTP basic auth, which serve to verify the user's identity. Once the user has been authenticated, Kubernetes enforces authorization decisions using Role-Based Access Control (`RBAC`). This technique involves assigning specific roles to users or processes with corresponding permissions to access and operate on resources. Therefore, Kubernetes' authentication and authorization process is a comprehensive security measure that ensures only authorized users can access resources and perform operations.
In Kubernetes, the `Kubelet` can be configured to permit `anonymous access`. By default, the Kubelet allows anonymous access. Anonymous requests are considered unauthenticated, which implies that any request made to the Kubelet without a valid client certificate will be treated as anonymous. This can be problematic as any process or user that can reach the Kubelet API can make requests and receive responses, potentially exposing sensitive information or leading to unauthorized actions.

#### K8's API Server Interaction

```shell
curl https://10.129.10.11:6443 -k

{
	"kind": "Status",
	"apiVersion": "v1",
	"metadata": {},
	"status": "Failure",
	"message": "forbidden: User \"system:anonymous\" cannot get path \"/\"",
	"reason": "Forbidden",
	"details": {},
	"code": 403
}
```

`System:anonymous` typically represents an unauthenticated user, meaning we haven't provided valid credentials or are trying to access the API server anonymously. In this case, we try to access the root path, which would grant significant control over the Kubernetes cluster if successful.

#### Kubelet API - Extracting Pods

```shell
curl https://10.129.10.11:10250/pods -k | jq .
```

#### Kubeletctl - Extracting Pods

```shell
kubeletctl -i --server 10.129.10.11 pods
```


