# mediawiki-test

challenge-1
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
