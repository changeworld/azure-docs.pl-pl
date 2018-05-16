---
title: Samouczek dotyczący rozwiązania Kubernetes na platformie Azure — wdrażanie klastra
description: Samouczek dotyczący usługi AKS — wdrażanie klastra
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c793aa02e614ead146806888d26a18867ff2eebb
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Samouczek: wdrażanie klastra usługi Azure Kubernetes Service (AKS)

Usługa Kubernetes zapewnia rozproszoną platformę dla konteneryzowanych aplikacji. Usługa AKS umożliwia łatwe i szybkie aprowizowanie produkcyjnego klastra Kubernetes. W tym samouczku (część trzecia z ośmiu) w usłudze AKS jest wdrażany klaster Kubernetes. Wykonano następujące czynności:

> [!div class="checklist"]
> * Wdrażanie klastra AKS rozwiązania Kubernetes
> * Instalacja interfejsu wiersza polecenia rozwiązania Kubernetes (kubectl)
> * Konfiguracja narzędzia kubectl

W kolejnych samouczkach aplikacja do głosowania platformy Azure będzie wdrażana w klastrze, skalowana i aktualizowana, a usługa Log Analytics zostanie skonfigurowana do monitorowania klastra usługi Kubernetes.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach utworzono obraz kontenera i przekazano go do wystąpienia usługi Azure Container Registry. Jeśli nie wykonano tych kroków, a chcesz kontynuować pracę, wróć do części [Samouczek 1 — tworzenie obrazów kontenera][aks-tutorial-prepare-app].

## <a name="enable-aks-preview"></a>Włącz podgląd usługi AKS

W przypadku usługi AKS w wersji zapoznawczej tworzenie nowych klastrów wymaga flagi funkcji w ramach subskrypcji. Możesz zażądać tej funkcji dla dowolnej liczby subskrypcji, z których chcesz skorzystać. Użyj polecenia `az provider register`, aby zarejestrować dostawcę usług AKS:

```azurecli
az provider register -n Microsoft.ContainerService
```

Po zarejestrowaniu możesz przystąpić do tworzenia klastra Kubernetes za pomocą usługi AKS.

## <a name="create-kubernetes-cluster"></a>Tworzenie klastra Kubernetes

Poniższy przykład obejmuje tworzenie klastra o nazwie `myAKSCluster` w grupie zasobów o nazwie `myResourceGroup`. Ta grupa zasobów została utworzona w ramach [poprzedniego samouczka][aks-tutorial-prepare-acr].

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

Po kilku minutach wdrażanie zostanie zakończone i zwróci informacje o wdrożeniu usługi AKS w formacie JSON.

## <a name="install-the-kubectl-cli"></a>Instalowanie interfejsu wiersza polecenia kubectl

Aby nawiązać połączenie z klastrem Kubernetes z komputera klienckiego, należy użyć narzędzia [kubectl][kubectl], czyli klienta wiersza polecenia usługi Kubernetes.

Jeśli korzystasz z usługi Azure CloudShell, narzędzie kubectl jest już zainstalowane. Jeśli chcesz zainstalować je lokalnie, uruchom następujące polecenie:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Nawiązywanie połączenia przy użyciu narzędzia kubectl

Aby skonfigurować narzędzie kubectl w celu łączenia się z klastrem Kubernetes, uruchom następujące polecenie:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby sprawdzić połączenie z klastrem, uruchom polecenie [kubectl get nodes][kubectl-get].

```azurecli
kubectl get nodes
```

Dane wyjściowe:

```
NAME                          STATUS    AGE       VERSION
k8s-myAKSCluster-36346190-0   Ready     49m       v1.7.9
```

Po ukończeniu samouczka klaster AKS jest gotowy do użycia z obciążeniami. W kolejnych samouczkach aplikacja wielu kontenerów jest wdrażana do tego klastra, skalowana w poziomie, aktualizowana i monitorowana.

## <a name="configure-acr-authentication"></a>Konfigurowanie uwierzytelniania usługi ACR

Konieczne jest skonfigurowanie uwierzytelniania między klastrem AKS i rejestrem ACR. Obejmuje to udzielenie tożsamości AKS odpowiednich uprawnień do ściągania obrazów z rejestru ACR.

Najpierw pobierz identyfikator jednostki usługi skonfigurowanej na potrzeby usług AKS. Zaktualizuj nazwę grupy zasobów i nazwę klastra AKS w taki sposób, aby była zgodna z Twoim środowiskiem.

```azurecli
CLIENT_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId" --output tsv)
```

Pobierz identyfikator zasobu rejestru ACR. Zaktualizuj nazwę rejestru na nazwę używanego rejestru ACR, a grupę zasobów na nazwę grupy zasobów, w której znajduje się w rejestr ACR.

```azurecli
ACR_ID=$(az acr show --name <acrName> --resource-group myResourceGroup --query "id" --output tsv)
```

Utwórz przypisanie roli, które spowoduje udzielenie odpowiedniego dostępu.

```azurecli
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku w usłudze AKS został wdrożony klaster Kubernetes. Wykonano następujące czynności:

> [!div class="checklist"]
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
