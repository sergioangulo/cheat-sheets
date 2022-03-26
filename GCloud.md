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
