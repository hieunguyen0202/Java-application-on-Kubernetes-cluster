### 273. Introduction

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/9446c973-c81c-45d8-93c6-89bbe7b283e1)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/a420c105-cbf4-40df-a65f-835af2dbf52e)

- First, we have a multitier your web application stack which is already containerized.
- And we are going to use the vprofile web application which we containerized in our previous projects.
- So your web application is containerized and you have also tested it.
- Now it's time to host it for production.

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/ec6b6afc-ee48-4fd7-87db-07a636a2afb5)

- The requirement could be you need `high availability` so your containers don't go down and also high availability on your Compute node requirement is also for `fault tolerance`.
- If something happens to the containers and they are not responding, they should also `auto heal`.
- It should be also `convenient to scale containers` and also the `compute resource` on which your containers are running `platform independent` and also portable, flexible, agile.
- So we should be able to run our containers on local cloud, virtual machines, physical machines and should be able to run on dev QA production.

  
- Kubernetes is today `the best container orchestration tool` in the market. It's very mature and a rock solid platform to run your containers for production.

  
- Let's also check some statistics in today's time. Kubernetes is one of the most used tools in the containerized platform and these statistics are here to prove them. 77% of containers today run on Kubernetes. If we include Redshift and Rancher which are already built on Kubernetes, then it's 89%. 56% of the organizations have reported that they're going to increase the usage of Kubernetes in next twelve months and that should be by end of 2020. From 2019% to 2028%, growth in usage of Kubernetes Cluster for production containers. This is a huge number.

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/2403f672-c123-4619-ad41-4488a3fa8316)

- So we can understand kubernetes has taken the Container Orchestration or Container World by a strome. So on this project we are going to use our Java vprofile application which we have Containerized and we're going to run it on Kubernetes Cluster for production.

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/52cbc2b2-2cc8-4fe0-a306-b2fbe6080f99)

- Let's see how we are going to do it. So we need a `Kubernetes cluster already`. And I'm going to `use Cops to launch my Kubernetes cluster`. You can check the project of Kubernetes setup where I've explained how you can `use kops to set up production grade Kubernetes cluster`. We also need `Containerized application`. We are going to use a vprofile. We have already done a project on containerizing our vprofile application. So these are `two main requirements`.
- We need a Kubernetes cluster and we need Containerized application. And we are using a vprofile application. So you know, in vprofile application we have a MySQL container that needs volume to store its data so we can have persistent data. So for that, we are going to `create an EBS volume to run our DB Pod`. Then we're going to label node with zone names. We're going to create EBS volume in a zone and we need our pod to be running on the same node or on the same zone where we created the EBS volume.
- So for this, we're going to `label our nodes with zone names`. So when we run our DB pod, we'll select node based on the zones.

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/fac6cfde-0efc-4760-95fa-f65d40d60dab)

- Once we are ready with all the steps, then we are going to write Kubernetes definition files to create our objects on kubernetes cluster, we're going to use deployment service secret and volume.

### 274. Spin K8s Cluster
- I am on my AWS Management console. I have an EC2 instance named kops. I have done all the prerequisites for kops. So now just with two commands I'm going to launch the Kubernetes cluster.
- Now I recommend you to pause the video and do all the prerequisites for kops and launch the Kubernetes cluster. You can check out Kubernetes setup project and do exactly same setup as we did in that project for kops.
  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/90ce2800-16e8-4d60-a384-2bb7950487b3)

- I'm going to login to this node now to the kops with this command
  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/def86272-d5cc-46cc-9d98-ee6ad5fb89d7)

- I'm going to create my Kubernetes cluster. kops create cluster --name, name of my cluster, --state, the bucket name, --zones, I'm mentioning two zones, node-count=2, node-size=t3.small, master-size=t3.medium, dns-zone, this and this should be same.

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/f6ee471a-9c91-40ac-9d48-da25b8326ca2)

 - Now with kops update cluster command, I'm going to launch my Kubernetes cluster

   ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/265d41b6-391a-48c9-a5d2-4938cc868d4a)

- So I'm going to create a new repository here named as `Kubernetes`.
  
   ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/6dc9b268-0589-42d5-8769-72e1fb8a2a0d)

- I'm going to clone this repository on my IntelliJ IDEA.
  
   ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/72287e56-b77a-42bf-96fd-55d72dde4444)

   ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/ecb04c5b-cb94-45f3-9f88-0201240e7374)

- Install plugins Kubernetes

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/89d95ca9-aa44-433a-83e6-db479ec92ec3)

-  Let's check the health of our cluster. Kops validate cluster.

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/6380f7f6-91bb-4718-8f95-7270375b550b)

