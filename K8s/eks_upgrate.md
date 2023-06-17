# EKS Updade Note

## 版本資訊
- 1.22 -> 1.27
- 升到 1.24，container runtime 會從 docker://20.10.17 變成 containerd://1.6.19
ref: https://docs.aws.amazon.com/eks/latest/userguide/kubernetes-versions.html

## 升級步驟與 SOP
- Update master node
```shell
aws eks update-cluster-version --name $cluster --kubernetes-version $target_version | jq '.update.id'
aws eks describe-update --update-id $updateid --name $cluster
```
- Update worker node 
```shell
aws eks update-nodegroup-version --cluster-name $cluster --nodegroup-name $nodegroup --kubernetes-version $target_version | jq '.update.id'
aws eks describe-update --update-id $updateid --name $cluster --nodegroup-name $nodegroup
```
- Update node group with launch template
```shell=
# Manually update AMI in launch template by searching amazon-eks-node-1.2x-v2023
aws eks update-nodegroup-version --cluster-name $cluster --nodegroup-name $nodegroup --launch-template '{ "name": $lt_name, "version": "2"}'  
```
- Update addons
```shell
aws eks describe-addon-versions --addon-name $addon
aws eks update-addon --cluster-name $cluster --addon-name $addon --addon-version $version --resolve-conflicts OVERWRITE
aws eks describe-update --update-id $updateid --name $cluster --addon-name $addon
```
- 升到 1.25 之後要先更新 kube-proxy
`v1.25.9-eksbuild.1`
- 升到 1.27 之後再將其他 addon 都更新
```shell=
# CoreDNS
aws eks update-addon --cluster-name $cluster --addon-name coredns --addon-version v1.10.1-eksbuild.1 --resolve-conflicts OVERWRITE
# Amazon VPC CNI
aws eks update-addon --cluster-name $cluster --addon-name vpc-cni --addon-version v1.12.6-eksbuild.2 --resolve-conflicts OVERWRITE
# kube-proxy
aws eks update-addon --cluster-name $cluster --addon-name kube-proxy --addon-version v1.27.1-eksbuild.1 --resolve-conflicts OVERWRITE
# Amazon EBS CSI Driver
aws eks update-addon --cluster-name $cluster --addon-name aws-ebs-csi-driver --addon-version v1.19.0-eksbuild.2 --resolve-conflicts OVERWRITE
```
- 檢查所有服務是否正常

## Other notes
- Create oidc by eksctl
```shell=
oidc_id=$(aws eks describe-cluster --name my-cluster --query "cluster.identity.oidc.issuer" --output text | cut -d '/' -f 5)
aws iam list-open-id-connect-providers | grep $oidc_id | cut -d "/" -f4
eksctl utils associate-iam-oidc-provider --cluster my-cluster --approve
```
https://docs.aws.amazon.com/eks/latest/userguide/enable-iam-roles-for-service-accounts.html
- Create oidc by aws-cli
```shell=
EKS_CLUSTER_NAME=YOUR-EKS-NAME
APISERVER=$(aws eks describe-cluster --name $EKS_CLUSTER_NAME | jq -r .cluster.endpoint)
EKS_API_FQDN=$(echo $APISERVER | sed 's/https:\/\///g')
EKS_ID=$(echo $EKS_API_FQDN | awk -F'.' '{print $1}')
thumbprint=$(
  echo | openssl s_client -servername $EKS_API_FQDN -showcerts -connect $EKS_API_FQDN:443 2> /dev/null \
  | sed -n -e '/BEGIN/h' -e '/BEGIN/,/END/H' -e '$x' -e '$p' | tail +2 \
  | openssl x509 -fingerprint -noout \
  | sed -e "s/.*=//" -e "s/://g" \
  | tr "ABCDEF" "abcdef"
)

AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query "Account" --output text)
OIDC_PROVIDER_URL=$(aws eks describe-cluster --name ${EKS_CLUSTER_NAME} --query "cluster.identity.oidc.issuer" --output text)
OIDC_PROVIDER=$(echo ${OIDC_PROVIDER_URL} | sed -e "s/^https:\/\///")
aws iam create-open-id-connect-provider --url $OIDC_PROVIDER_URL --client-id-list $CLIENT_ID --thumbprint-list $thumbprint
```
- Update cluster by eksctl
```shell=
eksctl upgrade cluster --name $cluster --approve
```
- Update nodegroup by eksctl
```shell=
eksctl upgrade nodegroup --name=$nodegroup --cluster=$cluster --region=ap-northeast-1 --kubernetes-version=1.24       
```
- Update addon by eksctl
```shell=
# get addon version
eksctl utils describe-addon-versions --name kube-proxy --kubernetes-version 1.27
# update addon version 
## CoreDNS
eksctl update addon --name coredns --version v1.10.1-eksbuild.1 --cluster $cluster
## Amazon VPC CNI
eksctl update addon --name vpc-cni --version v1.12.6-eksbuild.2 --cluster $cluster
## kube-proxy
eksctl update addon --name kube-proxy --version v1.27.1-eksbuild.1 --cluster $cluster
## Amazon EBS CSI Driver
eksctl update addon --name aws-ebs-csi-driver --version v1.19.0-eksbuild.2 --cluster $cluster
```






