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
~> gcloud config set compute/zone asia-southeast1-b
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

# kubectl
```
~> wget https://storage.googleapis.com/kubernetes-release/release/v1.9.0/bin/linux/amd64/kubectl
~> chmod +x kubectl
~> sudo mv kubectl /usr/local/bin/
```
```
~> kubectl version --client
Client Version: version.Info{Major:"1", Minor:"9", GitVersion:"v1.9.0", GitCommit:"925c127ec6b946659ad0fd596fa959be43f0cc05", GitTreeState:"clean", BuildDate:"2017-12-15T21:07:38Z", GoVersion:"go1.9.2", Compiler:"gc", Platform:"linux/amd64"}
```
