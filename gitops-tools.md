# GitOps Tools Comparison

## Anthos Config Management
Anthos Config Management is especially suited to deploy configurations,
policies, and workloads **needed to run the platform that you build
on top of Anthos** — for example, security agents, monitoring agents,
and certificate managers.

Although you can deploy user-facing applications with Anthos Config
Management, we don't recommend linking their release lifecycle
to the release lifecycle of the administrative workloads mentioned earlier.
Instead, **we recommend that you use a tool dedicated to
application deployment**, such as a **continuous deployment** tool,
so that application teams can be in charge of their release schedule.

ACM Staged rollouts - cluster per cluster or namespace per namespace.


## GitOps Tools

[FluxCD vs ArgoCD](https://thenewstack.io/gitops-on-kubernetes-deciding-between-argo-cd-and-flux/)

### Similarities

- **Secrets**. Bitnami sealed-secrets controller with SealedSecret CRD,
  enctypt in Git using a public key provided by the controller.
  Vault, Helm Secrets, ArgoCD Vault plugin
- **Webhook Receivers** that act as triggers to sync.
- **Alerting and Notifications**
- **Image Updates Automation** new or by patterns.
- Both have a CLI to interact with APIs.

### Differences

**Install**
- Flux: Bootstrapping a management repository.
- Argo: Install YAML manifests and configure.

**Reconciliation**
- Flux: modular controllers built on the [GitOps Toolkit](https://fluxcd.io/docs/components/).
  Sync configuration for each component.
- Argo: sync reconciliation is a global setting.

**App Delivery**
- Flux: Kustomize, Helm (native support with hooks via golang lib).
- Argo: Kustomize, Helm (render and apply, hooks are mapped to **Argo Hooks**).
  Subphases with **Argo Sync Waves**.
  
**Web UI**
- Flux: only experimental.
- Argo: rich one, with enterprise ready SSO (OIDC, LDAP)
  and Argo access control policies.
  
**RBAC**
- Flux: strictly relies on Kubernetes.
- Argo: own ACL-based RBAC. Groups and users.
  Internal and external identity store (e.g. LDAP).
  
**Multicluster**
- Flux: KubeConfig CRD to connect to sattelite clusters.
- Argo: pull (Argo on each cluster pulling from the main one)
  and push models (one Argo to many clusters).
  
**Extensibility**
- Flux: writing a custom controller using GitOps Toolkit.
- Argo plugins: run tools to generate manifests, configured using
  a configmap or a side-car container.
  Inject build environment.
  
**Helm**

Flux:
- no plugin support https://fluxcd.io/docs/migration/helm-operator-migration/#no-longer-supports-helm-downloader-pluginshttpshelmshdocstopicspluginsdownloader-plugins
- native support via Helm SDK.
- helm controller CRDs
  * Source: HelmRepository, GitRepository (with subpaths), Bucket (s3).
  * HelmRelease: chart, sourceRef, values
    valuesFrom: ConfigMap, Secret - use with kustomize generators and nameReference
    to trigger an update.
    valuesFiles: inside the chart
  * dependsOn: other HelmRelease-s.
  * timeout
  * install, upgrade, uninstall flags and options. For example,
    upgrade.remediation.strategy: rollback
  * kustomize post-renderer

Argo:
- non-native support:
  * helm template | kubectl apply
  * argo hooks, release name is simulated
    via `app.kubernetes.io/instance: release` label injection.
- values files (--values)
- parameters (--set)
- plugins baked into a custom image or init container.
- more granular control over manifest application from a single source:
  hooks and sync waves.
