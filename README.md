# microservices-talend


```
https://runnable.com/docker/java/dockerize-your-java-application

Dockerfile
-------------------

FROM anapsix/alpine-java
MAINTAINER Milan Das
COPY RestMicroServices_0.1.jar /home/RestMicroServices_0.1.jar
EXPOSE 8040
#CMD ["java","-jar","/home/RestMicroServices_0.1.jar"]
ENTRYPOINT ["java","-jar","/home/RestMicroServices_0.1.jar"]





docker build -t myds1 .
docker run -d -p 8040:8040 myds1
docker login
docker tag myds1 dmilan/talend:talendmicroservicedem
docker run -d -p 8040:8040 dmilan/talend:talendmicroservicedemo


docker-compose.yml
--------------------

version: "3"
services:
  web:
    image: localhost:5000/talendmicroservicedemo
    deploy:
      replicas: 3
      resources:
        limits:
          cpus: "0.1"
          memory: 250M
      restart_policy:
        condition: on-failure
    ports:
      - "8040:8040"
    networks:
      - webnet
networks:
  webnet:

----------
docker swarm init --advertise-addr 172.17.0.1

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-37rgqj570krg2fuvleqpzafrt3rssi46hzzcujj3nnca7p3nss-ctbndp5hhf369ktbcul8ezp3i 172.17.0.1:2377


docker stack deploy -c docker-compose.yml myms1swarmcluster

docker stack ps myms1swarmcluster


scale the app
-------

docker stack deploy -c docker-compose.yml myms1swarmcluster



--- setup a registry
mkdir auth
touch auth/htpasswd
chmod 666 auth/htpasswd


user=dasmilan
docker run --rm -it   -v `pwd`/auth:/auth   --entrypoint htpasswd registry:2 -B /auth/htpasswd $user
chmod 444 auth/htpasswd



docker run -d -p 5000:5000 --restart=always --name registry \
  -v `pwd`/auth/htpasswd:/auth/htpasswd:ro \
  -v `pwd`/registry:/var/lib/registry \
  -e "REGISTRY_AUTH=htpasswd" \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Local Registry" \
  -e "REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd" \
  -e "REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY=/var/lib/registry" \
  registry:2


  docker login localhost:5000

docker tag myds1 localhost:5000/talendmicroservicedemo



https://github.com/kubernetes/minikube


minikube start --vm-driver=xhyve
kubectl config use-context minikube
kubectl cluster-info

dmilan@dmilan-ThinkPad-E570:/opt/dockerimage/dockerms1$ docker build -t dockerms1:v1 .
kubectl run dockerms1 --image=dockerms1:v1 --port=8040
kubectl get deployments
kubectl expose deployment dockerms1 --type=LoadBalancer
kubectl get services

kubectl delete service dockerms1
kubectl delete deployment dockerms1

minikube dashboard

```
