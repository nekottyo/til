kubernetes-the-hard-way
===

https://github.com/kelseyhightower/kubernetes-the-hard-way

# 01-prerequisites

google cloud sdk setup
```
> curl https://sdk.cloud.google.com | bash       
> gcloud init
```

```
~> gcloud version
Google Cloud SDK 187.0.0
bq 2.0.28
core 2018.01.28
gsutil 4.28
```

```
~> gcloud config set compute/region asia-northeast1  
Updated property [compute/region].
```

```
~> gcloud config set compute/zone asia-northeast1-b
Updated property [compute/zone].
```

-----
# 02-client-tools

## cfssl

```
~> wget -q --show-progress --https-only --timestamping \
 https://pkg.cfssl.org/R1.2/cfssl_linux-amd64 \
 https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64
~> chmod +x cfssl_linux-amd64 cfssljson_linux-amd64
~> sudo mv cfssl_linux-amd64 /usr/local/bin/cfssl
~> sudo mv cfssljson_linux-amd64 /usr/local/bin/cfssljson
```

```
~> cfssl version
Version: 1.2.0
Revision: dev
Runtime: go1.6
```

## kubectl
```
~> wget https://storage.googleapis.com/kubernetes-release/release/v1.9.0/bin/linux/amd64/kubectl
~> chmod +x kubectl
~> sudo mv kubectl /usr/local/bin/
```
```
~> kubectl version --client
Client Version: version.Info{Major:"1", Minor:"9", GitVersion:"v1.9.0", GitCommit:"925c127ec6b946659ad0fd596fa959be43f0cc05", GitTreeState:"clean", BuildDate:"2017-12-15T21:07:38Z", GoVersion:"go1.9.2", Compiler:"gc", Platform:"linux/amd64"}
```

---
# 03-compute-resources


## create network
```
> gcloud compute networks create kubernetes-the-hard-way --subnet-mode custom
Created [https://www.googleapis.com/compute/v1/projects/api-project-616665585505/global/networks/kubernetes-the-hard-way].
NAME                     SUBNET_MODE  BGP_ROUTING_MODE  IPV4_RANGE  GATEWAY_IPV4
kubernetes-the-hard-way  CUSTOM       REGIONAL

Instances on this network will not be reachable until firewall rules
are created. As an example, you can allow all internal traffic between
instances as well as SSH, RDP, and ICMP by running:
```

## create subnet

```
~> gcloud compute networks subnets create kubernetes \
 --network kubernetes-the-hard-way \
 --range 10.240.0.0/24

Created [https://www.googleapis.com/compute/v1/projects/api-project-616665585505/regions/asia-northeast1/subnetworks/kubernetes].
NAME        REGION           NETWORK                  RANGE
kubernetes  asia-northeast1  kubernetes-the-hard-way  10.240.0.0/24
```

## create firewall-rules

```
~> gcloud compute firewall-rules create kubernetes-the-hard-way-allow-external \
 --allow tcp:22,tcp:6443,icmp \
 --network kubernetes-the-hard-way \
 --source-ranges 0.0.0.0/0

Creating firewall...|Created [https://www.googleapis.com/compute/v1/projects/api-project-616665585505/global/firewalls/kubernetes-the-hard-way-allow-external].
Creating firewall...done.
NAME                                    NETWORK                  DIRECTION  PRIORITY  ALLOW                 DENY
kubernetes-the-hard-way-allow-external  kubernetes-the-hard-way  INGRESS    1000      tcp:22,tcp:6443,icmp
```

```
~> gcloud compute firewall-rules list --filter="network:kubernetes-the-hard-way"

NAME                                    NETWORK                  DIRECTION  PRIORITY  ALLOW                 DENY
kubernetes-the-hard-way-allow-external  kubernetes-the-hard-way  INGRESS    1000      tcp:22,tcp:6443,icmp
kubernetes-the-hard-way-allow-internal  kubernetes-the-hard-way  INGRESS    1000      tcp,udp,icmp
```

## get address
```
~> gcloud compute addresses create kubernetes-the-hard-way \
 --region $(gcloud config get-value compute/region)
Created [https://www.googleapis.com/compute/v1/projects/api-project-616665585505/regions/asia-northeast1/addresses/kubernetes-the-hard-way].
```
```
~> gcloud compute addresses list --filter="name=('kubernetes-the-hard-way')"

NAME                     REGION           ADDRESS        STATUS
kubernetes-the-hard-way  asia-northeast1  XX.XX.XX.XX  RESERVED
```

