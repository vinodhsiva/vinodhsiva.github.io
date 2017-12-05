---
published: false
---
## Create a K8s Cluster in AWS with RBAC support



Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.

Install Kops in your control machine

> curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
chmod +x kops-linux-amd64
sudo mv kops-linux-amd64 /usr/local/bin/kops

kubectl is the prerequestic here. Can be downloaded from 

curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl

_chmod +x ./kubectl
 mv ./kubectl /usr/local/bin/kubectl_

Export Name of your cluster
_export NAME=devk8s.domain.com_


Create cluster

kops create cluster ${NAME} 
--cloud aws --zones eu-west-2a \ 
-node-size t2.medium \
--master-size m4.large \
--networking weave \
--authorization RBAC \
--topology private \
--bastion="true" \
--kubernetes-version "1.8.4" \
--ssh-access 217.138.124.34/32 \
--yes

After the cluster created in Aws, you can see 4 ec2 instances: bastion, master, node1, node2.

To validate the created cluster
- > * validate cluster: kops validate cluster
-  * list nodes: kubectl get nodes --show-labels
-  * ssh to the bastion: ssh -A -i ~/.ssh/id_rsa admin@bastion.domain.com-



2. add dashboard

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.7.1/src/deploy/recommended/kubernetes-dashboard.yaml

dashboard url

https://api.devk8s.fleet-360.com/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy

But having issues with permission.

Assigned cluster role to the kubernetes-dashboard. Able to see now..


kubectl create clusterrolebinding k8-dashboard-cluster-admin \
  --clusterrole=cluster-admin \
  --serviceaccount=kube-system:kubernetes-dashboard


--------------------------------

Deploy ingress