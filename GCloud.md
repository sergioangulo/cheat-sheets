# GCloud

## Nombre cuenta activa
```   bash
gcloud auth list
```

## get project id
```   bash
gcloud config list project
```

## create instance with cloud shell
```   bash
gcloud compute instances create gcelab2 --machine-type n1-standard-2 --zone us-central1-f
```

## show all default values for created instances
```   bash
gcloud compute instances create --help
```

## SSH access to instances
```   bash
gcloud compute ssh gcelab2 --zone us-central1-f
```


## Cloud Functions
Create "Cloud Storage" for Cloud Function:

```   bash
gsutil mb -p [PROJECT_ID] gs://[BUCKET_NAME]
```

### Deploy Cloud function
```   bash
gcloud functions deploy helloWorld \
  --stage-bucket [BUCKET_NAME] \
  --trigger-topic hello_world \
  --runtime nodejs8
```

Where helloWorld is defined in index.js

### Verify state function
```   bash
gcloud functions describe helloWorld
```

### Call example
```   bash
DATA=$(printf 'Hello World!'|base64) && gcloud functions call helloWorld --data '{"data":"'$DATA'"}'
```

### See logs
```   bash
gcloud functions logs read helloWorld
```

## GKE Google Kubernetes Engine
Configure  default cimoute zone

```   bash
gcloud config set compute/zone us-central1-a
```

Create Cluster KGE
```   bash
gcloud container clusters create [CLUSTER-NAME]
```


Get cluster credntials to authenticate
```   bash
gcloud container clusters get-credentials [CLUSTER-NAME]
```

Create a new object deployment 

```   bash
kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0
```

Create an Service Object to expose your app 
```   bash
kubectl expose deployment hello-server --type=LoadBalancer --port 8080
```

Inspect your service to get external-ip
```   bash
kubectl get service
```

Then you can call your app 
```   bash
wget http://[EXTERNAL-IP]:8080
```

Now you can delete your cluster
```   bash
gcloud container clusters delete [CLUSTER-NAME]
```

## Load Balancers
## Configure zone and region
```   bash
gcloud config set compute/zone us-central1-a
gcloud config set compute/region us-central1
```
## Create 3 virtual machines with same tag and apache
For X in [1,2,3], we could have www1,www2,www3. All with tag network-lb-tag
```   bash
gcloud compute instances create wwwX \
  --image-family debian-9 \
  --image-project debian-cloud \
  --zone us-central1-a \
  --tags network-lb-tag \
  --metadata startup-script="#! /bin/bash
    sudo apt-get update
    sudo apt-get install apache2 -y
    sudo service apache2 restart
    echo '<!doctype html><html><body><h1>wwwX</h1></body></html>' | tee /var/www/html/index.html"
```

Create a firewall rule to accept traffic (using the last tag)

```   bash
gcloud compute firewall-rules create www-firewall-network-lb \
    --target-tags network-lb-tag --allow tcp:80
```
Get external IPS from instances list

```   bash
gcloud compute instances list
```

Call your machines:
```   bash
curl http://[IP_ADDRESS]
```
### Configure service load balance

Create an external ip for your load balancer
```   bash
gcloud compute addresses create network-lb-ip-1 \
 --region us-central1
```

Add an HTTP health check resource
```   bash
gcloud compute http-health-checks create basic-check
```

Add a target group (*www-pool*) in the same region(us-central1) and use health-check resource (basic-check)
```   bash
gcloud compute target-pools create www-pool \
    --region us-central1 --http-health-check basic-check
```

Then , add instances (www1,www2,www3) to group (www-pool)

```   bash
Agregue las instancias al grupo:

gcloud compute target-pools add-instances www-pool \
    --instances www1,www2,www3
```

Create a forwarding rule
```   bash
gcloud compute forwarding-rules create www-rule \
    --region us-central1 \
    --ports 80 \
    --address network-lb-ip-1 \
    --target-pool www-pool
```

