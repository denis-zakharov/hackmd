# Kubernetes API

## Versioning
- alpha (v1alpha1) -- stop support or break the compatibility at any time
- beta (v2beta3) -- well tested, however, semantics of objects may change
  in incompatible ways.
- stable (v1) -- GA version.


## Resources and Kinds
- Resources correspond to HTTP paths.
- Kinds are the types of objects returned by and received by theses
  endpoints, as well as persisted into etcd.
  
## GVR and GVK

Group-Version-Resource:
- `/api/v1` is a core group for historic reasons.
- `/apis/<group>/<version>/namespaces/<namespace>/<resource`.

Group-Version-Kind.

## List groups and resources
```
k api-versions
k api-resources
```

## Declarative State

`spec` is a desired world state.

`status` is an observed world state.

## Explore

Send requests with authentication.
```
k proxy --port=8080
k get --raw /apis/...
```

Examples.
```
k get --raw /apis/batch/v1 | jq
k get --raw /apis/batch/v1beta1
k get --raw /apis/helm.openshift.io/v1beta1
```

## Request processing

```
HTTP   __ auth& __ Mutating  __Object     __Validating __Persist
handler   authz    admission   schema       admission    to etcd
                   +webhooks   validation   +webhooks
```

## API Client Building Blocks

### go-client https://github.com/kubernetes/client-go
Implements HTTP interface of the API server with usual verbs like `Get()`, `Put()` and also `Watch()`.

While all packages have their use, most of your code that speaks to Kubernetes APIs will use `tools/clientcmd/`
to set up a client from a kubeconfig file and `kubernetes/` for the actual Kubernetes API clients.

### API types https://github.com/kubernetes/api

Resources types definitions. For example, core API objects are defined in
[core/v1/types.go](https://github.com/kubernetes/api/blob/master/core/v1/types.go)

### API Machinery

All generic building blocks to implement Kubernetes-like API. This is not restricted
to container management and can be used to build a generic business API.

An important package for k8s api clients is `k8s.io/apimachinery/pkg/apis/meta/v1`. It contains
many of the generic API types such as `ObjectMeta`, `TypeMeta`, `GetOptions`, and `ListOptions`.
