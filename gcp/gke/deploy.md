# Deploying Google Kubernetes Engine 

### Prerequisites

* Install the Google Cloud sdk commandline from https://cloud.google.com/sdk/


### Storage

* Create a persistant volume for etcd storage: 

      gcloud beta compute disks create workbench-etcd --project=metabolomics-workbench-dev --type=pd-standard --size=10GB --zone=us-west1-a --physical-block-size=4096


### Ingress

* Reserve a Static IP address:

      gcloud compute addresses create workbench --project=metabolomics-workbench-dev --region=us-west1


### Kubernetes

* Create a Kubernetes Cluster:

      gcloud beta container --project "metabolomics-workbench-dev" clusters create "workbench" --zone "us-west1-a" --no-enable-basic-auth --cluster-version "1.12.8-gke.6" --machine-type "n1-standard-2" --image-type "COS" --disk-type "pd-standard" --disk-size "100" --metadata disable-legacy-endpoints=true --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" --num-nodes "2" --enable-cloud-logging --enable-cloud-monitoring --enable-ip-alias --network "projects/metabolomics-workbench-dev/global/networks/default" --subnetwork "projects/metabolomics-workbench-dev/regions/us-west1/subnetworks/default" --default-max-pods-per-node "110" --addons HorizontalPodAutoscaling --enable-autoupgrade --enable-autorepair
