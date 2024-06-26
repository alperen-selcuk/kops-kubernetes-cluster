# kops-kubernetes-cluster-on-google-cloud

github sayfasından kops release indirilir.

```https://github.com/kubernetes/kops```

google üzerinde işlem yapacağımız için google cloud sdk bilgisayarınıza indirmeniz ve auth olmanız gerekmektedir. 

https://cloud.google.com/sdk/docs/install-sdk

```gcloud auth login```

```gcloud auth application-default login```

unique bir bucket yaratmamız gerekiyor.

```gsutil mb -l europe-north1 gs://my-kops-clusters-111/```

cluster kurulumu için gerekli variablelar

```
PROJECT=`gcloud config get-value project`

export KOPS_STATE_STORE=gs://my-kops-clusters-111/
```

cluster için config oluşturuyoruz öncelikle.

```kops create cluster simple.k8s.local --node-count 3 --zones europe-north1-a --state ${KOPS_STATE_STORE}/ --project=${PROJECT}```

özelliklerini değiştirmek istiyorsak onları değiştiriyoruz. özellikle master node un yüksek cpulu makina olmasına özen gösterin. en az n2-standart-2 olması lazım, yoksa masterda bazı podlar ayağa kalkmıyor.

```
kops edit ig --name=simple.k8s.local nodes-europe-north1-a

kops edit ig --name=simple.k8s.local master-europe-north1-a
```

ve en son cluster ı kuruyoruz ve 10 dakika kadar bekliyoruz.

```kops update cluster --name simple.k8s.local --state ${KOPS_STATE_STORE} --yes --admin```


cluster kurduktan sonra kubeconfiği aşağıdaki komutlar çekeriz.

```kops export kubecfg simple.k8s.local --state ${KOPS_STATE_STORE} --admin```

config çektikten sonra validate edeceğiz.

```kops validate cluster --state ${KOPS_STATE_STORE}```

****

eğer multi node master kurmak istiyorsanız. 

```
kops create cluster \
    --node-count 3 \
    --master-count 3 \
    --zones europe-north1-a,europe-north1-b \
    --master-zones europe-north1-a,europe-north1-b \
    hacluster.k8s.local
```
