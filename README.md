# mediawiki-test

Challenge-1
===========
#pull the mediawiki image from dockerhub
podman pull mediawiki
podman images

#login into the quay.io
podman login quay.io -u <USERNAME> -p <PASSWORD>

#taging 
podman tag docker.io/library/mediawiki:latest quay.io/tripatikumarpatra/mediawiki:1.0
#push the tagging image to quay.io 
podman push quay.io/tripatikumarpatra/mediawiki:1.0

#create a new namespace in openshift 
oc login
oc new-project mediawiki

#deploy mysqlDB
oc new-app --name=mysql --template=mysql-ephemeral -p MYSQL_USER=petclinic -p MYSQL_PASSWORD=petclinic -p MYSQL_DATABASE=petclinic -p MYSQL_ROOT_PASSWORD=petclinic
oc get dc
oc get po 

#create a secret for pulling the mediawiki image from quay.io registry
oc create secret docker-registry my-secret --docker-server=quay.io --docker-username=tripatikumarpatra --docker-password=<PASSWORD>

#link the secret to serviceaccount
oc secret link  default my-secret --for=pull

#Deploy the app
oc new-app --name=mediawiki --image=quay.io/tripatikumarpatra/mediawiki:1.0 --as-deployment-config
oc get svc
oc expose svc mediawiki
![image](https://user-images.githubusercontent.com/38211738/212481149-aa336f28-231a-40ee-8f76-49739ec22f34.png)

  
  
Challenge-2
===========
#Create a new namespace  
oc new-project project-test

#create resource quota for project-test 
oc create quota project-test-quota --hard=cpu=2,memory=3Gi
  
#Deploy theree different application
oc new-app --name=poda --image=nginx:1.20-ubi8 --as-deployment-config
oc new-app --name=podb --image=httpd:2.4-el8 --as-deployment-config
oc new-app --name=podc --image=php:7.4-ubi8 --as-deployment-config 

#Set resource to different application/DC
oc set resources dc poda --limits=memory=400Mi --requests=memory=400Mi
oc set resources dc podb --limits=memory=200Mi --requests=memory=200Mi
oc set resources dc podc --limits=memory=400Mi --requests=memory=400Mi

Challenge-3
============
Create a template yaml file for a test application, which is having all customized code for app name, Deplyoment name, service name, route name, hostname etc.
file name: nginx-template.yaml

while running the yaml file, we need to pass the argument like below.
oc process -f nginx-template.yaml -p NAME=<name> -p APPLICATION_DOMAIN=<domain-name> |oc create -f - 
 
