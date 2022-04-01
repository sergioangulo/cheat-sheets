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



