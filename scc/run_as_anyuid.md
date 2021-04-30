[TOC]

# Run as anyuid



## Demo

```bash
# create project
oc new-project $GUID-http --display-name="$GUID HTTP Server"

# create http server application
oc new-app --docker-image=httpd:2.4.17

# watch pod status
# failed to start pod: CrashLoopBackOff
watch oc get pods 

# check pod logs
oc logs httpd-6bd664d96-gv4bf

# (13)Permission denied: AH00072: make_sock: could not bind to address [::]:80
# (13)Permission denied: AH00072: make_sock: could not bind to address 0.0.0.0:80

# add `anyuid` scc permissions to `default` service account of `$GUID-http` project
oc adm policy add-scc-to-user anyuid -z default -n $GUID-http

# clusterrole.rbac.authorization.k8s.io/system:openshift:scc:anyuid added: "default"

# delete previous failed pod
oc delete pod httpd-6bd664d96-gv4bf

# watch new pod status
# start pod successfully
watch oc get pods

# clean up project (optional)
oc delete project $GUID-http

```



Alternatively, you have to run application on non-priviliged ports (>= 1024).



## References

- https://blog.csdn.net/weixin_43902588/article/details/107304082