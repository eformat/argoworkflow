## argo workflows & pipelines

Argo Workflows is a Kubernetes-native workflow engine for complex job orchestration, including serial and parallel execution.

https://argoproj.github.io/argo/

```
-- client binary
asroot wget https://github.com/argoproj/argo/releases/download/v2.6.0/argo-linux-amd64 -O /usr/local/bin/argo
asroot chmod 755 /usr/local/bin/argo

-- deploy to openshift
oc new-project argowk --display-name="ArgoWorkflow" --description="ArgoWorkflow"
oc apply -n argowk -f https://raw.githubusercontent.com/argoproj/argo/v2.6.0/manifests/install.yaml

-- need to create service accounts with cluster level permissions
oc adm policy add-cluster-role-to-user admin system:serviceaccount:$(oc project -q):argo-server
oc adm policy add-cluster-role-to-user admin system:serviceaccount:$(oc project -q):argo

oc port-forward svc/argo-server -n argowk 2746:2746 &

-- user interface
http://127.0.0.1:2746

-- https://github.com/argoproj/argo/blob/master/docs/workflow-executors.md
-- workflow executors
oc edit cm workflow-controller-configmap

apiVersion: v1
kind: ConfigMap
metadata:
  name: workflow-controller-configmap
data:
  config: |
    containerRuntimeExecutor: k8sapi

-- example
argo submit --watch https://raw.githubusercontent.com/argoproj/argo/master/examples/hello-world.yaml
argo submit --watch https://raw.githubusercontent.com/argoproj/argo/master/examples/coinflip.yaml
argo submit --watch https://raw.githubusercontent.com/argoproj/argo/master/examples/loops-maps.yaml

-- commands
argo list
argo get xxx-workflow-name-xxx
argo logs xxx-pod-name-xxx #from get command above
```