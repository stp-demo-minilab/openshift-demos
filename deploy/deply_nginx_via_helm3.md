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


# download helm charts for 8.8.4
helm pull bitnami/nginx --version 8.8.4 --untar=true

# install helm chart in current project for 8.8.4
# default service.type: LoadBalancer
# overide service.type as CluterIP
helm install nginx bitnami/nginx \
  --version 8.8.4 \
  --set service.type=ClusterIP
  
# check helm installation status
helm status nginx

# list helm releases
helm ls
helm ls --all

# REVISION from 1
NAME 	NAMESPACE      	REVISION	UPDATED                             	STATUS  	CHART      	APP VERSION
nginx	c5dpn-helm-demo	1       	2021-05-02 18:57:24.143383 +0800 CST	deployed	nginx-8.8.4	1.19.10

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



## Upgrade Nginx



```bash
# upgarde nginx from 8.8.4 to 8.8.5hel
helm upgrade nginx bitnami/nginx \
  --version 8.8.5 \
  --set service.type=ClusterIP
  

# check release
helm ls --all

# before upgrading
# revision is 1
NAME 	NAMESPACE      	REVISION	UPDATED                             	STATUS  	CHART      	APP VERSION
nginx	c5dpn-helm-demo	1       	2021-05-02 18:57:24.143383 +0800 CST	deployed	nginx-8.8.4	1.19.10

# after upgrading
# revision is 2
NAME 	NAMESPACE      	REVISION	UPDATED                             	STATUS  	CHART      	APP VERSION
nginx	c5dpn-helm-demo	2       	2021-05-02 18:59:39.142981 +0800 CST	deployed	nginx-8.8.5	1.19.10


```



## Rollbak Nginx (Optional)

```bash
# check releases
helm ls --all

# latest revision is 2
NAME 	NAMESPACE      	REVISION	UPDATED                             	STATUS  	CHART      	APP VERSION
nginx	c5dpn-helm-demo	2       	2021-05-02 18:59:39.142981 +0800 CST	deployed	nginx-8.8.5	1.19.10

# rollback as previous revision
# in this case previous revision is 1 (2-1)
helm rollback nginx 1

# check releases
helm ls --all

# nginx is rolled back to 8.8.4, but revision is only increased, so latest revision is 3 (2+1)
NAME 	NAMESPACE      	REVISION	UPDATED                            	STATUS  	CHART      	APP VERSION
nginx	c5dpn-helm-demo	3       	2021-05-02 19:04:22.21451 +0800 CST	deployed	nginx-8.8.4	1.19.10

```





## Uninstall Nginx (Optional)



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