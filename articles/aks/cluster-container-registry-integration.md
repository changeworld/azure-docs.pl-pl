---
title: Integracja rejestru kontenerów platformy Azure z usługą Azure Kubernetes
description: Dowiedz się, jak zintegrować usługę Azure Kubernetes Service (AKS) z rejestrem kontenerów platformy Azure (ACR)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 514cc25e1959145c65fe60cd3054cec4ed28f44d
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617421"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Uwierzytelnianie za pomocą usługi Azure Container Registry z poziomu usługi Azure Kubernetes Service

Podczas korzystania z usługi Azure Container Registry (ACR) z usługą Azure Kubernetes Service (AKS), należy ustanowić mechanizm uwierzytelniania. W tym artykule przedstawiono przykłady konfigurowania uwierzytelniania między tymi dwiema usługami platformy Azure. 

Można skonfigurować AKS do integracji ACR w kilku prostych poleceń z interfejsu wiersza polecenia platformy Azure. Ta integracja przypisuje rolę AcrPull do jednostki usługi skojarzonej z klastrem AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Te przykłady wymagają:

* **Rola** **administratora konta** lub konta platformy Azure w **ramach subskrypcji platformy Azure**
* Interfejsu wiersza polecenia platformy Azure w wersji 2.0.73 lub nowszej

Aby uniknąć konieczności **właściciela** lub **roli administratora konta platformy Azure,** można skonfigurować jednostki usługi ręcznie lub użyć istniejącego podmiotu usługi do uwierzytelniania usługi ACR z usługi AKS. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie usługi ACR za pomocą podmiotów usługi](../container-registry/container-registry-auth-service-principal.md) lub [Uwierzytelnianie z kubernetes z kluczem tajnym ściągania](../container-registry/container-registry-auth-kubernetes.md).

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Tworzenie nowego klastra AKS z integracją ACR

Integrację aks i ACR można skonfigurować podczas początkowego tworzenia klastra AKS.  Aby zezwolić klastrowi AKS na interakcję z usługą ACR, używana jest **jednostka usługi** Azure Active Directory. Następujące polecenie interfejsu wiersza polecenia umożliwia autoryzowanie istniejącego usługi ACR w ramach subskrypcji i konfiguruje odpowiednią rolę **ACRPull** dla jednostki usługi. Podaj prawidłowe wartości dla swoich parametrów poniżej.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
$MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

Alternatywnie można określić nazwę usługi ACR przy użyciu identyfikatora zasobu usługi ACR, który ma następujący format:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>` 

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Ten krok może potrwać kilka minut.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Konfigurowanie integracji ACR dla istniejących klastrów AKS

Zintegruj istniejący program ACR z istniejącymi klastrami AKS, podając prawidłowe wartości dla **acr-name** lub **acr-resource-id,** jak poniżej.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```

Lub

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Można również usunąć integrację między klasterem ACR a klasterem AKS z następującymi

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```

lub

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Praca z ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Importowanie obrazu do usługi ACR

Zaimportuj obraz z centrum docker do usługi ACR, uruchamiając następujące elementy:


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Wdrażanie przykładowego obrazu z usługi ACR do usługi AKS

Upewnij się, że masz odpowiednie poświadczenia AKS

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Utwórz plik o nazwie **acr-nginx.yaml,** który zawiera następujące elementy:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80
```

Następnie uruchom to wdrożenie w klastrze AKS:

```console
kubectl apply -f acr-nginx.yaml
```

Wdrożenie można monitorować, uruchamiając:

```console
kubectl get pods
```

Powinieneś mieć dwa uruchomione zasobniki.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