## create machines
```
~> for i in 0 1 2; do
 gcloud compute instances create controller-${i} \
   --async \
   --boot-disk-size 200GB \
   --can-ip-forward \
   --image-family ubuntu-1604-lts \
   --image-project ubuntu-os-cloud \
   --machine-type n1-standard-1 \
   --private-network-ip 10.240.0.1${i} \
   --scopes compute-rw,storage-ro,service-management,service-control,logging-write,monitoring \
   --subnet kubernetes \
   --tags kubernetes-the-hard-way,controller
done

Instance creation in progress for [controller-0]: https://www.googleapis.com/compute/v1/projects/api-project-616665585505/zones/asia-northeast1-b/operations/operation-1517993540144-5649b69bb4b81-530a0e13-5f1dbb2a
Use [gcloud compute operations describe URI] command to check the status of the operation(s).
Instance creation in progress for [controller-1]: https://www.googleapis.com/compute/v1/projects/api-project-616665585505/zones/asia-northeast1-b/operations/operation-1517993542591-5649b69e0a219-3f60eff6-2eafe49c
Use [gcloud compute operations describe URI] command to check the status of the operation(s).
Instance creation in progress for [controller-2]: https://www.googleapis.com/compute/v1/projects/api-project-616665585505/zones/asia-northeast1-b/operations/operation-1517993544939-5649b6a0475f8-baee9a94-f26ffd36
Use [gcloud compute operations describe URI] command to check the status of the operation(s).
```
```
~> for i in 0 1 2; do
 gcloud compute instances create worker-${i} \
   --async \
   --boot-disk-size 200GB \
   --can-ip-forward \
   --image-family ubuntu-1604-lts \
   --image-project ubuntu-os-cloud \
   --machine-type n1-standard-1 \
   --metadata pod-cidr=10.200.${i}.0/24 \
   --private-network-ip 10.240.0.2${i} \
   --scopes compute-rw,storage-ro,service-management,service-control,logging-write,monitoring \
   --subnet kubernetes \
   --tags kubernetes-the-hard-way,worker
done
Instance creation in progress for [worker-0]: https://www.googleapis.com/compute/v1/projects/api-project-616665585505/zones/asia-northeast1-b/operations/operation-1517993578839-5649b6c09bbdb-9a971849-48b656aa
Use [gcloud compute operations describe URI] command to check the status of the operation(s).
Instance creation in progress for [worker-1]: https://www.googleapis.com/compute/v1/projects/api-project-616665585505/zones/asia-northeast1-b/operations/operation-1517993581264-5649b6c2ebc81-dc65e179-182c0586
Use [gcloud compute operations describe URI] command to check the status of the operation(s).
Instance creation in progress for [worker-2]: https://www.googleapis.com/compute/v1/projects/api-project-616665585505/zones/asia-northeast1-b/operations/operation-1517993583395-5649b6c4f40bb-a2d1645d-d5d37762
Use [gcloud compute operations describe URI] command to check the status of the operation(s).
```

```
~> gcloud compute instances list
NAME          ZONE               MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP     STATUS
controller-0  asia-northeast1-b  n1-standard-1               10.240.0.10  104.198.93.205  RUNNING
controller-1  asia-northeast1-b  n1-standard-1               10.240.0.11  35.194.108.53   RUNNING
controller-2  asia-northeast1-b  n1-standard-1               10.240.0.12  35.200.119.114  RUNNING
worker-0      asia-northeast1-b  n1-standard-1               10.240.0.20  35.190.234.247  RUNNING
worker-1      asia-northeast1-b  n1-standard-1               10.240.0.21  35.200.108.97   RUNNING
worker-2      asia-northeast1-b  n1-standard-1               10.240.0.22  104.198.83.51   RUNNING
```

---

# 04-certificate-authority

## CA

```
~> cfssl gencert -initca ca-csr.json | cfssljson -bare ca
```

```
~> cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -profile=kubernetes \
  admin-csr.json | cfssljson -bare admin
```

```
> ls | xargs -n1
admin.csr
admin-key.pem
admin.pem
ca.csr
ca-key.pem
ca.pem
```

## worker

```
for instance in worker-0 worker-1 worker-2; do
cat > ${instance}-csr.json <<EOF
{
  "CN": "system:node:${instance}",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "JP",
      "L": "XXXXXXX",
      "O": "system:nodes",
      "OU": "Kubernetes The Hard Way",
      "ST": "XXXXX"
    }
  ]
}
EOF

EXTERNAL_IP=$(gcloud compute instances describe ${instance} \
  --format 'value(networkInterfaces[0].accessConfigs[0].natIP)')

INTERNAL_IP=$(gcloud compute instances describe ${instance} \
  --format 'value(networkInterfaces[0].networkIP)')

cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -hostname=${instance},${EXTERNAL_IP},${INTERNAL_IP} \
  -profile=kubernetes \
  ${instance}-csr.json | cfssljson -bare ${instance}
done
```

```
~> ls worker-* | xargs -n1
worker-0.csr
worker-0-key.pem
worker-0.pem
worker-1.csr
worker-1-key.pem
worker-1.pem
worker-2.csr
worker-2-key.pem
worker-2.pem
```

## proxy
```
kubernetes> ls kube-proxy* | xargs -n1
kube-proxy.csr
kube-proxy-csr.json
kube-proxy-key.pem
kube-proxy.pem
```

## API
```
~> KUBERNETES_PUBLIC_ADDRESS=$(gcloud compute addresses describe kubernetes-the-hard-way \
 --region $(gcloud config get-value compute/region) \
 --format 'value(address)')
```

```
> cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -hostname=10.32.0.1,10.240.0.10,10.240.0.11,10.240.0.12,${KUBERNETES_PUBLIC_ADDRESS},127.0.0.1,kubernetes.default \
  -profile=kubernetes \
  kubernetes-csr.json | cfssljson -bare kubernetes
```

```
~> ls kubernetes*.pem
kubernetes-key.pem  kubernetes.pem
```

## Distribute

```
~> for instance in worker-0 worker-1 worker-2; do                                                                             
  gcloud compute scp ca.pem ${instance}-key.pem ${instance}.pem ${instance}:~/
done
```

```
> for instance in controller-0 controller-1 controller-2; do
  gcloud compute scp ca.pem ca-key.pem kubernetes-key.pem kubernetes.pem ${instance}:~/
done
```
