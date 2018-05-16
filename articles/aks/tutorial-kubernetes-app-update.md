---
title: Samouczek dotyczący usługi Kubernetes na platformie Azure — aktualizowanie aplikacji
description: Samouczek dotyczący usługi AKS — aktualizowanie aplikacji
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 622cd17a93bf1b9fa9d3c138d385ca1d29426f3b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Samouczek: aktualizowanie aplikacji w usłudze Azure Kubernetes Service (AKS)

Po wdrożeniu aplikacji w usłudze Kubernetes można ją zmodyfikować, określając nowy obraz kontenera lub wersję obrazu. W takiej sytuacji aktualizacja jest przygotowywana tak, aby tylko część była współbieżnie aktualizowana. Ta aktualizacja etapowa umożliwia kontynuowanie działania podczas aktualizacji aplikacji. Udostępnia ona również mechanizm wycofywania w przypadku niepowodzenia wdrożenia.

W tym samouczku (część szósta z ośmiu) aktualizowana jest przykładowa aplikacja do głosowania platformy Azure. Zadania do wykonania to na przykład:

> [!div class="checklist"]
> * Aktualizowanie kodu aplikacji frontonu
> * Tworzenie zaktualizowanego obrazu kontenera
> * Wypychanie obrazu kontenera do usługi Azure Container Registry
> * Wdrażanie zaktualizowanego obrazu kontenera

W kolejnych samouczkach usługa Log Analytics zostanie skonfigurowana do monitorowania klastra usługi Kubernetes.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach aplikacja została spakowana w postaci obrazu kontenera, obraz został przekazany do usługi Azure Container Registry i utworzono klaster usługi Kubernetes. Następnie uruchomiono aplikację w klastrze usługi Kubernetes.

Sklonowano również repozytorium aplikacji, w tym kod źródłowy aplikacji i utworzony wcześniej plik narzędzia Docker Compose używany w tym samouczku. Sprawdź, czy został utworzony klon repozytorium oraz czy katalogi zostały zmienione na sklonowany katalog. Zawiera on katalog o nazwie `azure-vote` i plik o nazwie `docker-compose.yaml`.

Jeśli nie wykonano tych kroków, a chcesz kontynuować pracę, wróć do części [Samouczek 1 — tworzenie obrazów kontenera][aks-tutorial-prepare-app].

## <a name="update-application"></a>Aktualizowanie aplikacji

W tym samouczku wprowadzana jest zmiana aplikacji, a zaktualizowana aplikacja jest wdrażana w klastrze usługi Kubernetes.

Kod źródłowy aplikacji znajduje się wewnątrz katalogu `azure-vote`. Otwórz plik `config_file.cfg` w dowolnym edytorze kodu lub tekstu. W tym przykładzie używamy programu `vi`.

```console
vi azure-vote/azure-vote/config_file.cfg
```

Zmień wartości elementów `VOTE1VALUE` i `VOTE2VALUE`, a następnie zapisz plik.

```console
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Zapisz i zamknij plik.

## <a name="update-container-image"></a>Aktualizowanie obrazu kontenera

Użyj narzędzia [docker-compose][docker-compose], aby ponownie utworzyć obraz frontonu i uruchomić zaktualizowaną aplikację. Argument `--build` jest używany w celu poinstruowania narzędzia Docker Compose o konieczności ponownego utworzenia obrazu aplikacji.

```console
docker-compose up --build -d
```

## <a name="test-application-locally"></a>Testowanie aplikacji w środowisku lokalnym

Przejdź do adresu http://localhost:8080, aby wyświetlić zaktualizowaną aplikację.

![Obraz przedstawiający klaster Kubernetes na platformie Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Tagowanie i wypychanie obrazów

Otaguj obraz `azure-vote-front` wartością loginServer rejestru kontenerów.

Pobierz nazwę serwera logowania przy użyciu polecenia [az acr list](/cli/azure/acr#az_acr_list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Użyj polecenia [docker tag][docker-tag] w celu otagowania obrazu. Zastąp element `<acrLoginServer>` nazwą serwera logowania usługi Azure Container Registry lub nazwą hosta rejestru publicznego. Zwróć również uwagę, że obraz został zaktualizowany do wersji `v2`.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

Użyj polecenia [docker push][docker-push] w celu przekazania obrazu do rejestru. Zastąp element `<acrLoginServer>` nazwą serwera logowania usługi Azure Container Registry. Jeśli wystąpią problemy związane z wypychaniem do rejestru usługi ACR, upewnij się, że uruchomiono polecenie [az acr login][az-acr-login].

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-update-application"></a>Wdrażanie zaktualizowanej aplikacji

Aby zapewnić maksymalny czas działania, należy uruchomić wiele wystąpień zasobnika aplikacji. Sprawdź tę konfigurację przy użyciu polecenia [kubectl get pod][kubectl-get].

```
kubectl get pod
```

Dane wyjściowe:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Jeśli obraz azure-vote-front nie został uruchomiony w wielu zasobnikach, skaluj wdrożenie `azure-vote-front`.


```azurecli
kubectl scale --replicas=3 deployment/azure-vote-front
```

Aby zaktualizować aplikację, użyj polecenia [kubectl set][kubectl-set]. Zaktualizuj element `<acrLoginServer>` przy użyciu nazwy serwera logowania lub nazwy hosta rejestru kontenerów.

```azurecli
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Aby monitorować wdrożenie, użyj polecenia [kubectl get pod][kubectl-get]. Podczas wdrażania aplikacji działanie zasobników jest przerywane, a następnie są one ponownie tworzone przy użyciu obrazu kontenera.

```azurecli
kubectl get pod
```

Dane wyjściowe:

```
NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-updated-application"></a>Testowanie zaktualizowanej aktualizacji

Pobierz zewnętrzny adres IP usługi `azure-vote-front`.

```azurecli
kubectl get service azure-vote-front
```

Przejdź do adresu IP, aby wyświetlić zaktualizowaną aplikację.

![Obraz przedstawiający klaster Kubernetes na platformie Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku zaktualizowano aplikację i wydano tę aktualizację do klastra Kubernetes. Wykonano następujące zadania:

> [!div class="checklist"]
> * Zaktualizowano kod aplikacji frontonu
> * Utworzono zaktualizowany obraz kontenera
> * Wypchnięto obraz kontenera do usługi Azure Container Registry
> * Wdrożono zaktualizowaną aplikację

Przejdź do następnego samouczka, aby dowiedzieć się, jak uaktualnić usługę Kubernetes do nowej wersji.

> [!div class="nextstepaction"]
> [Upgrade Kubernetes (Uaktualnianie usługi Kubernetes)][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az_acr_login