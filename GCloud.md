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