- We have two nodes and one master node.
  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/cc319e76-0ac9-4d44-acb1-c2d0dca3d44f)

### 275. Volume Prerqs for DB POD
- Now, before we start writing definition files for our pods, we have to create a volume for our DB pod so it can store the mysql data, which gets stored in wire lib mysql into EBS volume.
- So let's create an EBS volume. AWS ec2 create volume Availability Zone. I have selected us-east-2a, size just three GB.

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/c27b5e40-4cd6-4461-ac4b-ac4788c7239d)

- Now, we have to make sure when we run our DB pod, it should be running on a node which is in the same zone, same Availability Zone. And we can make that through node selector option in our definition file. Node selector works with labels. So we are going to create
our own label.

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/41b96998-dead-41b6-b1d3-ba5090f8fdf4)

- So we should see the available labels. They are existing labels. If you want, we can use them, but we will create our own label.
  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/f2c4788e-025c-4adb-a6c1-f2aa0e756f9b)

- Okay, so I've given two labels. We are done with our setup and the prerequisite that was required to run our project. Now we'll start writing the definition files.
  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/065ea044-7c20-4f18-ade5-49ae50b01c73)

- For GCP, run this command to create the persisent disk `gcloud compute disks create my-disk --size=100GB --region=us-central1`

### 276. Source Code Overview
- If you feel that you want to see all the definition file before you make a decision, then search for a branch `kube-app`. In our repository we have a directory here kubernete in this we have a vpro-app and here we have all the [definition files](https://github.com/devopshydclub/vprofile-project/tree/kube-app/kubernetes/vpro-app).
  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/6009839e-69b7-44fd-86d5-fb308e7d05c2)

- You can put it in your own repository like I'm going to put it in a different repository that I have already cloned on my intelligence. It's a fresh there's no data in it for now.

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/dc004144-213a-4d79-a941-685d531c20ab)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/05c61009-d2f5-4c9e-a140-d502e0e5ccd5)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/7e71ecfb-8a2b-443d-87b4-f67320fd8324)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/853cf0cc-fd52-4a31-9430-e26234509bb8)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/ca8ca5ab-ed10-4239-a0f2-d9db17681274)

- Some important informations: [First](https://github.com/devopshydclub/vprofile-project/blob/docker/Docker-files/app/Dockerfile) and [Second](https://github.com/devopshydclub/vprofile-project/blob/docker/src/main/resources/application.properties)

  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/482e6b38-dd28-4332-a2e4-d65df5a1d3c8)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/807371b7-1d69-4d26-abd4-45cf98d92012)

- So when container comes up from this image, it will have this application properties file. It will look for the backend services with this name with vprodb, username root, password vprodbpass and that for memcache and that one for RabbitMQ. And RabbitMQ.
- You also have the username guest guest and the password guest. Also note down the port numbers.
- These are very important information to collect before we create our definition files because we need to define these information so our application can access the back end services.
- There are two sensitive information over here. One is the database password and next is the RabbitMQ password. And it won't be ideal to put these passwords into text format.
- So first we have to encrypt this password with secret and then from our pod definition we can access this encrypted passports. Not really encrypted, but encoded.
### 277. Kube secret for passwords
- First let's encode our password for `web vprodbpass the password`and `RabbitMQ password` is guest.

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/c44b36eb-c0d9-4a5c-8550-d57ed916b974)

- Let's write our first definition file, which will be of creating a secret.

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/b95edd85-ffd0-40a6-ac25-e2343e28d34f)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/9bdc154e-79a5-43e6-9fe4-d8b2b4277cd4)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/da432aee-a3c4-48b0-991a-22977f595f35)

- Let's push it to our version control. It goes to a remote repository. We're going to fetch it in our cops via Git clone. We see that in our remote repository. I'm going to copy this and clone it.
  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/4dbf3867-a3db-4f80-85a6-13ff8826a5f7)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/f8e479a9-5a7c-4761-8f06-9d1e88d95c9c)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/eeb76b7b-d0c8-4bfa-9ea0-86013deeb3d0)

- `kubectl create -f secret` created `kubectl get secret` `kubectl describe secret` to see the detailed information. And you should see now we really don't see the value as long as we don't decode them. It just says it's `five byte` of size and this is of `ten bytes` of size. So this is safely stored in our Kubernetes cluster. And this really prevents the accidental exposure of your passwords. But if your definition file is still public or accessible by many people, they can really see that it's encoded with base 64 and they can easily decoded with base 64.
#### Source code
- Write the config file `app-secret.yml`
```yml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
type: Opaque
data:
  db-pass: dnByb2RicGFzcw== 
  rmq-pass: Z3Vlc3Q=

```
### 278. DB Deployment Definition
- Now we'll write our `DB definition file`. Rememmber to use image from dockerhub repository.

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/db0c1213-10ff-4b5e-bf45-2f661f97933c)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/e51f6301-419d-4be4-957a-f9a43097ca9b)

