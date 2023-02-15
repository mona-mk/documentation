# Kubectl cheat-sheet

#### get all pods:
`kubectl get pods --all-namespaces`

---
#### get pods for a specific namespace:
`kubectl get pods -n batch-jobs`

---
#### get all nodes:
`kubectl get nodes`

#### to describe a component (configuration behind a resource):
`kubectl describe <resoucetype> <resourcename> -n <namespaces>`

---
#### get up-to-date deployment configuration with state info from etcd attached:
`kubectl get deployment <deployment-name> -o yaml`

---
#### get current cluster:
`kubectl config current-context`

---
#### switch cluster (dev/staging/prod:
`kubectl config use-context arn:aws:eks:eu-central-1:XXXXXXXXXXXX:cluster/cluster_name`

---
#### list all namespaces:
`kubectl get namespaces`

---
#### list all cronjobs in namespace batch-jobs:
`kubectl get cronjobs -n batch-jobs`

---
#### test a cronjob as a test job in namespace batch-jobs:
`kubectl create job --from=cronjob/<cronjob-name> -n batch-jobs <job-name>`

---
#### find pods associated with a job:
`Kubectl get pods --selector=job-name=<jobname> --output=jsonpath={.items..metadata.name} -n <namespace>`

---
#### find pods associated with a node in all namespaces:
`kubectl get pods --all-namespaces -o wide --field-selector spec.nodeName=<nodeName>`

---
#### get deployment yaml (service, pod, secret etc):
`kubectl get deploy deploymentname -o yaml`

---
#### get ingress objects:
`kubectl get ingress --all-namespaces`

---
#### suspend cronjob:
`kubectl patch cronjobs <cronjob-name> -n <namespace> -p '{"spec" : {"suspend" : true }}'`

---
#### get running logs of one of the containers in a pod:
`kubectl logs -f <podname> -n <namespace> -c <containername>`

---
#### get shell to a running container in pod:
`kubectl exec -it <podname> -c <containername> -n <namespace> -- /bin/bash`

---
#### check existing secret/s:
`kubectl get secret -n <namespace>`:
`kubectl get secret <secret-name> -n <namespace> -o yaml`

---
#### decode secret:
`kubectl get secret --namespace <namespace> <secret-name> -o jsonpath="{.data.<secret-key>}" | base64 --decode`

---
#### create a SealedSecret locally:
```
kubectl -n <namespace> create secret generic <secret-name> \
--from-literal=<secret-key>=<secret-value> -o json --dry-run=client | \
kubeseal --controller-name=sealed-secrets --controller-namespace=kube-system \
-o yaml > <yaml-file>:
```

---
#### Add new secrets to the local file:
```
kubectl -n <namespace> create secret generic <secret-name> \
--from-literal=<secret-key>=<secret-value> -o json --dry-run=client | \
kubeseal --controller-name=sealed-secrets --controller-namespace=kube-system \
-o yaml --merge-into <yaml-file>:
```

---
#### Upload secret to kubernetes cluster:
`kubectl create -f <yaml-file>`

---
#### delete secret:
`kubectl delete secret <secret-name>`