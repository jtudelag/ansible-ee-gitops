# ansible-ee-gitops

First create secrets manually for your private registries (pull and push images) and for the Tekton Trigger webhook.

## Private Registry Secret
For example, for your private registries. In this case I am pushing to `quay.io` and also pulling from `registry.redhat.io`.
```yaml
apiVersion: v1
data:
  .dockerconfigjson: XXXXXX
kind: Secret
metadata:
  annotations:
    tekton.dev/docker-0: quay.io
    tekton.dev/docker-1: registry.redhat.io
  name: quay
type: kubernetes.io/dockerconfigjson
```

Then you need to patch `pipeline` SA with it.
```bash
oc edit sa pipeline

kind: ServiceAccount
metadata:
  name: pipeline
secrets:
- name: pipeline-token-n27h8
- name: pipeline-dockercfg-smhwn
- name: quay
```

## Webhook Secret Token
And for the webhook secret token:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: ansible-ee-trigger-secret
type: Generic
stringData:
  secretToken: "123"
```




