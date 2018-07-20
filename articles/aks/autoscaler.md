---
title: Usługi Kubernetes na platformie Azure — skalowanie klastra
description: Dowiedz się, jak za pomocą skalowania automatycznego klastra za pomocą usługi Azure Kubernetes Service (AKS) automatycznie Skaluj klaster w celu spełnienia określonych wymagań.
services: container-service
author: sakthivetrivel
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/19/18
ms.author: sakthivetrivel
ms.custom: mvc
ms.openlocfilehash: 629659a3a5090bae987be77637a574fcbe0abe98
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39164014"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Klastrze skalowania automatycznego w systemie Azure Kubernetes Service (AKS) — wersja zapoznawcza

Usługa Azure Kubernetes Service (AKS) zapewnia elastyczne rozwiązanie, aby wdrożyć zarządzany klaster Kubernetes na platformie Azure. Jako zasób wzrostu wymagań, klaster do klastra, aby rozwijać, aby spełnić wymagania tego na podstawie ograniczeń ustawisz zezwala na skalowania automatycznego. Skalowanie klastra (CA) robi to poprzez skalowanie węzłów agenta na podstawie czasu zasobników. Skanuje klastra okresowo, aby wyszukać oczekujące zasobników lub pustych węzłów ono i zwiększa rozmiar, jeśli jest to możliwe. Domyślnie urząd certyfikacji skanuje pod kątem oczekujących zasobników, co 10 sekund i usunięcie węzła, jeśli jest niepotrzebne przez więcej niż 10 minut. W przypadku użycia za pomocą skalowania automatycznego zasobników w poziomie (HPA), HPA zaktualizuje replik zasobników i zasobów dla każdego zapotrzebowania. Jeśli nie ma wystarczającej liczby węzłów lub niepotrzebne węzłów, zgodnie z tym skalowanie zasobników, urząd certyfikacji będzie odpowiadać i zaplanować zasobników w nowy zestaw węzłów.

> [!IMPORTANT]
> Integracja skalowanie klastra usługi Azure Kubernetes Service (AKS) jest obecnie w **Podgląd**. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.
>

## <a name="prerequisites"></a>Wymagania wstępne

W tym dokumencie przyjęto założenie, iż klaster AKS z włączoną funkcją RBAC. Jeśli potrzebujesz klastra AKS, zobacz [szybkiego startu usługi Azure Kubernetes Service (AKS)][aks-quick-start].

 Aby użyć skalowanie klastra, klaster musi używać Kubernetes v1.10.X lub nowszy i musi być włączona RBAC. Aby uaktualnić klaster, zobacz artykuł [Uaktualnianie klastra usługi AKS][aks-upgrade].

## <a name="gather-information"></a>Zbieranie informacji

W poniższej tabeli wymieniono wszystkie informacje, które należy podać w definicji skalowania automatycznego.

Pobierz identyfikator subskrypcji przy użyciu: 

``` azurecli
az account show --query id
```

Generować zestaw poświadczeń platformy Azure, uruchamiając następujące polecenie:

```console
$ az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription-id>" --output json

"appId": <app-id>,
"displayName": <display-name>,
"name": <name>,
"password": <app-password>,
"tenant": <tenant-id>
```

Identyfikator aplikacji, hasło i identyfikator dzierżawy będzie Twoim clientID, clientSecret i identyfikator dzierżawy w poniższych krokach.

Pobierz nazwę puli węzeł, uruchamiając następujące polecenie. 

```console
$ kubectl get nodes --show-labels
```

Dane wyjściowe:

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

Następnie wyodrębnić wartość etykiety **obiektu agentpool**. Domyślna nazwa dla puli węzłów klastra jest "nodepool1".

Aby uzyskać nazwę grupy zasobów węzła, wyodrębnić wartość etykiety **kubernetes.azure.com<span></span>/klastra**. Nazwa grupy zasobów węzła zazwyczaj mają postać MC_ [grupy zasobów]\__ [nazwa klastra] [lokalizacja].

Parametr vmType odwołuje się do usługi jest używane, w tym miejscu, czyli usługi AKS.

Teraz powinien mieć następujące informacje:

- Identyfikator subskrypcji
- ResourceGroup
- ClusterName
- ClientID
- ClientSecret
- Identyfikator dzierżawy
- NodeResourceGroup
- VMType

Następnie kodowania, wszystkie te wartości przy użyciu base64. Na przykład, aby zakodować VMType wartością base64:

```console
$ echo AKS | base64
QUtTCg==
```

## <a name="create-secret"></a>Utwórz wpis tajny
Przy użyciu tych danych, Utwórz wpis tajny do wdrożenia przy użyciu wartości znajdujących się w poprzednich krokach, takich jak:

- ClientID: `<base64-encoded-client-id>`
- ClientSecret: `<base64-encoded-client-secret>`
- Grupa zasobów: `<base64-encoded-resource-group>` (należy używać małych liter)
- Identyfikator subskrypcji: `<base64-encode-subscription-id>`
- Identyfikator dzierżawy: `<base64-encoded-tenant-id>`
- VMType: `<base64-encoded-vm-type>`
- ClusterName: `<base64-encoded-clustername>`
- NodeResourceGroup: `<base64-encoded-node-resource-group>` (Użyj, wartość etykiety będzie verbatim. Wielkość liter)

