---
title: Samouczek Kubernetes na platformie Azure — wdrażanie klastra
description: W tym samouczku usługi Azure Kubernetes Service (AKS) można utworzyć klaster AKS i użyć polecenia kubectl do nawiązania połączenia z węzłem głównym Kubernetes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 9f06aa44824c28369b331d4079e9e81417bf17c7
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263843"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Samouczek: Wdrażanie klastra usługi Azure Kubernetes Service (AKS)

Usługa Kubernetes udostępnia rozproszoną platformę dla aplikacji kontenerowych. Za pomocą AKS można szybko utworzyć gotowy do produkcji klaster Kubernetes. W tym samouczku część trzecia z siedmiu, klaster Kubernetes jest wdrażany w AKS. Dowiesz się, jak:

> [!div class="checklist"]
> * Wdrażanie klastra Kubernetes AKS, który może być uwierzytelniany w usłudze Azure Container Registry
> * Instalowanie interfejsu wiersza polecenia Kubernetes (polecenia kubectl)
> * Konfigurowanie polecenia kubectl w celu nawiązania połączenia z klastrem AKS

W dodatkowych samouczkach aplikacja do głosowania platformy Azure jest wdrażana w klastrze, skalowanym i aktualizowanym.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach obraz kontenera został utworzony i przekazany do wystąpienia Azure Container Registry. Jeśli nie wykonano tych kroków, a chcesz skorzystać z [samouczka 1 — Tworzenie obrazów kontenerów][aks-tutorial-prepare-app].

Ten samouczek wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.53 lub nowszej. Uruchom `az --version`, aby znaleźć wersję. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Tworzenie klastra Kubernetes

Klastry AKS mogą używać kontroli dostępu opartej na rolach Kubernetes (RBAC). Te kontrolki pozwalają definiować dostęp do zasobów na podstawie ról przypisanych do użytkowników. Uprawnienia są łączone, jeśli użytkownik ma przypisane wiele ról, a uprawnienia mogą być ograniczone do pojedynczej przestrzeni nazw lub całego klastra. Domyślnie interfejs wiersza polecenia platformy Azure automatycznie włącza RBAC podczas tworzenia klastra AKS.

Utwórz klaster AKS za pomocą polecenia [AZ AKS Create][]. W poniższym przykładzie tworzony jest klaster o nazwie *myAKSCluster* w grupie zasobów o nazwie Moja *resourceName*. Ta grupa zasobów została utworzona w [poprzednim samouczku][aks-tutorial-prepare-acr]. Aby umożliwić klastrowi AKS współdziałanie z innymi zasobami platformy Azure, zostanie automatycznie utworzona jednostka usługi Azure Active Directory, ponieważ nie została ona określona. W tym miejscu ta jednostka usługi ma uprawnienia [do ściągania obrazów][container-registry-integration] z wystąpienia Azure Container Registry (ACR) utworzonego w poprzednim samouczku.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Po kilku minutach wdrożenie zostanie ukończone i zwróci informacje w formacie JSON o wdrożeniu AKS.

> [!NOTE]
> Aby zapewnić niezawodne działanie klastra, należy uruchomić co najmniej 2 (dwa) węzły.

## <a name="install-the-kubernetes-cli"></a>Instalowanie interfejsu wiersza polecenia Kubernetes

Aby nawiązać połączenie z klastrem Kubernetes z komputera lokalnego, należy użyć [polecenia kubectl][kubectl], klienta wiersza polecenia Kubernetes.

Jeśli używasz Azure Cloud Shell, `kubectl` jest już zainstalowany. Można go również zainstalować lokalnie za pomocą polecenia [AZ AKS Install-CLI][] :

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Nawiązywanie połączenia z klastrem za pomocą polecenia kubectl

Aby skonfigurować `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [AZ AKS Get-Credentials][] . Poniższy przykład pobiera poświadczenia dla klastra AKS o nazwie *myAKSCluster* w liście *zasobów*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby sprawdzić połączenie z klastrem, uruchom polecenie [polecenia kubectl Get nodes][kubectl-get] :

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.13.10
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku klaster Kubernetes został wdrożony w AKS i skonfigurowano `kubectl` w celu nawiązania z nim połączenia. Wiesz już, jak:

> [!div class="checklist"]
> * Wdrażanie klastra Kubernetes AKS, który może być uwierzytelniany w usłudze Azure Container Registry
> * Instalowanie interfejsu wiersza polecenia Kubernetes (polecenia kubectl)
> * Konfigurowanie polecenia kubectl w celu nawiązania połączenia z klastrem AKS

Przejdź do następnego samouczka, aby dowiedzieć się, jak wdrożyć aplikację w klastrze.

> [!div class="nextstepaction"]
> [Wdrażanie aplikacji w Kubernetes][aks-tutorial-deploy-app]

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
[AZ AKS Create]: /cli/azure/aks#az-aks-create
[AZ AKS Install-CLI]: /cli/azure/aks#az-aks-install-cli
[AZ AKS Get-Credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
