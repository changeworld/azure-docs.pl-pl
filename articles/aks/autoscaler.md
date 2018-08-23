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
ms.openlocfilehash: 06b93273ea096f2d24df4411a64ce99d054892cf
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055189"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Klastrze skalowania automatycznego w systemie Azure Kubernetes Service (AKS) — wersja zapoznawcza

Usługa Azure Kubernetes Service (AKS) zapewnia elastyczne rozwiązanie, aby wdrożyć zarządzany klaster Kubernetes na platformie Azure. Jako zasób wzrostu wymagań, klaster do klastra, aby rozwijać, aby spełnić wymagania tego na podstawie ograniczeń ustawisz zezwala na skalowania automatycznego. Skalowanie klastra (CA) robi to poprzez skalowanie węzłów agenta na podstawie czasu zasobników. Skanuje klastra okresowo, aby wyszukać oczekujące zasobników lub pustych węzłów ono i zwiększa rozmiar, jeśli jest to możliwe. Domyślnie urząd certyfikacji skanuje pod kątem oczekujących zasobników, co 10 sekund i usunięcie węzła, jeśli jest niepotrzebne przez więcej niż 10 minut. Gdy jest używane z [skalowania automatycznego zasobników w poziomie](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) (HPA) HPA zaktualizuje replik zasobników i zasobów dla każdego zapotrzebowania. Jeśli nie ma wystarczającej liczby węzłów lub niepotrzebne węzłów, zgodnie z tym skalowanie zasobników, urząd certyfikacji będzie odpowiadać i zaplanować zasobników w nowy zestaw węzłów.

W tym artykule opisano sposób wdrażania skalowanie klastra w węzłach agenta. Jednak ponieważ skalowanie klastra jest wdrażana w przestrzeni nazw systemu kubernetes, skalowania automatycznego nie będą skalowane w dół do węzła, w którym zasobniku ten.

> [!IMPORTANT]
> Integracja skalowanie klastra usługi Azure Kubernetes Service (AKS) jest obecnie w **Podgląd**. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.
>

## <a name="prerequisites"></a>Wymagania wstępne

W tym dokumencie przyjęto założenie, iż klaster AKS z włączoną funkcją RBAC. Jeśli potrzebujesz klastra AKS, zobacz [szybkiego startu usługi Azure Kubernetes Service (AKS)][aks-quick-start].

 Aby użyć skalowanie klastra, klaster musi używać Kubernetes v1.10.X lub nowszy i musi być włączona RBAC. Aby uaktualnić klaster, zobacz artykuł [Uaktualnianie klastra usługi AKS][aks-upgrade].

## <a name="gather-information"></a>Zbieranie informacji

Aby wygenerować uprawnień dla Twojego skalowanie klastra do uruchamiania w klastrze, uruchom ten skrypt powłoki bash:

```sh
#! /bin/bash
ID=`az account show --query id -o json`
SUBSCRIPTION_ID=`echo $ID | tr -d '"' `

TENANT=`az account show --query tenantId -o json`
TENANT_ID=`echo $TENANT | tr -d '"' | base64`

read -p "What's your cluster name? " cluster_name
read -p "Resource group name? " resource_group

CLUSTER_NAME=`echo $cluster_name | base64`
RESOURCE_GROUP=`echo $resource_group | base64`

PERMISSIONS=`az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$SUBSCRIPTION_ID" -o json`
CLIENT_ID=`echo $PERMISSIONS | sed -e 's/^.*"appId"[ ]*:[ ]*"//' -e 's/".*//' | base64`
CLIENT_SECRET=`echo $PERMISSIONS | sed -e 's/^.*"password"[ ]*:[ ]*"//' -e 's/".*//' | base64`

SUBSCRIPTION_ID=`echo $ID | tr -d '"' | base64 `

NODE_RESOURCE_GROUP=`az aks show --name $cluster_name  --resource-group $resource_group -o tsv --query 'nodeResourceGroup' | base64`

echo "---
apiVersion: v1
kind: Secret
metadata:
    name: cluster-autoscaler-azure
    namespace: kube-system
data:
    ClientID: $CLIENT_ID
    ClientSecret: $CLIENT_SECRET
    ResourceGroup: $RESOURCE_GROUP
    SubscriptionID: $SUBSCRIPTION_ID
    TenantID: $TENANT_ID
    VMType: QUtTCg==
    ClusterName: $CLUSTER_NAME
    NodeResourceGroup: $NODE_RESOURCE_GROUP
