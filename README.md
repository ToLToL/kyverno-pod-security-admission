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