- And we created our ebsVolume in this zone, and for this node, which is in this, in this zone, we have given a label, so our pod will run only on the node which is in the same zone, which has this label.

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/a02dbf00-2715-4678-8e26-2770dbcf8c4a)

- And remember the `volume ID` that we noted in previous lession

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/5b0b2e2d-63cd-466e-9ece-83fb07cc453f)

- We are in the same region, see that's the volume, you can match it with the volumeID, yes that's the volume
  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/c653df90-d7ae-4459-b0d4-cf05ae79cf1f)

- We have to give one tag over here, so then only a pod will be able to attach to this volume where node is going to attach the volume, otherwise you'll get permission denied errors, so the tag is KubernetesCluster, and your cluster name, so my cluster name is, vprokube.groophy.in, okay, so whatever name you have given to your KubernetesCluster, you have to give the same name over here.

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/fcef7669-74dd-4d79-9a9e-937225c535de)
  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/452fb79d-9736-4df5-9a50-968658668e60)

- Commit `Ctrl + K` and push `Ctrl + Shift + K`. And go back to git bash to action
  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/e5da489f-71d6-4009-affe-ed5cc498d2b7)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/f73b4c43-0647-4212-903c-6715386c85b6)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/ba744b3a-3620-4156-9a26-e435ee53ec91)


#### Source code
- Write the config file `vprodbdep.yml`
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: vprodb
  labels:
    app: vprodb
spec:
  selector:
    matchLabels:
      app: vprodb
  replicas: 1
  template:
    metadata:
      labels:
        app: vprodb
    spec:
      containers:
      - name: vprodb
        image: vprofile/vprofiledb
          #args:
          #- "--ignore-db-dir"
          #- "lost+found"
        volumeMounts:
        - mountPath: /var/lib/mysql
          name: vpro-db-data
        ports:
        - name: vprodb-port
          containerPort: 3306
        env:
         - name: MYSQL_ROOT_PASSWORD
           valueFrom:
             secretKeyRef:
               name: app-secret
               key: db-pass
      nodeSelector:
        zone: us-east-1a
      volumes:
        - name: vpro-db-data
          # This AWS EBS volume must already exist.
          awsElasticBlockStore:
             volumeID: MentiondVoumeID
             fsType: ext4

      initContainers:
        - name: busybox
          image: busybox:latest
          args: ["rm", "-rf", "/var/lib/mysql/lost+found"]
          volumeMounts:
            - name: vpro-db-data
              mountPath: /var/lib/mysql

```

- If you use GCP google cloud platform `vprodbdep.yml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: vprodb
  labels:
    app: vprodb
spec:
  selector:
    matchLabels:
      app: vprodb
  replicas: 1
  template:
    metadata:
      labels:
        app: vprodb
    spec:
      containers:
      - name: vprodb
        image: vprofile/vprofiledb
        # args:
        # - "--ignore-db-dir"
        # - "lost+found"
        volumeMounts:
        - mountPath: /var/lib/mysql
          name: vpro-db-data
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: app-secret
              key: db-pass
        ports:
        - name: vprodb-port
          containerPort: 3306
      nodeSelector:
        zone: us-central1-a
      volumes:
      - name: vpro-db-data
        gcePersistentDisk:
          pdName: my-disk
          fsType: ext4

      initContainers:
      - name: busybox
        image: busybox:latest
        args: ["rm", "-rf", "/var/lib/mysql/lost+found"]
        volumeMounts:
        - name: vpro-db-data
          mountPath: /var/lib/mysql


```
### 279. DB Service Definition
- Okay, we have to create a service for this DB so our application pod can access it. So I'm going to say DB-CIP cluster IP. We are not going to expose it to the outside world. It's going to only for the internal traffic API version v1 for the Kind service.

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/9ba00823-4d59-4307-b617-ee14acd6fb6b)

#### Source code
- Write the config file `db-CIP.yml`
  
```yml
apiVersion: v1
kind: Service
metadata:
  name: vprodb
spec:
  ports:
  - port: 3306
    targetPort: vprodb-port
    protocol: TCP
  selector:
    app: vprodb
  type: ClusterIP
