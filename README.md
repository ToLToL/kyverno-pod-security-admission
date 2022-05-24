# kyverno-pod-security-admission

## Requirements

1. Read Kubernetes documentation about [Pod Security Admission](https://kubernetes.io/docs/concepts/security/pod-security-admission/)

2. Launch a Kubernetes cluster, I chose GKE on Google Cloud (latest version: v1.23.5)

3. Since the PodSecurity feature gate is a beta feature and is enabled by default from v1.24 install the PodSecurity admission webhook

The PodSecurity admission logic is also available as a validating admission webhook. This implementation is also beta. For environments where the built-in PodSecurity admission plugin cannot be enabled, you can instead enable that logic via a validating admission webhook.

A pre-built container image, certificate generation scripts, and example manifests are available at https://git.k8s.io/pod-security-admission/webhook.

To install:

```
git clone https://github.com/kubernetes/pod-security-admission.git
cd pod-security-admission/webhook
make certs
kubectl apply -k .
```

## Comparison: Pod Security Admission vs Kyverno

### Enforce baseline PodSecurity

#### Pod Security Admission

1. `kubectl apply -f psa_enforce_baseline_test_namespace.yaml`
2. `kubectl get ns --show-labels`
```
NAME                    STATUS   AGE     LABELS
test                    Active   41s     kubernetes.io/metadata.name=test,pod-security.kubernetes.io/enforce-version=v1.24,pod-security.kubernetes.io/enforce=baseline
```

3. `kubectl apply -f nginx_privileged.yaml`: the pod is created because PSA only checks pod creation in `test` namespace
4. `kubectl apply -f nginx_privileged.yaml -n test`: error because it matches Kyverno statement
```
Error from server (Forbidden): error when creating "nginx_privileged.yaml": pods "nginx" is forbidden: violates PodSecurity "baseline:v1.24": privileged (container "nginx" must not set securityContext.privileged=true)
```
5. `kubectl delete ns test`

#### Kyverno

1. `kubectl apply -f kyverno_disallow_privileged.yaml`
2. `kubectl get cpol`
```
NAME                                         BACKGROUND   ACTION    READY
pod-security-admission-disallow-privileged   true         enforce   true
```
3. `kubectl apply -f nginx_privileged.yaml`: the pod is created because Kyverno policy only checks for pod in `test` namespace
4. `kubectl apply -f nginx_privileged.yaml -n test`: error because it matches Kyverno statement
```
Error from server: error when creating "nginx_privileged.yaml": admission webhook "validate.kyverno.svc-fail" denied the request:

resource Pod/default/nginx was blocked due to the following policies

pod-security-admission-disallow-privileged:
  pod-security-admission-disallow-privileged: 'validation error: You should not run
    your pod as privileged. Rule pod-security-admission-disallow-privileged failed
    at path /spec/containers/0/securityContext/privileged/'
```
5. `kubectl delete cpol --all`