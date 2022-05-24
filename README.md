# kyverno-pod-security-admission

## Requirements

Read Kubernetes documentation about [Pod Security Admission](https://kubernetes.io/docs/concepts/security/pod-security-admission/)

### Before you begin
To use this mechanism, your cluster must enforce Pod Security admission.

#### Built-in Pod Security admission enforcement
In Kubernetes v1.24, the PodSecurity feature gate is a beta feature and is enabled by default. You must have this feature gate enabled. If you are running a different version of Kubernetes, consult the documentation for that release.

#### Alternative: installing the PodSecurity admission webhook
The PodSecurity admission logic is also available as a validating admission webhook. This implementation is also beta. For environments where the built-in PodSecurity admission plugin cannot be enabled, you can instead enable that logic via a validating admission webhook.

A pre-built container image, certificate generation scripts, and example manifests are available at https://git.k8s.io/pod-security-admission/webhook.

To install:

```
git clone https://github.com/kubernetes/pod-security-admission.git
cd pod-security-admission/webhook
make certs
kubectl apply -k .
```