---"
```

Po wykonaniu kroków w skrypcie, skryptu zostanie wygenerowana swoje dane w postaci danych poufnych, w następujący sposób:

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>$
  ResourceGroup: <base64-encoded-resource-group>  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

Następnie pobierz nazwę puli węzeł, uruchamiając następujące polecenie. 

```console
$ kubectl get nodes --show-labels
```

Dane wyjściowe:

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

Następnie wyodrębnić wartość etykiety **obiektu agentpool**. Domyślna nazwa dla puli węzłów klastra jest "nodepool1".

Teraz przy użyciu klucza tajnego i języka node puli, można utworzyć wykres wdrożenia.

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
kubectl create -f aks-cluster-autoscaler.yaml
```

Aby sprawdzić, czy działa skalowanie klastra, użyj następującego polecenia, a następnie zapoznaj się z listą zasobników. Powinien być prefiksem "klaster-skalowania automatycznego" uruchomiony zasobnik. Jeśli tak się dzieje, usługi skalowania automatycznego klastra została wdrożona.

```console
kubectl -n kube-system get pods
```

Aby wyświetlić stan skalowania automatycznego klastra, uruchom

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

## <a name="interpreting-the-cluster-autoscaler-status"></a>Interpretowanie stan skalowania automatycznego klastra

```console
$ kubectl -n kube-system describe configmap cluster-autoscaler-status
Name:         cluster-autoscaler-status
Namespace:    kube-system
Labels:       <none>
Annotations:  cluster-autoscaler.kubernetes.io/last-updated=2018-07-25 22:59:22.661669494 +0000 UTC

Data
====
status:
----
Cluster-autoscaler status at 2018-07-25 22:59:22.661669494 +0000 UTC:
Cluster-wide:
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 registered=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC

NodeGroups:
  Name:        nodepool1
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0 cloudProviderTarget=1 (minSize=1, maxSize=5))
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 cloudProviderTarget=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC


Events:  <none>
```

Stan skalowania automatycznego klastra można sprawdzić stan skalowania automatycznego klastra w dwóch różnych poziomach: całego klastra i w każdej grupie węzła. Ponieważ AKS aktualnie obsługuje tylko jedną pulę węzłów, te metryki są takie same.

* Kondycja wskazuje ogólną kondycję węzłów. Skalowanie klastra niezbyt dobrze radzi sobie utworzyć lub usuwa węzły w klastrze, ten stan zmieni się "W niedobrej kondycji". Dostępna jest również podział stanu różnych węzłów:
    * "Gotowe" oznacza, że węzeł jest gotowe, aby zasobników zaplanowane na nim.
    * "Braku gotowości" oznacza, że węzeł, które spowodowało przerwanie w dół po jej uruchomieniu.
    * "NotStarted" oznacza, że węzeł nie jest jeszcze całkowicie uruchomiona.
    * "LongNotStarted" oznacza, że węzeł nie powiodło się w limicie uzasadnione.
    * "Zarejestrowanego oznacza, że węzeł jest zarejestrowany w grupie
    * "Wyrejestrować" oznacza, że węzeł znajduje się po stronie dostawcy klastra, ale nie można zarejestrować w usłudze Kubernetes.
  
* ScaleUp pozwala sprawdzić, gdy klaster okaże się, że mogło nastąpić skalowanie w górę w klastrze.
    * Przejście jest stan zmiany węzła lub zmiany liczby węzłów w klastrze.
    * Liczba węzłów gotowy jest liczba węzłów, dostępne i gotowe w klastrze. 
    * CloudProviderTarget jest liczba węzłów skalowanie klastra wykrył, że klastra musi obsługiwać obciążenia.

* ScaleDown pozwala sprawdzić, czy są kandydatami do skalowania w dół. 
    * Można usunąć węzła, który wykrył skalowanie klastra bez wpływu na zdolność klastra do obsługi obciążenia jest kandydatem do skalowania w dół. 
    * Godziny podane pokazują ostatniego zaewidencjonowania klastra dla skalowania w dół kandydatów i jego ostatni czas przejścia.

Na koniec w obszarze zdarzenia można zobaczyć dowolnej skali lub Skaluj w dół do zdarzenia, powodzenia lub niepowodzenia i ich czasy, które posiada przeprowadzać skalowanie klastra.

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
