# ansible-ee-gitops

First create secrets manually for your private registries (pull and push images) and for the Tekton Trigger webhook.

## Private Registry Secret

For example, for your private registries. In this case we am pushing to `quay.io` and also pulling from `registry.redhat.io`.
```yaml
apiVersion: v1
data:
  .dockerconfigjson: XXXXXX
kind: Secret
metadata:
  annotations:
    tekton.dev/docker-0: quay.io
    tekton.dev/docker-1: registry.redhat.io
  name: pull-and-push
type: kubernetes.io/dockerconfigjson
```

Then you need to link the secret `pull-and-push` to the `pipeline` SA so it can be used for pulling and pushing images.
```bash
oc secret link pipeline pull-and-push --for=pull,mount
```

## Install Tekton Hub ansible-builder task

Go to [Tekton Hub](https://hub.tekton.dev/tekton/task/ansible-builder) and install the `ansible-builder` task.

```bash
kubectl -n <MY_NAMESPACE> apply -f https://raw.githubusercontent.com/tektoncd/catalog/main/task/ansible-builder/0.1/ansible-builder.yaml
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