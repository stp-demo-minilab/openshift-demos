[TOC]

# Speed Up Maven Build



## Demo



### Set up Nexus

```bash
# create project
oc new-project $GUID-nexus --display-name="$GUID Nexus"

# deploy nexus 3 without persistent for test only
# import openshift template
oc create -f https://raw.githubusercontent.com/monodot/openshift-nexus/master/nexus3-template.yaml

# deploy nexus3
oc new-app nexus3

# watch pod status
watch oc get pods

# check service
oc get svc

# get route (nexus url)
oc get route

# login in nexus
# default password: admin / admin123

# open `maven-public` repository in Nexus
# should have no arifacts at this moment

```



### Deploy an application with Maven build



```bash
# create project
oc new-project "$GUID-petclinic" --display-name "Spring PetClinic"

# deploy application
oc new-app openjdk-11-rhel7:1.1~https://github.com/stp-demo-minilab/spring-petclinic.git \
  --name=spring-petclinic

# watch pod status
watch oc get pods

# check build logs in a new terminal
oc logs -f spring-petclinic-1-build

# download depencencies from maven central repository via public internet

# [INFO] Downloading from central: https://repo1.maven.org/maven2/org/springframework/boot/spring-boot-starter-parent/2.3.3.RELEASE/spring-boot-starter-parent-2.3.3.RELEASE.pom


# check build pod age, need about 6 min

NAME                                READY   STATUS      RESTARTS   AGE
spring-petclinic-1-build            0/1     Completed   0          6m18s

# use internal nexus as maven mirror url
oc set env bc spring-petclinic MAVEN_MIRROR_URL="http://nexus.$GUID-nexus:8081/repository/maven-public/"

# start build
oc start-build spring-petclinic

# check build logs
oc logs -f $(oc get pods | grep build | awk '/Running/ {print $1}')

# download depencencies from internal nexus in openshift cluster

# [INFO] Downloading from mirror.default: http://nexus.c5dpn-nexus:8081/repository/maven-public/org/springframework/boot/spring-boot-starter-parent/2.3.3.RELEASE/spring-boot-starter-parent-2.3.3.RELEASE.pom

# build pod still need about 6 min to complete because at the first time internal maven repo does not have dependencies

# build again
oc start-build spring-petclinic

# check build logs again
oc logs -f $(oc get pods | grep build | awk '/Running/ {print $1}')

# download depencencies from internal nexus in openshift cluster is very fast at this time
# only need about 80s to complete build
# even though we don't use local maven repo

```



## Clean up Resources (optional)

```bash
oc delete project $GUID-nexus
oc delete project $GUID-petclinic
```





## References

- https://blog.csdn.net/weixin_43902588/article/details/103486278