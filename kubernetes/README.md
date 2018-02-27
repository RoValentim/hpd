# 23/02/2018 - Kubernetes

## Pré-requisito
Ao utilizar, se baseie em:

```
https://12factor.net
```

Ele é baseado em master e worker.

Utilizado apenas através de API.

Kuberlet recebe as requisições para conversar com os containers e com a engine.

Kube Proxy ele que gerencia as regras de rede.

Portas que devem estar abertas:
MASTER
kube-apiserver => 6443 TCP
etcd server API => 2379-2380 TCP
Kubelet API => 10250 TCP
kube-scheduler => 10251 TCP
kube-controller-manager => 10252 TCP
Kubelet API Read-only => 10255 TCP

WORKER
Kubelet API => 10250 TCP
Kubelet API Read-only => 10255 TCP
NodePort Services => 30000-32767 TCP


Se trabalha por POD, não por container, ele pode ter mais de um, container.


## Instalação
```
curl -fsSL https://get.docker.com | bash
yum update && yum install apt-transport-https

vim /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg

yum update
setenforce 0
yum install -y kubelet kubeadm kubectl
systemctl enable kubelet && systemctl start kubelet

cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sysctl --system
swapoff -a
vi /etc/fstab e desabilita o swap

Na máquina master:
kubeadm init --pod-network-cidr 192.168.0.0/16

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config


kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"


vim /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
(tirar o systemd e colocar cgroupfs no lugar)
```

### Para testar e verificar que está tudo ok:
```
docker container run hello-world
```


kubectl get node
kubectl get pod
kubectl get rs



echo "source <(kubelet completion bash)" >> ~/.bashrc

#
kubeadm reset
systemctl daemon-reload
systemctl restart kubelet

systemctl stop firewalld

https://kubernetes.io/docs/concepts/cluster-administration/addons/



kubectl get pods --all-namespaces
Unable to connect to the server: net/http: TLS handshake timeoutdocker ps ok



kubeadm join --token 68110f.bb49920375c8b539 10.142.0.8:6443 --discovery-token-ca-cert-hash sha256:e97e8496fa3765a0a11fb0aa7b5dd28a9d1eeb2fb82e477dc9f7914b0c1c5422





# 26/02/2018 - Kubernetes

systemctl start docker
systemctl restart kubelet
kubectl get pod --all-namespaces
kubectl get namespace
cat .kube/config
kubectl get pod -n kube-system
kubectl run nginx --image nginx
kubelet get deployment
kubelet describe deployment
kubectl get replicaset
kubectl get replicaset --all-namespaces
kubectl describe replicaset nginx-<ID>
kubectl get node
kubectl describe pode nginx-<ID>
kubectl get event
kubectl get deployment -o yaml
kubectl get rs
kubectl get pod

kubectl get deployment -o yaml > primeiro.yaml
Mudar o label para outro qualquer (giropops)
Mudar o name para outro qualquer (nginx2)
Mudar para 3 replicas

kubectl create -f primeiro.yaml

kubectl edit deployment nginx2
Mudar para 5 replicas e label

kubectl get replicaset
kubectl get replicaset nginx2-<ID>
kubectl get pods -L dc
kubectl get pods -L app

kubectl rollout history deployment
kubectl rollout history deployment nginx2
kubectl rollout history deployment nginx2 --revision 1
kubectl rollout history deployment nginx2 --revision 2
kubectl rollout history undo nginx2 --to-revision 1

kubectl -o wide

vim primeiro.yaml
Mudar para nginx3
Mudar em "spec:" adicionando (detro de conntainer):
   ports:
      - containerPort: 80
        protocol: TCP
Ex.:
spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
        terminationMessagePat


kubectl create -f primeiro.yaml
kubectl expose deployment nginx3
kubectl get service

kubectl delete service nginx3
kubectl expose deployment nginx3 --type NodePort
kubectl get service
(Testar no navegador na porta exposta)

