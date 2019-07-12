## Volcano v0.1 installation on minikube

```
root1@root1-HP-EliteBook-840-G2:~$ minikube start --kubernetes-version=v1.13.0
Starting local Kubernetes v1.13.0 cluster...
Starting VM...
Getting VM IP address...
E0712 11:08:12.789522    4090 start.go:210] Error parsing version semver:  Version string empty
Moving files into cluster...
Setting up certs...
Connecting to cluster...
Setting up kubeconfig...
Stopping extra container runtimes...
Starting cluster components...
Verifying kubelet health ...
Verifying apiserver health ...Kubectl is now configured to use the cluster.
Loading cached images from config file.


Everything looks great. Please enjoy minikube!




root1@root1-HP-EliteBook-840-G2:~$ kubectl get pods --all-namespaces
NAMESPACE     NAME                                  READY   STATUS              RESTARTS   AGE
kube-system   coredns-86c58d9df4-95jng              1/1     Running             0          54s
kube-system   coredns-86c58d9df4-pt56z              1/1     Running             0          54s
kube-system   etcd-minikube                         1/1     Running             0          10s
kube-system   kube-addon-manager-minikube           1/1     Running             0          56s
kube-system   kube-apiserver-minikube               1/1     Running             0          15s
kube-system   kube-controller-manager-minikube      1/1     Running             0          17s
kube-system   kube-proxy-2m8hw                      1/1     Running             0          54s
kube-system   kube-scheduler-minikube               1/1     Running             0          10s
kube-system   kubernetes-dashboard-fb9d74ff-rzx45   0/1     ContainerCreating   0          53s
kube-system   storage-provisioner                   1/1     Running             0          52s



root1@root1-HP-EliteBook-840-G2:~$ curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  7001  100  7001    0     0   4838      0  0:00:01  0:00:01 --:--:--  4834
root1@root1-HP-EliteBook-840-G2:~$ chmod 700 get_helm.sh
root1@root1-HP-EliteBook-840-G2:~$ ./get_helm.sh
Helm v2.14.2 is available. Changing from version v2.14.1.
Downloading https://get.helm.sh/helm-v2.14.2-linux-amd64.tar.gz
Preparing to install helm and tiller into /usr/local/bin
[sudo] password for root1: 
helm installed into /usr/local/bin/helm
tiller installed into /usr/local/bin/tiller
Run 'helm init' to configure helm.
root1@root1-HP-EliteBook-840-G2:~$ helm init --service-account tiller
$HELM_HOME has been configured at /home/root1/.helm.

Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.

Please note: by default, Tiller is deployed with an insecure 'allow unauthenticated users' policy.
To prevent this, run `helm init` with the --tiller-tls-verify flag.
For more information on securing your installation see: https://docs.helm.sh/using_helm/#securing-your-helm-installation
root1@root1-HP-EliteBook-840-G2:~$ kubectl create serviceaccount -n kube-system tiller
serviceaccount/tiller created
root1@root1-HP-EliteBook-840-G2:~$ helm init --service-account tiller
$HELM_HOME has been configured at /home/root1/.helm.
Warning: Tiller is already installed in the cluster.
(Use --client-only to suppress this message, or --upgrade to upgrade Tiller to the current version.)
root1@root1-HP-EliteBook-840-G2:~$ kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
clusterrolebinding.rbac.authorization.k8s.io/tiller-cluster-rule created
root1@root1-HP-EliteBook-840-G2:~$ watch 'kubectl --namespace kube-system get pods | grep tiller'



root1@root1-HP-EliteBook-840-G2:~$ cd servicecomb/go/src/volcano.sh/
root1@root1-HP-EliteBook-840-G2:~/servicecomb/go/src/volcano.sh$ git clone git@github.com:volcano-sh/volcano.git
Cloning into 'volcano'...
remote: Enumerating objects: 13, done.
remote: Counting objects: 100% (13/13), done.
remote: Compressing objects: 100% (13/13), done.
remote: Total 35427 (delta 4), reused 1 (delta 0), pack-reused 35414
Receiving objects: 100% (35427/35427), 41.90 MiB | 1.11 MiB/s, done.
Resolving deltas: 100% (18562/18562), done.
root1@root1-HP-EliteBook-840-G2:~/servicecomb/go/src/volcano.sh$ cd volcano/


root1@root1-HP-EliteBook-840-G2:~/servicecomb/go/src/volcano.sh/volcano$ git checkout v0.1 
Note: checking out 'v0.1'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at feabf5ae Merge pull request #152 from sivanzcw/develop
root1@root1-HP-EliteBook-840-G2:~/servicecomb/go/src/volcano.sh/volcano$ sed -i 's/latest/v0.1/g' installer/chart/volcano/values.yaml 


root1@root1-HP-EliteBook-840-G2:~/servicecomb/go/src/volcano.sh/volcano$ kubectl create namespace asif
namespace/asif created
root1@root1-HP-EliteBook-840-G2:~/servicecomb/go/src/volcano.sh/volcano$ helm plugin install installer/chart/volcano/plugins/gen-admission-secret
Error: plugin already exists
root1@root1-HP-EliteBook-840-G2:~/servicecomb/go/src/volcano.sh/volcano$ helm gen-admission-secret --service volcano-admission-service --namespace asif
creating certs in tmpdir /tmp/tmp.tMnXfy3f24 
Generating RSA private key, 2048 bit long modulus (2 primes)
.......................................+++++
...........+++++
e is 65537 (0x010001)
certificatesigningrequest.certificates.k8s.io/volcano-admission-service.asif created
NAME                             AGE   REQUESTOR       CONDITION
volcano-admission-service.asif   0s    minikube-user   Pending
certificatesigningrequest.certificates.k8s.io/volcano-admission-service.asif approved
secret/volcano-admission-secret created
root1@root1-HP-EliteBook-840-G2:~/servicecomb/go/src/volcano.sh/volcano$ helm install installer/chart/volcano/ --namespace asif --name volcano
NAME:   volcano
LAST DEPLOYED: Fri Jul 12 11:16:10 2019
NAMESPACE: asif
STATUS: DEPLOYED

RESOURCES:
==> v1/ClusterRole
NAME                 AGE
volcano-admission    0s
volcano-controllers  0s
volcano-scheduler    0s

==> v1/ClusterRoleBinding
NAME                      AGE
volcano-admission-role    0s
volcano-controllers-role  0s
volcano-scheduler-role    0s

==> v1/ConfigMap
NAME                         DATA  AGE
volcano-scheduler-configmap  1     0s

==> v1/Deployment
NAME                 READY  UP-TO-DATE  AVAILABLE  AGE
volcano-admission    0/1    1           0          0s
volcano-controllers  0/1    1           0          0s
volcano-scheduler    0/1    1           0          0s

==> v1/Pod(related)
NAME                                  READY  STATUS             RESTARTS  AGE
volcano-admission-797bcc65cc-74lxd    0/1    ContainerCreating  0         0s
volcano-controllers-7f4c6f8c76-xcs5b  0/1    ContainerCreating  0         0s
volcano-scheduler-7996cc756c-7jq6q    0/1    Pending            0         0s

==> v1/Service
NAME                       TYPE       CLUSTER-IP      EXTERNAL-IP  PORT(S)  AGE
volcano-admission-service  ClusterIP  10.106.238.230  <none>       443/TCP  0s

==> v1/ServiceAccount
NAME                 SECRETS  AGE
volcano-admission    1        0s
volcano-controllers  1        0s
volcano-scheduler    1        0s

==> v1alpha1/Queue
NAME     AGE
default  0s


root1@root1-HP-EliteBook-840-G2:~/servicecomb/go/src/volcano.sh/volcano$ helm list
NAME   	REVISION	UPDATED                 	STATUS  	CHART        	APP VERSION	NAMESPACE
volcano	1       	Fri Jul 12 11:16:10 2019	DEPLOYED	volcano-0.0.1	           	asif     
root1@root1-HP-EliteBook-840-G2:~/servicecomb/go/src/volcano.sh/volcano$ kubectl get pods --namespace asif
NAME                                   READY   STATUS    RESTARTS   AGE
volcano-admission-797bcc65cc-74lxd     1/1     Running   0          11m
volcano-controllers-7f4c6f8c76-xcs5b   1/1     Running   0          11m
volcano-scheduler-7996cc756c-7jq6q     1/1     Running   0          11m


```
