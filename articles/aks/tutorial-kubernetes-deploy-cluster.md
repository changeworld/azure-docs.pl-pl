---
title: Samouczek dotyczący usługi Kubernetes na platformie Azure — wdrażanie klastra
description: W tym samouczku dotyczącym usługi Azure Kubernetes Service (AKS) utworzysz klaster usługi AKS i nawiążesz połączenie z węzłem głównym usługi Kubernetes za pomocą narzędzia kubectl.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 302947ae9e952c0ceb37d9ddbba5ccd3c33c53a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031075"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Samouczek: Wdrażanie klastra usługi Azure Kubernetes Service (AKS)

Usługa Kubernetes zapewnia rozproszoną platformę dla konteneryzowanych aplikacji. Za pomocą usługi AKS można szybko utworzyć klaster Kubernetes gotowy do użycia w środowisku produkcyjnym. W tym samouczku (część trzecia z siedmiu) w usłudze AKS jest wdrażany klaster Kubernetes. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie jednostki usługi na potrzeby interakcji z zasobami
> * Wdrażanie klastra AKS rozwiązania Kubernetes
> * Instalowanie interfejsu wiersza polecenia rozwiązania Kubernetes (kubectl)
> * Konfigurowanie narzędzia kubectl w celu nawiązania połączenia z klastrem AKS

W dodatkowych samouczkach aplikacja Azure Vote jest wdrażana w klastrze, skalowana i aktualizowana.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach utworzono obraz kontenera i przekazano go do wystąpienia usługi Azure Container Registry. Jeśli nie wykonano tych kroków, a chcesz kontynuować pracę, zacznij od części [Samouczek 1 — tworzenie obrazów kontenera][aks-tutorial-prepare-app].

Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.53 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Jednostka usługi Azure Active Directory zezwala klastrowi usługi AKS na interakcje z innymi zasobami platformy Azure. Tę jednostkę usługi może automatycznie utworzyć interfejs wiersza polecenia platformy Azure lub portal. Inna możliwość to jej wstępne utworzenie i przypisanie dodatkowych uprawnień. Ten samouczek obejmuje utworzenie jednostki usługi, udzielenie dostępu do wystąpienia usługi Azure Container Registry (ACR) utworzonego w poprzednim samouczku, a następnie utworzenie klastra usługi AKS.

Utwórz jednostkę usługi za pomocą polecenia [az ad sp create-for-rbac][]. Parametr `--skip-assignment` umożliwia ograniczenie przypisywania dodatkowych uprawnień. Domyślnie ta jednostka usługi jest ważna przez rok.

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
az acr show --resource-group myResourceGroup --name <acrName> --query "id" --output tsv
```

Aby udzielić poprawnego dostępu do klastra usługi AKS na potrzeby używania obrazów przechowywanych w usłudze ACR, przypisz rolę `AcrPull` za pomocą polecenia [az role assignment create][]. Zastąp ciągi `<appId`> i `<acrId>` wartościami określonymi w dwóch poprzednich krokach.

```azurecli
az role assignment create --assignee <appId> --scope <acrId> --role acrpull
```

## <a name="create-a-kubernetes-cluster"></a>Tworzenie klastra Kubernetes

Klastry usługi AKS mogą używać kontroli dostępu opartej na rolach (RBAC) rozwiązania Kubernetes. Te kontrolki umożliwiają zdefiniowanie dostępu do zasobów na podstawie ról przypisanych użytkownikom. Uprawnienia są łączone, jeśli użytkownikowi przypisano wiele ról, a zakres uprawnień można ograniczyć do jednej przestrzeni nazw lub do całego klastra. Domyślnie interfejs wiersza polecenia platformy Azure automatycznie włącza kontrolę dostępu opartą na rolach podczas tworzenia klastra usługi AKS.

Utwórz klaster usługi AKS za pomocą polecenia [az aks create][]. W poniższym przykładzie tworzony jest klaster o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup*. Ta grupa zasobów została utworzona w ramach [poprzedniego samouczka][aks-tutorial-prepare-acr]. Podaj własne wartości `<appId>` i `<password>` z poprzedniego kroku, w którym utworzono jednostkę usługi.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --service-principal <appId> \
    --client-secret <password> \
    --generate-ssh-keys
```

Po kilku minutach wdrażanie zostanie zakończone i zwróci informacje o wdrożeniu usługi AKS w formacie JSON.

## <a name="install-the-kubernetes-cli"></a>Instalowanie interfejsu wiersza polecenia rozwiązania Kubernetes

Aby nawiązać połączenie z klastrem Kubernetes z komputera lokalnego, należy użyć narzędzia [kubectl][kubectl], czyli klienta wiersza polecenia usługi Kubernetes.

Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Możesz także zainstalować je lokalnie za pomocą polecenia [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Nawiązywanie połączenia z klastrem przy użyciu narzędzia kubectl

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][]. Poniższy przykład umożliwia pobranie poświadczeń dla nazwy klastra usługi AKS *myAKSCluster* w grupie zasobów *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby sprawdzić połączenie z klastrem, uruchom polecenie [kubectl get nodes][kubectl-get]:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-28993262-0   Ready    agent   3m18s   v1.9.11
```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku wdrożono klaster Kubernetes w usłudze AKS i skonfigurowano narzędzie `kubectl` w celu nawiązania z nim połączenia. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie jednostki usługi na potrzeby interakcji z zasobami
> * Wdrażanie klastra AKS rozwiązania Kubernetes
> * Instalowanie interfejsu wiersza polecenia rozwiązania Kubernetes (kubectl)
> * Konfigurowanie narzędzia kubectl w celu nawiązania połączenia z klastrem AKS

Przejdź do następnego samouczka, aby dowiedzieć się, jak wdrożyć aplikację w klastrze.

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
[azure-cli-install]: /cli/azure/install-azure-cli