kubectl delete service nginx3
kubectl expose deployment nginx3 --type LoadBalancer
kubectl get service

kubectl get service nginx3 -o yaml


kubelet get deployment
kubectl delete deployment nginx nginx2

kubectl scale deployment nginx3 --replicas=5
kubectl get pod -o wide
kubectl delete pod

kubectl exec -ti nginx3-<ID> bash
kubectl exec nginx3-<ID> printenv | greps KUBERNETES
kubectl logs nginx3-<ID> ou kubectl logs -f nginx3-<ID>
Logs dos containers: /var/log/containers



kubectl run hog --image vish/stress

kubectl get deployment hog -o yaml > limite.yaml
Em resources: do spec do template
  limits:
    memory: "64Mi"
    cpu: 1
  requests:
    memory: "48Mi"
args:
  - -cpus
  - -2
  (ver no video)



kubectl create -f limite.yaml
kubectl describe deployment hog-limite


kubectl get deployment hog -o yaml > limite.yaml
Em resources:
label hog-limit
name hog
run hog
após resources: e no mesmo nível
args:
  limits:
    memory: "64Mi"
  requests:
    memory: "48Mi"


kubectl describe deployment hog-limite



kubectl create namespace limitado

vim limitado.yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: limitado
spec:
  limits:
  - default:
      cpu: 1
      memory: "48Mi"
    defaultRequest:
      cpu: 0.5
      memory: "40Mi"
    type: Container

  limits:
  - default:
      cpu: 1
      memory: "48Mi"
    defaultRequest:
      cpu: 0.5
      memory: "40Mi"
    type: Container



kubectl create -f limitado.yaml -n limitado
kubectl get LimitRange -n limitado
kubectl describe LimitRange -n limitado
kubectl run limitado-hog --image vish/stress -n limitado
kubectl get pod -o wide
kubectl get pod -o wide -n limitado
kubectl deployment pod -o wide -n limitado
kubectl describe deployment -n limitado limitado-hog
kubectl get pod -o wide -n limitado
kubectl describe pod limitado<ID> -n limitado
kubectl -n limitado get pod limitado<ID> -o yaml > limitado-hog.yaml




kubectl get deploymen
kubectl delete deployment <todos>


kubectl run nginx --image nginx nginx

kubectl get node,deployment,rs,

kubectl get deployment -o yaml > segundo
vim nele
apagar o creation e toda a lista de status
name: nginx-novo
em template abaixo de run:
  dc: Brazil
  app: girus

depois de terminationGracePeriodSeconds: 30, no mesmo nível
nodeSelector:
  disk: ssd


Funciona:
spec:
      containers:
      - image: nginx
	imagePullPolicy: Always
	name: nginx
	ports:
	- containerPort: 80
	  protocol: TCP
	resources: {}
	terminationMessagePath: /dev/termination-log
	terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
      nodeSelector:
	disk: ssd
up



kubectl label node kubernates-1 disk=ssd
kubectl label node kubernates-2 disk=hdd

kubectl describe node kubernates-1

kubectl create -f segundo.yaml

<ver no video outros comandos>













# 27/02/2018 - Kubernetes
https://github.com/kubernetes/kops

systemctl start docker
systemctl restart kubelet
kubectl get pod --all-namespaces

Na Master:
apt-get install -y nfs-kernel-server
ou
yum -y install nfs-utils
systemctl enable nfs-server.service
systemctl start nfs-server.service

mkdir /opt/agility
chmod 1777 /opt/agility
cd /opt/agility
echo "GIROPOPS STRIGUS GIRUS" > giropops

vim /etc/exports
Adicionar:
/opt/agility	*	(rw,sync,no_root_squash,subtree_check)

exportfs -ar


Nas outras:
apt-get install nfs-common
ou
yum -y install nfs-utils

