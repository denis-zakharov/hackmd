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