w następującym formacie:

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>
  ResourceGroup: <base64-encoded-resource-group>
  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

## <a name="create-a-deployment-chart"></a>Tworzenie wykresu wdrożenia

Utwórz plik o nazwie `aks-cluster-autoscaler.yaml`i skopiuj go do poniższego kodu YAML.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["events","endpoints"]
  verbs: ["create", "patch"]
- apiGroups: [""]
  resources: ["pods/eviction"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["pods/status"]
  verbs: ["update"]
- apiGroups: [""]
  resources: ["endpoints"]
  resourceNames: ["cluster-autoscaler"]
  verbs: ["get","update"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["watch","list","get","update"]
- apiGroups: [""]
  resources: ["pods","services","replicationcontrollers","persistentvolumeclaims","persistentvolumes"]
  verbs: ["watch","list","get"]
- apiGroups: ["extensions"]
  resources: ["replicasets","daemonsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["policy"]
  resources: ["poddisruptionbudgets"]
  verbs: ["watch","list"]
- apiGroups: ["apps"]
  resources: ["statefulsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["storage.k8s.io"]
  resources: ["storageclasses"]
  verbs: ["get", "list", "watch"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: Role
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["configmaps"]
  resourceNames: ["cluster-autoscaler-status"]
  verbs: ["delete","get","update"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: RoleBinding
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  labels:
    app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cluster-autoscaler
  template:
    metadata:
      labels:
        app: cluster-autoscaler
    spec:
      serviceAccountName: cluster-autoscaler
      containers:
      - image: k8s.gcr.io/cluster-autoscaler:{{ ca_version }}
        imagePullPolicy: Always
        name: cluster-autoscaler
        resources:
          limits:
            cpu: 100m
            memory: 300Mi
          requests:
            cpu: 100m
            memory: 300Mi
        command:
        - ./cluster-autoscaler
        - --v=3
        - --logtostderr=true
        - --cloud-provider=azure
        - --skip-nodes-with-local-storage=false
        - --nodes=1:10:nodepool1
        env:
        - name: ARM_SUBSCRIPTION_ID
          valueFrom:
            secretKeyRef:
              key: SubscriptionID
              name: cluster-autoscaler-azure
        - name: ARM_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: ResourceGroup
              name: cluster-autoscaler-azure
        - name: ARM_TENANT_ID
          valueFrom:
            secretKeyRef:
              key: TenantID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_ID
          valueFrom:
            secretKeyRef:
              key: ClientID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_SECRET
          valueFrom:
            secretKeyRef:
              key: ClientSecret
              name: cluster-autoscaler-azure
        - name: ARM_VM_TYPE
          valueFrom:
            secretKeyRef:
              key: VMType
              name: cluster-autoscaler-azure
        - name: AZURE_CLUSTER_NAME
          valueFrom:
            secretKeyRef:
              key: ClusterName
              name: cluster-autoscaler-azure
        - name: AZURE_NODE_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: NodeResourceGroup
              name: cluster-autoscaler-azure
      restartPolicy: Always
```

Skopiuj i Wklej klucz tajny, utworzony w poprzednim kroku i wstaw go na początku pliku.

Następnie ustaw zakres węzłów, wypełnij argument `--nodes` w obszarze `command` w postaci MIN:MAX:NODE_POOL_NAME. Na przykład: `--nodes=3:10:nodepool1` Ustawia minimalną liczbę węzłów do 3, maksymalną liczbę węzłów do 10, a nazwa puli węzeł, aby nodepool1.

Następnie wypełnij pola obrazu, w obszarze **kontenery** z wersją skalowanie klastra, którego chcesz użyć. AKS wymaga v1.2.2 lub nowszej. W przykładzie poniżej użyto v1.2.2.

## <a name="deployment"></a>Wdrożenie

Wdrażanie klastra — skalowanie, uruchamiając

```console
kubectl create -f cluster-autoscaler-containerservice.yaml
```

Aby sprawdzić, czy działa skalowanie klastra, użyj następującego polecenia, a następnie zapoznaj się z listą zasobników. W przypadku zasobników z prefiksem "klaster-skalowania automatycznego" Uruchamianie usługi skalowania automatycznego klastra został wdrożony.

```console
kubectl -n kube-system get pods
```

Aby wyświetlić stan skalowania automatycznego klastra, uruchom

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

## <a name="next-steps"></a>Kolejne kroki

Skalowanie klastra za pomocą skalowania automatycznego zasobników w poziomie, zapoznaj się z [skalowania aplikacji Kubernetes i infrastruktury][aks-tutorial-scale].

Dowiedz się więcej na temat wdrażania usługi AKS i zarządzania nią z samouczków usługi AKS.

> [!div class="nextstepaction"]
> [Samouczek usługi AKS][aks-tutorial-prepare-app]

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md

<!-- LINKS - external -->
[cluster-autoscale]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md
