---
title: Samouczek dotyczący rozwiązania Kubernetes na platformie Azure — wdrażanie klastra
description: Samouczek dotyczący usługi AKS — wdrażanie klastra
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c8698f16138e9baeb9c9c1142a5d0c8937a69d1b
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341403"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Samouczek: wdrażanie klastra usługi Azure Kubernetes Service (AKS)

Usługa Kubernetes zapewnia rozproszoną platformę dla konteneryzowanych aplikacji. Za pomocą usługi AKS można szybko aprowizować klaster Kubernetes gotowy do użycia w środowisku produkcyjnym. W tym samouczku (część trzecia z siedmiu) w usłudze AKS jest wdrażany klaster Kubernetes. Wykonano następujące czynności:

> [!div class="checklist"]
> * Tworzenie jednostki usługi na potrzeby interakcji z zasobami
> * Wdrażanie klastra AKS rozwiązania Kubernetes
> * Instalacja interfejsu wiersza polecenia rozwiązania Kubernetes (kubectl)
> * Konfiguracja narzędzia kubectl

W kolejnych samouczkach aplikacja Azure Vote jest wdrażana w klastrze, skalowana i aktualizowana.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach utworzono obraz kontenera i przekazano go do wystąpienia usługi Azure Container Registry. Jeśli nie wykonano tych kroków, a chcesz kontynuować pracę, wróć do części [Samouczek 1 — tworzenie obrazów kontenera][aks-tutorial-prepare-app].

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Jednostka usługi Azure Active Directory zezwala klastrowi usługi AKS na interakcje z innymi zasobami platformy Azure. Tę jednostkę usługi może automatycznie utworzyć interfejs wiersza polecenia platformy Azure lub portal. Inna możliwość to jej wstępne utworzenie i przypisanie dodatkowych uprawnień. Ten samouczek obejmuje utworzenie jednostki usługi, udzielenie dostępu do wystąpienia usługi Azure Container Registry (ACR) utworzonego w poprzednim samouczku, a następnie utworzenie klastra usługi AKS.

Tworzenie jednostki usługi za pomocą polecenia [az ad sp create-for-rbac][]. Parametr `--skip-assignment` umożliwia ograniczenie przypisywania dodatkowych uprawnień.

```azurecli
az ad sp create-for-rbac --skip-assignment
```

Dane wyjściowe są podobne do poniższego przykładu:

```
{
  "appId": "e7596ae3-6864-4cb8-94fc-20164b1588a9",
  "displayName": "azure-cli-2018-06-29-19-14-37",
  "name": "http://azure-cli-2018-06-29-19-14-37",
  "password": "52c95f25-bd1e-4314-bd31-d8112b293521",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Zwróć uwagę na wartości *appId* i *password*. Te wartości są używane w kolejnych krokach.

## <a name="configure-acr-authentication"></a>Konfigurowanie uwierzytelniania usługi ACR

Aby uzyskać dostęp do obrazów przechowywanych w usłudze ACR, należy udzielić jednostce usługi AKS uprawnień do ściągania obrazów z usługi ACR.

Najpierw pobierz identyfikator zasobu usługi ACR za pomocą polecenia [az acr show][]. Zaktualizuj wartość `<acrName>` za pomocą nazwy rejestru używanego przez wystąpienie usługi ACR i grupę zasobów, w której znajduje się wystąpienie usługi ACR.

```azurecli
az acr show --name <acrName> --resource-group myResourceGroup --query "id" --output tsv
```

Aby udzielić poprawnego dostępu do klastra usługi AKS na potrzeby używania obrazów przechowywanych w usłudze ACR, utwórz przypisanie roli za pomocą polecenia [az role assignment create][]. Zastąp ciągi `<appId`> i `<acrId>` wartościami określonymi w dwóch poprzednich krokach.

```azurecli
az role assignment create --assignee <appId> --role Reader --scope <acrId>
```

## <a name="create-kubernetes-cluster"></a>Tworzenie klastra Kubernetes

Teraz utwórz klaster usługi AKS za pomocą polecenia [az aks create][]. Poniższy przykład obejmuje tworzenie klastra o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup*. Ta grupa zasobów została utworzona w ramach [poprzedniego samouczka][aks-tutorial-prepare-acr]. Podaj własne wartości `<appId>` i `<password>` odpowiednio dla poprzedniego kroku, w którym utworzono jednostkę usługi.

```azurecli
az aks create \
    --name myAKSCluster \
    --resource-group myResourceGroup \
    --node-count 1 \
    --generate-ssh-keys \
    --service-principal <appId> \
    --client-secret <password>
```

Po kilku minutach wdrażanie zostanie zakończone i zwróci informacje o wdrożeniu usługi AKS w formacie JSON.

## <a name="install-the-kubectl-cli"></a>Instalowanie interfejsu wiersza polecenia kubectl

Aby nawiązać połączenie z klastrem Kubernetes z komputera klienckiego, należy użyć narzędzia [kubectl][kubectl], czyli klienta wiersza polecenia usługi Kubernetes.

Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie kubectl jest już zainstalowane. Istnieje także możliwość zainstalowania go lokalnie za pomocą polecenia [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Nawiązywanie połączenia przy użyciu narzędzia kubectl

Za pomocą polecenia [az aks get-credentials][] skonfiguruj narzędzie kubectl w celu nawiązania połączenia z klastrem Kubernetes. Poniższy przykład umożliwia pobranie poświadczeń dla nazwy klastra usługi AKS *myAKSCluster* w grupie zasobów *myResourceGroup*:

```azurecli
az aks get-credentials --name myAKSCluster --resource-group myResourceGroup
```

Aby sprawdzić połączenie z klastrem, uruchom polecenie [kubectl get nodes][kubectl-get].

```azurecli
kubectl get nodes
```

Dane wyjściowe:

```
NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-66427764-0   Ready     agent     9m        v1.9.6
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku w usłudze AKS został wdrożony klaster Kubernetes. Wykonano następujące czynności:

> [!div class="checklist"]
> * Tworzenie jednostki usługi na potrzeby interakcji z zasobami
> * Wdrożony klaster AKS rozwiązania Kubernetes
> * Instalacja interfejsu wiersza polecenia rozwiązania Kubernetes (kubectl)
> * Konfigurowanie narzędzia kubectl

Przejdź do następnego samouczka, aby dowiedzieć się, jak uruchomić aplikację w klastrze.

> [!div class="nextstepaction"]
> [Wdrażanie aplikacji w rozwiązaniu Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
