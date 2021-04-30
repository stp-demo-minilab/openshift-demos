[TOC]

# Deploy Nginx via Helm3



## Demo

```bash
# create project
oc new-project $GUID-helm-demo --display-name="$GUID Helm Demo"

# search nginx in helm hub
# https://artifacthub.io/packages/helm/bitnami/nginx
helm search hub nginx

# add helm repo
helm repo add bitnami https://charts.bitnami.com/bitnami

# list local added helm repo
helm repo list

# search nginx in helm repo
helm search repo nginx

NAME                            	CHART VERSION	APP VERSION	DESCRIPTION
bitnami/nginx                   	8.8.5        	1.19.10    	Chart for the nginx server

# nginx version is 1.19.10
# https://hub.docker.com/r/bitnami/nginx/tags/?page=1&ordering=last_updated&name=1.19.10


# download helm charts
helm pull bitnami/nginx --version 8.8.5 --untar=true

# install helm chart in current project
# default service.type: LoadBalancer
helm install nginx bitnami/nginx \
  --version 8.8.5 \
  --set service.type=ClusterIP
  
# list helm releases
helm ls

# watch pod status
watch oc get pods

# check service nginx
# Port:              http  80/TCP
# TargetPort:        http/TCP
# Endpoints:         10.129.2.195:8080
oc describe svc nginx

# expose service to route
oc expose svc nginx

# get route (nginx url)
oc get route

# access the nginx url in browser

```



## Clean Up Resources



```bash
# unintall
helm uninstall nginx

# check helm release
helm ls

# check resources
oc get all -o name

# delete route
oc delete route nginx

```







## References

Helm Hub

https://artifacthub.io/



Helm Official Site:

https://helm.sh/



References

- https://blog.csdn.net/weixin_43902588/article/details/104585541



SCC:

- https://kubernetes.io/docs/tasks/configure-pod-container/security-context/#set-the-security-context-for-a-container