showmount -e <IP MASTER>
showmount -e 35.231.52.138
showmount -e 10.32.0.1
mount <IP MASTER>:/opt/agility /mnt

Na master:
vim pv.yaml
Arquivo:
kind: PersistentVolume
apiVersion: v1
metadata:
  name: meu-vol
  labels:
    type: local
spec:
  capacity:
    storage: 100M
  accessModes:
  - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  nfs:
    path: /opt/agility
    server: <IP MASTER>
    readOnly: false

kubectl create -f pv.yaml


vim pvc.yaml
Arquivo:
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: meu-pvc
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 10Mi

kubectl create -f pvc.yaml


vim segundo.yaml
Arquivo:
apiVersion: v1
items:
- apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    annotations:
      deployment.kubernetes.io/revision: "1"
    generation: 1
    labels:
      run: nginx
    name: nginx2
    namespace: default
    resourceVersion: "18707"
  spec:
    replicas: 1
    selector:
      matchLabels:
        run: nginx
    strategy:
      rollingUpdate:
        maxSurge: 1
        maxUnavailable: 1
      type: RollingUpdate
    template:
      metadata:
        creationTimestamp: null
        labels:
          run: nginx
      spec:
        containers:
        - image: nginx
          imagePullPolicy: Always
          name: nginx
	  volumeMounts:
	  - name: nfs-vol
	    mountPath: /opt
          ports:
          - containerPort: 80
            protocol: TCP
          resources: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
        volumes:
        - name: nfs-vol
          persistentVolumeClaim:
            claimName: meu-pvc
        dnsPolicy: ClusterFirst
        restartPolicy: Always
        schedulerName: default-scheduler
        securityContext: {}
        terminationGracePeriodSeconds: 30
        nodeSelector:
          disk: ssd
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""

kubectl create -f segundo.yaml


kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
kubectl get pods
kubectl get pods --all-namespaces
kubectl get -n kube-system deployment
kubectl get -n kube-system service
kubectl edit service -n kube-system kubernetes-dashboard
troca o  type de ClusterIP para NodePort

kubectl get -n kube-system service
tentar acessar o ip mostrado de fora

Se não for:
kubectl proxy --address=
Isso vai bindar todos os endereços

kubectl proxy --address=<IP PUBLICO>
kubectl proxy --address=35.231.52.138 --accept-hosts='.' --accept-paths='.' --port 443
kubectl proxy --address= --accept-hosts='.' --accept-paths='.' --port 443

vim user.yaml
Arquivo:
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system


vim rbac.yaml
Arquivo:
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kube-system


kubectl create -f user.yaml
kubectl create -f rbac.yaml
kubectl get serviceaccount -n kube-system
kubectl -n kube-system get secret
kubectl describe secret <SECRET> -n kube-system
kubectl describe secret admin-user -n kube-system
ou
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')


Pegar o token do admin-user

kubectl proxy --address= --accept-hosts='.' --accept-paths='.' --port 443 &
kubectl proxy --address=35.231.52.138 --accept-hosts='.' --accept-paths='.' --port 443
kubectl proxy --address=10.32.0.1 --accept-hosts='.' --accept-paths='.' --port 443
kubectl proxy --address= --accept-hosts '.' --accept-paths '.' --port 80


Acessar o endereço externo e usar o token para acesso (não funciona no Chrome)


HELM
wget https://storage.googleapis.com/kubernetes-helm/helm-v2.7.0-linux-amd64.tar.gz
e descompacar em /usr/local/bin

wget goo.gl/nbEcHn
tar -xvf nbEcHn
cp linux-amd64/helm /usr/bin/
chmod +x /usr/bin/helm

kubectl create serviceaccount -n kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
kubectl patch deployment tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}' -n kube-system
helm init

helm search dashboard
helm home
helm version
helm init --upgrade
helm --debug install stable/kubernetes-dashboard
helm search database
helm --debug install stable/mariadb --set persistence.enabled=false



#