### Send traffic
Get external IP for our forwarding rule *www-rule*
```   bash
gcloud compute forwarding-rules describe www-rule --region us-central1
```

Send requests to the last external ip (IP_ADDRESS) to check the balancer
```   bash
while true; do curl -m1 IP_ADDRESS; done
```

## HTTP load balancer
Is implemented in GFE (google front-end)

Create a load balancer template:
```   bash
gcloud compute instance-templates create lb-backend-template \
   --region=us-central1 \
   --network=default \
   --subnet=default \
   --tags=allow-health-check \
   --image-family=debian-9 \
   --image-project=debian-cloud \
   --metadata=startup-script='#! /bin/bash
     apt-get update
     apt-get install apache2 -y
     a2ensite default-ssl
     a2enmod ssl
     vm_hostname="$(curl -H "Metadata-Flavor:Google" \
     http://169.254.169.254/computeMetadata/v1/instance/name)"
     echo "Page served from: $vm_hostname" | \
     tee /var/www/html/index.html
     systemctl restart apache2'
```
Create an instsnce group based on template
```   bash
gcloud compute instance-groups managed create lb-backend-group \
   --template=lb-backend-template --size=2 --zone=us-central1-a
```

Create a forwarding rule healt-checked that allow using google cloud health-check 
```   bash
gcloud compute firewall-rules create fw-allow-health-check \
    --network=default \
    --action=allow \
    --direction=ingress \
    --source-ranges=130.211.0.0/22,35.191.0.0/16 \
    --target-tags=allow-health-check \
    --rules=tcp:80
```

Configure an external, static and global IP for your clients to access load balancer
```   bash
gcloud compute addresses create lb-ipv4-1 \
    --ip-version=IPV4 \
    --global
```

Get IPV4 load balancer IP
```   bash
gcloud compute addresses describe lb-ipv4-1 \
    --format="get(address)" \
    --global
```

Add an HTTP health check resource

```   bash
    gcloud compute health-checks create http http-basic-check \
        --port 80
```

Create a backend service
```   bash
 gcloud compute backend-services create web-backend-service \
        --protocol=HTTP \
        --port-name=http \
        --health-checks=http-basic-check \
        --global
```
Add instance group like a backend of bakend service: 

```   bash
 gcloud compute backend-services add-backend web-backend-service \
        --instance-group=lb-backend-group \
        --instance-group-zone=us-central1-a \
        --global
```

Create an URL map to send input requests to default backend service:
```   bash
gcloud compute url-maps create web-map-http \
        --default-service web-backend-service
```

Create an http proxy target to route request to the map
```   bash
    gcloud compute target-http-proxies create http-lb-proxy \
        --url-map web-map-http
```

Create a global forwarding rule to resend input requests to proxy:
```   bash
    gcloud compute forwarding-rules create http-content-rule \
        --address=lb-ipv4-1\
        --global \
        --target-http-proxy=http-lb-proxy \
        --ports=80
```

## Cloud Storage 

Upload a file to a bucket:
```   bash
gsutil cp example_file.jpg gs://YOUR-BUCKET-NAME
```

Obtener objeto desde deposito
```   bash
gsutil cp -r gs://YOUR-BUCKET-NAME/example_file.jpg .
```

cp objects inside bucket
```   bash
gsutil cp gs://YOUR-BUCKET-NAME/example_file.jpg gs://YOUR-BUCKET-NAME/image-folder/
```

list content of a bucket
```   bash
gsutil ls gs://YOUR-BUCKET-NAME
```
change access to file to makeit public
```   bash
gsutil acl ch -u AllUsers:R gs://YOUR-BUCKET-NAME/example_file.jpg
```

change public access to private

```   bash
gsutil acl ch -d AllUsers gs://YOUR-BUCKET-NAME/ada.jpg
```

delete object
```   bash
gsutil rm gs://YOUR-BUCKET-NAME/ada.jpg
```
