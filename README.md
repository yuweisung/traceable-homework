# Traceable Homework

## Setup k8s env (docker-desktop)
Turn on k8s in docker desktop like this.
![image](images/docker-k8s.png)  

You should be able to run kubecl command.
```
alias k = kubectl
k cluster-info
Kubernetes control plane is running at https://127.0.0.1:6443
CoreDNS is running at https://127.0.0.1:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```
## Deploy crAPI
Follow the setup.md in crAPI repo to install the crAPI in docker k8s.
```
git clone git@github.com:OWASP/crAPI.git
cd crAPI/deploy/helm
k create ns crapi
helm install --namespace crapi crapi . --values values.yaml --set apiGatewayServiceInstall=false
NAME: crapi
LAST DEPLOYED: Mon Jun 30 12:46:23 2025
NAMESPACE: crapi
STATUS: deployed
REVISION: 1
TEST SUITE: None
```
Check the status of crAPI.
```
k get pod, svc -n crapi
NAME                                   READY   STATUS    RESTARTS   AGE
pod/crapi-community-5589f65994-klkg2   1/1     Running   0          98s
pod/crapi-identity-f4ffb86ff-r8vvm     1/1     Running   0          98s
pod/crapi-web-6895f64cfd-ctvrv         1/1     Running   0          98s
pod/crapi-workshop-668666d596-m94dm    1/1     Running   0          98s
pod/gateway-service-596556fbc9-zkkr5   1/1     Running   0          98s
pod/mailhog-648b95cc86-sdxz5           1/1     Running   0          98s
pod/mongodb-0                          1/1     Running   0          98s
pod/postgresdb-0                       1/1     Running   0          98s

NAME                          TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
service/crapi-community       ClusterIP      10.97.11.102    <none>        8087/TCP                     98s
service/crapi-identity        ClusterIP      10.96.85.247    <none>        8080/TCP                     98s
service/crapi-web             LoadBalancer   10.100.41.150   localhost     80:30080/TCP,443:30443/TCP   98s
service/crapi-workshop        ClusterIP      10.102.143.57   <none>        8000/TCP                     98s
service/gateway-service       ClusterIP      10.98.169.34    <none>        443/TCP                      98s
service/mailhog               ClusterIP      10.107.2.60     <none>        1025/TCP                     98s
service/mailhog-web           ClusterIP      10.106.7.172    <none>        8025/TCP                     98s
service/mailhog-web-ingress   LoadBalancer   10.97.75.36     localhost     8025:30025/TCP               98s
service/mongodb               ClusterIP      10.105.228.83   <none>        27017/TCP                    98s
service/postgresdb            ClusterIP      10.97.241.79    <none>        5432/TCP                     98s
```
## Postman desktop
From postman.com, download and install postman on your desktop.

## Install traceable platform agent (helm)
1. Create an agent token on traceable and set env.
![image](images/traceable-token.png)
```
export TOKEN=xxxxxxxxxx
export ENV=YUWEI_SUNG
```
2. Deploy traceable platform agent
```
helm repo add traceableai https://helm.traceable.ai
helm repo update
helm install --namespace traceableai traceable-agent traceableai/traceable-agent --create-namespace --set token=$TOKEN --set environment=$ENV
```
Check the status of deployment.
```
k get pod -n traceableai
NAME                               READY   STATUS    RESTARTS   AGE
traceable-agent-5765d45fc6-dz9sj   1/1     Running   0          13m
```

## Install ebpf tracer agent (helm)
```
helm repo add traceableai https://helm.traceable.ai
helm repo update
helm upgrade --namespace traceableai traceable-agent traceableai/traceable-agent --set token=$TOKEN --set environment=YUWEI_SUNG --set runAsDaemonSet=false --set daemonSetMirroringEnabled=true --set ebpfCaptureEnabled=true --set ebpfRunAsPrivileged=true --set ebpfDeployOnMaster=true
```
Veryif the agent pods are running.
```
kubectl get pods -n traceableai
NAME                               READY   STATUS    RESTARTS   AGE
traceable-agent-764496c58c-s62gq   1/1     Running   0          56s
traceable-ebpf-tracer-ds-m6cnt     1/1     Running   0          56s
```

## Learning crAPI using Postman
1. Load the attached postman json and env json files to Postman.
![image](images/postman-crapi.png)
2. Change the api endpoint port to 80.
![image](images/postman-env.png)
3. Run 200 iteration with 100ms delay

## Check the Traceable UI


## Run 