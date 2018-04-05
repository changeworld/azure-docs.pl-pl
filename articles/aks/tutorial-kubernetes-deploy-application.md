---
title: Samouczek dotyczący usługi Kubernetes na platformie Azure — wdrażanie aplikacji
description: Samouczek dotyczący usługi AKS — wdrażanie aplikacji
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 9fad9627be453fb00508d1f6ec9594bd2bde6fad
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-run-applications-in-azure-container-service-aks"></a>Samouczek: uruchamianie aplikacji w usłudze Azure Container Service (AKS)

W tym samouczku (część czwarta z ośmiu) przykładowa aplikacja jest wdrażana w klastrze Kubernetes. Wykonano następujące czynności:

> [!div class="checklist"]
> * Aktualizowanie plików manifestu Kubernetes
> * Uruchamianie aplikacji w usłudze Kubernetes
> * Testowanie aplikacji

W kolejnych samouczkach ta aplikacja zostanie przeskalowana, zaktualizowana, a usługa Log Analytics zostanie skonfigurowana do monitorowania klastra Kubernetes.

Na potrzeby tego samouczka założono, że masz podstawową wiedzę na temat pojęć związanych z rozwiązaniem Kubernetes. Aby uzyskać szczegółowe informacje na jego temat, zapoznaj się z [dokumentacją rozwiązania Kubernetes][kubernetes-documentation].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach aplikacja została spakowana w postaci obrazu kontenera, obraz został przekazany do usługi Azure Container Registry i utworzono klaster usługi Kubernetes. 

Do ukończenia tego samouczka potrzebujesz wstępnie utworzonego pliku manifestu usługi Kubernetes `azure-vote-all-in-one-redis.yaml`. Ten plik został pobrany z kodu źródłowego aplikacji w poprzednim samouczku. Sprawdź, czy został utworzony klon repozytorium oraz czy katalogi zostały zmienione na sklonowane repozytorium.

Jeśli nie wykonano tych kroków, a chcesz kontynuować pracę, wróć do części [Samouczek 1 — tworzenie obrazów kontenera][aks-tutorial-prepare-app].

## <a name="update-manifest-file"></a>Aktualizowanie pliku manifestu

W tym samouczku użyto usługi Azure Container Registry (ACR) do przechowywania obrazu kontenera. Przed uruchomieniem aplikacji należy zaktualizować nazwę serwera logowania usługi ACR w pliku manifestu usługi Kubernetes.

Pobierz nazwę serwera logowania usługi ACR przy użyciu polecenia [az acr list][az-acr-list].

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Plik manifestu został wstępnie utworzony przy użyciu nazwy serwera logowania `microsoft`. Otwórz plik w dowolnym edytorze tekstu. W tym przykładzie plik jest otwierany przy użyciu narzędzia `nano`.

```console
nano azure-vote-all-in-one-redis.yaml
```

Zastąp element `microsoft` nazwą serwera logowania usługi ACR. Tę wartość można znaleźć w wierszu **47** pliku manifestu.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Powyższy kod przyjmuje postać taką jak poniżej.

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Zapisz i zamknij plik.

## <a name="deploy-application"></a>Wdrażanie aplikacji

Użyj polecenia [kubectl create][kubectl-create], aby uruchomić aplikację. To polecenie analizuje plik manifestu i tworzy zdefiniowane obiekty usługi Kubernetes.

```azurecli
kubectl create -f azure-vote-all-in-one-redis.yaml
```

Dane wyjściowe:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Testowanie aplikacji

Utworzenie [usługi Kubernetes][kubernetes-service] powoduje uwidocznienie aplikacji w Internecie. Ten proces może potrwać kilka minut. 

Aby monitorować postęp, użyj polecenia [kubectl get-service][kubectl-get] z argumentem `--watch`.

```azurecli
kubectl get service azure-vote-front --watch
```

Początkowo adres *EXTERNAL-IP* dla usługi *azure-vote-front* pojawia się jako *oczekujący*.
  
```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Po zmianie adresu *EXTERNAL-IP* z *oczekującego* na *adres IP*, zatrzymaj proces śledzenia narzędzia kubectl za pomocą polecenia `CTRL-C`. 

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Aby wyświetlić aplikację, przejdź do zewnętrznego adresu IP.

![Obraz przedstawiający klaster Kubernetes na platformie Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

Jeśli aplikacja się nie załadowała, może być to spowodowane problemem z autoryzacją rejestru obrazów.

Wykonaj następujące czynności, aby [zezwolić na dostęp za pośrednictwem wpisu tajnego rozwiązania Kubernetes](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret).

## <a name="next-steps"></a>Następne kroki

W tym samouczku aplikacja do głosowania na platformie Azure została wdrożona w klastrze Kubernetes w usłudze AKS. Wykonasz następujące zadania:  

> [!div class="checklist"]
> * Pobieranie plików manifestu Kubernetes
> * Uruchamianie aplikacji w usłudze Kubernetes
> * Testowanie aplikacji

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat skalowania aplikacji Kubernetes i bazowej infrastruktury usługi Kubernetes. 

> [!div class="nextstepaction"]
> [Skalowanie aplikacji i infrastruktury rozwiązania Kubernetes][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr#list