```
### 280. Memcached Deployment & Service

- Let's continue with our next Pod definition file or deployment definition file, which is for memcache.
  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/b13a65ff-e0e1-4c9f-a176-5982df2bec70)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/e2d9ab10-45c3-4f5f-9c8a-881875482969)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/cc5a993a-9337-416d-bfba-b66d2902ff88)

#### Source code
- Write config file `mcdep.yml` and `mc-CIP.yml`
```yml
# mcdep.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: vpromc
  labels:
    app: vpromc
spec:
  selector:
    matchLabels:
      app: vpromc
  replicas: 1
  template:
    metadata:
      labels:
        app: vpromc
    spec:
      containers:
      - name: vpromc
        image: memcached
        ports:
        - name: vpromc-port
          containerPort: 11211
```

```yml
# mc-CIP.yml
apiVersion: v1
kind: Service
metadata:
  name: vprocache01
spec:
  ports:
  - port: 11211
    targetPort: vpromc-port
    protocol: TCP
  selector:
    app: vpromc 
  type: ClusterIP
```
### 281. RabbitMQ Deployment & Service
- Next RabbitMQ
  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/47092fe3-d9b5-4ef6-ba41-588bb7fead7a)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/8810f685-fbee-4262-9230-26f00e5afc98)

#### Source code
- Write config file `rmq-dep.yml` and `rmq-CIP-service.yml`
```yml
# rmq-dep.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: vpromq01
  labels:
    app: vpromq01
spec:
  selector:
    matchLabels:
      app: vpromq01
  replicas: 1
  template:
    metadata:
      labels:
        app: vpromq01
    spec:
      containers:
      - name: vpromq01
        image: rabbitmq
        ports:
        - name: vpromq01-port
          containerPort: 15672
        env: 
         - name: RABBITMQ_DEFAULT_PASS
           valueFrom:
             secretKeyRef:
               name: app-secret
               key: rmq-pass
         - name: RABBITMQ_DEFAULT_USER
           value: "guest"

```

```yml
# rmq-CIP-service.yml
apiVersion: v1
kind: Service
metadata:
  name: vpromq01
spec:
  ports:
  - port: 15672
    targetPort: vpromq01-port
    protocol: TCP
  selector:
    app: vpromq01
  type: ClusterIP
```

### 282. Tomcat Deployment, Service & Init containers
- Now our main hero tomcat application. So file vproappdep.yaml.
  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/018f4a78-d1f3-4174-aa45-90c89702c3ab)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/30460fec-eb14-4ae1-b763-d5419d6db7bb)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/1afc1aec-b09c-46aa-8504-d9a486c7a4ba)

#### Source code
- Write config file `vproappdep.yml` and `vproapp-service.yml`
```yml
# vproappdep.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: vproapp
  labels: 
    app: vproapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: vproapp
  template:
    metadata:
      labels:
        app: vproapp
    spec:
      containers:
      - name: vproapp
        image: vprofile/vprofileapp
        ports:
        - name: vproapp-port
          containerPort: 8080
      initContainers:
      - name: init-mydb
        image: busybox
        command: ['sh', '-c', 'until nslookup vprodb.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for mydb; sleep 2; done;']
      - name: init-memcache
        image: busybox
        command: ['sh', '-c', 'until nslookup vprocache01.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for mydb; sleep 2; done;']

```

```yml
# vproapp-service.yml
apiVersion: v1
kind: Service
metadata:
  name: vproapp-service
spec:
  ports:
  - port: 80
    targetPort: vproapp-port
    protocol: TCP
  selector:
    app: vproapp
  type: LoadBalancer
```

### 283. Provision Stack on K8s Cluster
- Commit and push

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/455f1057-55d1-4db2-a55d-6791c62c5cd0)

- Let's check

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/78b86a2e-a32e-4568-baab-59a74ee41886)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/672fd216-4593-4e44-b807-ab3c69d40759)

  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/b4f20b64-c782-40a8-8d98-28a253885a6b)

- It takes some times to come up

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/70d7dc2d-a0f7-48e0-a272-ab6ce70b5d3e)

- Let's try login. If this works, then DB will be also verified. So we're also able to connect to the DB pod.
  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/dac58510-638a-4d1d-86ac-aeac0ab1f87e)

- RabbitMQ, mq initiated.
  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/395d1272-7fea-4dab-89d3-5615530a71de)

- All users for memcache.
  
  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/fd810078-0672-4f71-89b1-b160c118721b)

  ![image](https://github.com/hieunguyen0202/DevOps-Training/assets/98166568/762f1bca-2ef8-44e0-9829-6b5e30b7260a)

- All the services are verified. So from containerisation of our project. Now we are running our containerized application on production grade Kubernetes cluster.

### 284. URL for Website & Wrap up
- We have an endpoint and we can also put this into our GoDaddy. Or we can also use route 53, put the CNAME entry.

  



