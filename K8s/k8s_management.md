# Kubernetes Management

## Tools
### kubectl
- install 
```shell
# Linux
# X86-64
 curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

# MAC
# AMR 64
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.27.1/2023-04-19/bin/darwin/amd64/kubectl
# Apply execute permissions to the binary.
chmod +x ./kubectl
# Copy the binary to a folder in your PATH. If you have already installed a version of kubectl, then we recommend creating a $HOME/bin/kubectl and ensuring that $HOME/bin comes first in your $PATH.
mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH
# (Optional) Add the $HOME/bin path to your shell initialization file so that it is configured when you open a shell.
echo 'export PATH=$HOME/bin:$PATH' >> ~/.bash_profile
# After you install kubectl, you can verify its version.
kubectl version --short --client
```
https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html
- Auto completion
```shell
Bash: echo "source <(kubectl completion zsh)" >> ~/.bashrc
Zsh: echo "source <(kubectl completion zsh)" >> ~/.zshrc
```
- kubectl list all resources in namespace
```shell
kubectl get all -n `namespace`
```
- kubectl list all objects in namespace
```shell
kubectl api-resources --verbs=list --namespaced -o name | xargs -n 1 kubectl get --show-kind --ignore-not-found -n `namespace`

kgetall() {
  namespace=$1
  for resource in $(kubectl api-resources --verbs=list --namespaced -o name | grep -v "events.events.k8s.io" | grep -v "events" | sort | uniq); do
    echo "Resource:" ${resource}
    kubectl -n ${namespace} get --ignore-not-found ${resource}
  done
}
```
### eksctl
- install 
```shell
brew tap weaveworks/tap
brew install weaveworks/tap/eksctl
```
https://eksctl.io/introduction/#for-macos
- Auto completion
```shell
# For zsh completion, please run
mkdir -p ~/.zsh/completion/
eksctl completion zsh > ~/.zsh/completion/_eksctl
# and put the following in ~/.zshrc:
fpath=($fpath ~/.zsh/completion)
```
https://eksctl.io/introduction/#zsh

### aws cli
- install 
https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
- Auto completion
https://docs.aws.amazon.com/zh_tw/cli/latest/userguide/cli-configure-completion.html

### k9s


### Lens

## Create cluster by eksctl
- Create an Amazon EKS cluster IAM role
- Create cluster
```shell=
eksctl create nodegroup \
  --cluster my-cluster \
  --region region-code \
  --name my-mng \
  --node-ami-family ami-family \ #https://eksctl.io/usage/custom-ami-support/#setting-the-node-ami-family
  --node-type m5.large \
  --nodes 3 \
  --nodes-min 2 \
  --nodes-max 4 \
  --ssh-access \
  --ssh-public-key my-key
If you want to block Pod access to IMDS, then add the --disable-pod-imds option to the following command.
```

- Update eks version 
```shell

```

- Update eks version with launch template
```shell

```

- Customizing managed nodes with launch templates
```shell

---
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: my-cluster
  region: region-code

managedNodeGroups:
  - name: my-nodegroup
    ami: ami-1234567890abcdef0
    instanceType: m5.large
    privateNetworking: true
    disableIMDSv1: true
    labels: { x86-al2-specified-mng }
    overrideBootstrapCommand: |
      #!/bin/bash
      /etc/eks/bootstrap.sh my-cluster \
        --b64-cluster-ca certificate-authority \
        --apiserver-endpoint api-server-endpoint \
        --dns-cluster-ip service-cidr.10 \
        --container-runtime containerd \
        --kubelet-extra-args '--max-pods=my-max-pods-value' \
        --use-max-pods false
```

## aws-auth format
https://github.com/kubernetes-sigs/aws-iam-authenticator#full-configuration-format

## Best practices for Kubernetes Secrets management
ref: https://snyk.io/blog/best-practices-for-kubernetes-secrets-management/
- Encrypted Confidential Data at Rest
ref: https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/

## Kubernetes Apply vs. Replace vs. Patch
ref: https://blog.atomist.com/kubernetes-apply-replace-patch/

## Deploy Metrics Server
```shell
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```
https://docs.aws.amazon.com/eks/latest/userguide/metrics-server.html

## Deploy ALB ingress controller
```shell
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=$cluster
```
https://docs.aws.amazon.com/zh_tw/eks/latest/userguide/aws-load-balancer-controller.html