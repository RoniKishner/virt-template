# AGENTS.md - config

Kubebuilder-based Kustomize manifests: CRDs, RBAC, webhook configs, and three deployment overlays.

## Rules

- Never hand-edit generated manifests here (CRDs, RBAC, webhook configs) - they're produced by `make manifests` from `api/` type markers and `+kubebuilder:rbac` markers in the code. Change the source markers and regenerate instead
- `hack/lint.sh` runs `yamllint` against this directory - keep YAML lint-clean

## Cross-namespace authorization

`config/admission/` defines a `ValidatingAdmissionPolicy` with CEL checks for three permissions when creating a `VirtualMachineTemplateRequest`:

- `virtualmachinetemplaterequests/source` create in the source VM's namespace (skipped if same namespace)
- `datavolumes` create in the target namespace
- `virtualmachinetemplates` create in the target namespace

## Overlays

- `default` - Kubernetes with cert-manager (self-signed issuer)
- `openshift` - OpenShift with Service CA operator (different namespace: `openshift-cnv`, different DNS labels)
- `virt-operator` - certificates managed externally by virt-operator, ingress-only network policies

All overlays set namespace prefix `virt-template-` and deploy to their respective namespace.
