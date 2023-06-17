# Gcloud Cheatsheet

ref:https://kubernetes.io/docs/reference/kubectl/cheatsheet/

## List all resource in project/organization
```shell
gcloud asset search-all-resources --scope=projects/gogan-infra-prod --order-by=assetType --read-mask=display_name,name,assetType > gogan-infra-prod
```

## Set and get custom metadata
- Set custom metadata
```shell
gcloud compute instances add-metadata tenmax-jk-dw-spark-07rj --metadata=foo=bar
```

- Remove custom metadata
```shell
gcloud compute instances remove-metadata tenmax-jk-dw-spark-07rj --keys=foo
```

- Curl custom metadata(within target vm)
```shell
curl "http://metadata.google.internal/computeMetadata/v1/instance/attributes/foo" -H "Metadata-Flavor: Google"
```

- Lookup for metadata tags
```shell
curl "http://metadata.google.internal/computeMetadata/v1/instance/" -H "Metadata-Flavor: Google"
```


## Common commnad

- Set default project
```shell
gcloud config set project `project_id` 
```
- To get instances list
```shell
gcloud compute instances list 
```
- To create multiple instances
```shell
gcloud compute instances create vm-{1..4}
```
- To delete multiple instances
```shell
gcloud compute instances delete --quiet vm{1..4}
``` 
- To create disks
```shell
gcloud compute disks create disk-1 --size 200 --type pd-standard --zone asia-east1-a
```
> size: GB
> tpye: pd-standard or pd-ssd

- Attach disks to instances 
```shell 
gcloud compute instances attach-disks vm-0 --disk disk-1 
```
- Set up default project ID, zone and region
```shell 
gcloud config set project [project name]
gcloud config set compute/zone [zone]
gcloud config set compute/region [region]
```
- Show images list
```shell
gcloud compute images list
``` 
    
## Resize PD
ref:https://medium.com/google-cloud/resize-your-persist-disk-on-google-cloud-on-the-fly-b3491277b718
```shell
sudo growpart /dev/sda 1
sudo resize2fs /dev/sda1
```

## Acquire a static ip
1. Switch project
```shell
gcloud config set project `target_project_id` ex: academic-being-718
```
2. Switch to the cluster 
```shell
gcloud container clusters get-credentials `cluster_name` --zone asia-east1-b ex: pixhelp2
```
3. Set namespace
```shell
kubectl config set-context --current --namespace
```

4. Acquire a static ip
```shell
gcloud compute addresses create ADDRESS_NAME --global --ip-version [IPV4 | IPV6]
``

## GCS gsutil
```shell
gsutil ls -l `gs_bucket` 
gstuil cp `gs_bucket/folder/file dest_path`
```

## gcloud IAM 
ref: https://cloud.google.com/iam/docs/creating-managing-service-accounts
### List service account 
```shell
gcloud iam service-accounts list
```

### Create service account
```shell
gcloud iam service-accounts create SERVICE_ACCOUNT_ID \
    --description="DESCRIPTION" \
    --display-name="DISPLAY_NAME"
```

### List IAM role

### Grant IAM role to service account 
```shell
gcloud projects add-iam-policy-binding PROJECT_ID --member="serviceAccount:SERVICE_ACCOUNT_ID@PROJECT_ID.iam.gserviceaccount.com" --role="ROLE_NAME"
```

## Mount ssd
ref: https://cloud.google.com/compute/docs/disks/local-ssd

## VPC peering 
1. setup firewall rull, allow eath vpc network
2. setup vpc peering for project A
```shell
gcloud compute networks peerings create peer-pixnet-gt --network=network-research-lab --peer-project=pixnet-gt --peer-network=network-gt --export-custom-routes
```
3. setup vpc peering for project B
```shell
gcloud compute networks peerings create peer-pixnet-research-lab --network=network-gt --peer-project=pixnet-research-lab --peer-network=network-research-lab --export-custom-routes
```

## Create images from instance
```shell
gcloud beta compute machine-images create web-adm-1-php7-0 --project=pixnet-wallet --source-instance=web-adm-1 --source-instance-zone=asia-east1-c --storage-location=asia
```
