---
title: Samouczek dotyczący usługi Kubernetes na platformie Azure — przygotowywanie rejestru Azure Container Registry
description: Samouczek dotyczący środowiska AKS — przygotowywanie rejestru Azure Container Registry
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e72ec06f7c682e68ca94ba5d4044d1d9974b0b24
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101043"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Samouczek: wdrażanie usługi Azure Container Registry i korzystanie z niej

Usługa Azure Container Registry to oparty na platformie Azure rejestr prywatny na potrzeby obrazów kontenerów platformy Docker. Ten samouczek (część druga z siedmiu) zawiera opis sposobu wdrażania wystąpienia usługi Azure Container Registry i wypychania do niego obrazu kontenera. Wykonano następujące czynności:

> [!div class="checklist"]
> * Wdrażanie wystąpienia usługi Azure Container Registry
> * Tagowanie obrazu kontenera na potrzeby rejestru Azure Container Registry
> * Przekazywanie obrazu do rejestru Azure Container Registry

W kolejnych samouczkach to wystąpienie rejestru Azure Container Registry zostanie zintegrowane z klastrem Kubernetes w środowisku AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W [poprzednim samouczku ][aks-tutorial-prepare-app] utworzono obraz kontenera na potrzeby prostej aplikacji do głosowania platformy Azure. Jeśli obraz aplikacji do głosowania platformy Azure nie został utworzony, wróć do artykułu [Samouczek 1 — Tworzenie obrazów kontenerów][aks-tutorial-prepare-app].

Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="deploy-azure-container-registry"></a>Wdrażanie usługi Azure Container Registry

W przypadku wdrażania usługi Azure Container Registry należy najpierw posiadać grupę zasobów. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create]. W tym przykładzie grupa zasobów o nazwie `myResourceGroup` zostanie utworzona w regionie `eastus`.

```azurecli
az group create --name myResourceGroup --location eastus
```

Utwórz usługę Azure Container Registry za pomocą polecenia [az acr create][az-acr-create]. Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

W dalszej części tego samouczka wartość `<acrName>` zostanie użyta jako symbol zastępczy nazwy rejestru kontenerów.

## <a name="container-registry-login"></a>Logowanie do rejestru Container Registry

Użyj polecenia [az acr login][az-acr-login], aby zalogować się do wystąpienia rejestru Azure Container Registry. Należy podać unikatową nazwę nadaną kontenerowi podczas jego tworzenia.

```azurecli
az acr login --name <acrName>
```

Polecenie zwraca komunikat „Logowanie pomyślne” po ukończeniu.

## <a name="tag-container-images"></a>Tagowanie obrazów kontenerów

Aby wyświetlić listę bieżących obrazów, użyj polecenia [docker images][docker-images].

```console
docker images
```

Dane wyjściowe:

```
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Każdy obraz kontenera należy otagować za pomocą nazwy loginServer rejestru. Ten tag jest używany na potrzeby kierowania podczas wypychania obrazów kontenerów do rejestru obrazów.

Użyj polecenia [az acr list][az-acr-list], aby uzyskać nazwę loginServer.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Następnie otaguj obraz `azure-vote-front` wartością loginServer rejestru kontenerów. Ponadto dodaj wartość `:v1` na końcu nazwy obrazu. Ten tag wskazuje wersję obrazu.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Po otagowaniu uruchom polecenie [docker images][docker-images], aby zweryfikować operację.

```console
docker images
```

Dane wyjściowe:

```
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Wypychanie obrazów do rejestru

Wypchnij obraz `azure-vote-front` do rejestru.

Korzystając z następującego przykładu, zastąp nazwę loginServer usługi ACR nazwą loginServer z używanego środowiska.

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

Wykonanie tej operacji może zająć kilka minut.

## <a name="list-images-in-registry"></a>Wyświetlanie listy obrazów w rejestrze

Aby zwrócić listę obrazów, które zostały wypchnięte do usługi Azure Container Registry, użyj polecenia [az acr repository list][az-acr-repository-list]. Zaktualizuj polecenie nazwą wystąpienia usługi ACR.

```azurecli
az acr repository list --name <acrName> --output table
```

Dane wyjściowe:

```azurecli
Result
----------------
azure-vote-front
```

Aby następnie wyświetlić tagi dla określonego obrazu, użyj polecenia [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Dane wyjściowe:

```azurecli
Result
--------
v1
```

Po ukończeniu tego samouczka obraz kontenera zostanie zapisany w prywatnym wystąpieniu usługi Azure Container Registry. W kolejnych samouczkach ten obraz zostanie wdrożony z rejestru Azure Container Registry do klastra Kubernetes.

## <a name="next-steps"></a>Następne kroki

W tym samouczku usługa Azure Container Registry została przygotowana do użycia w klastrze AKS. Wykonano następujące czynności:

> [!div class="checklist"]
> * Wdrażanie wystąpienia usługi Azure Container Registry
> * Tagowanie obrazu kontenera na potrzeby rejestru Azure Container Registry
> * Przekazywanie obrazu do rejestru Azure Container Registry

Przejdź do kolejnego samouczka, aby uzyskać więcej informacji o wdrażaniu klastra Kubernetes na platformie Azure.

> [!div class="nextstepaction"]
> [Wdrażanie klastra Kubernetes][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#create
[az-acr-list]: /cli/azure/acr#list
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az_acr_login
[az-acr-list]: https://docs.microsoft.com/cli/azure/acr#az_acr_list
[az-acr-repository-list]: /cli/azure/acr/repository#list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#show-tags
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
