---
title: OpenFaaS za pomocą usługi Azure Kubernetes (AKS)
description: Wdrażanie i użytkowanie OpenFaaS z usługi Kubernetes Azure (AKS)
services: container-service
author: justindavies
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: 778fa5ddcdf8006d28c092746e4ac17a497baa5f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="using-openfaas-on-aks"></a>Za pomocą OpenFaaS na AKS

[OpenFaaS] [ open-faas] to struktura umożliwiająca tworzenie Niekorzystającą funkcje na górze kontenerów. Jako projekt typu Open Source zyskały przyjęcia na dużą skalę w ramach społeczności. Ten dokument zawiera szczegóły dotyczące instalowania i używania OpenFaas w klastrze usługi Kubernetes Azure (AKS).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące elementy.

* Podstawową wiedzę na temat Kubernetes.
* Klaster usługi Kubernetes Azure (AKS) i poświadczenia AKS skonfigurowany w systemie deweloperskim.
* Azure CLI zainstalowane w systemie deweloperskim.
* Narzędzia wiersza polecenia Git zainstalowanych w systemie.

## <a name="get-openfaas"></a>Pobierz OpenFaaS

Klonowanie repozytorium OpenFaaS projektu na platformie programistycznej.

```azurecli-interactive
git clone https://github.com/openfaas/faas-netes
```

Zmień do katalogu sklonowanego repozytorium.

```azurecli-interactive
cd faas-netes
```

## <a name="deploy-openfaas"></a>Wdrażanie OpenFaaS

Dobrym rozwiązaniem, OpenFaaS i OpenFaaS funkcji powinny być przechowywane w ich własnych Kubernetes przestrzeni nazw.

Tworzenie przestrzeni nazw systemu OpenFaaS.

```azurecli-interactive
kubectl create namespace openfaas
```

Utworzyć drugi przestrzeń nazw dla funkcji OpenFaaS.

```azurecli-interactive
kubectl create namespace openfaas-fn
```

Wykres Helm OpenFaaS znajduje się w sklonowanego repozytorium. Ten wykres umożliwia wdrażanie OpenFaaS w klastrze AKS.

```azurecli-interactive
helm install --namespace openfaas -n openfaas \
  --set functionNamespace=openfaas-fn, \
  --set serviceType=LoadBalancer, \
  --set rbac=false chart/openfaas/
```

Dane wyjściowe:

```
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

  kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

Uzyskiwanie dostępu do bramy OpenFaaS jest tworzony publiczny adres IP. Aby uzyskać dostęp do tego adresu IP, należy użyć [kubectl pobrać usługi] [ kubectl-get] polecenia. Może upłynąć kilka minut dla adresu IP, który ma być przypisany do usługi.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Dane wyjściowe.

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Aby przetestować OpenFaaS system, przejdź do zewnętrznego adresu IP, portu 8080, `http://52.186.64.52:8080` w tym przykładzie.

![OpenFaaS interfejsu użytkownika](media/container-service-serverless/openfaas.png)

Na koniec należy zainstalować OpenFaaS interfejsu wiersza polecenia. Ta exmaple używane brew, zobacz [dokumentacji interfejsu wiersza polecenia OpenFaaS] [ open-faas-cli] wyświetlić więcej opcji.

```console
brew install faas-cli
```

## <a name="create-first-function"></a>Tworzenie pierwszej funkcji

Teraz, działa OpenFaaS Tworzenie funkcji przy użyciu portalu OpenFaas.

Polecenie **wdrożyć nową funkcję** i wyszukaj **Figlet**. Wybierz funkcję Figlet, a następnie kliknij przycisk **Wdróż**.

![Figlet](media/container-service-serverless/figlet.png)

Użyj curl do wywołania funkcji. Zamień na adres IP w poniższym przykładzie który OpenFaas bramy.

```azurecli-interactive
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Dane wyjściowe:

```console
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Utworzyć drugi — funkcja

Teraz Utwórz funkcję drugiego. W tym przykładzie zostanie wdrożony przy użyciu interfejsu wiersza polecenia OpenFaaS i zawiera obraz niestandardowy kontenera i pobierania danych z bazy danych rozwiązania Cosmos. Kilka elementów muszą być skonfigurowana przed utworzeniem funkcji.

Najpierw utwórz nową grupę zasobów dla rozwiązania Cosmos bazy danych.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Wdróż wystąpienie CosmosDB rodzaju `MongoDB`. Wystąpienie musi unikatową nazwę, zaktualizuj `openfaas-cosmos` unikatowym dla danego środowiska.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Pobierz ciąg połączenia bazy danych rozwiązania Cosmos i zapisze go w zmiennej.

Zaktualizuj wartość `--resource-group` argument nazwę grupy zasobów oraz `--name` argumentu na nazwę Twojego rozwiązania Cosmos bazy danych.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Teraz wypełnić rozwiązania Cosmos bazę danych z danych testowych. Utwórz plik o nazwie `plans.json` i skopiuj następujący kod json.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

Użyj *mongoimport* narzędzia na załadowanie wystąpienia CosmosDB z danymi.

Jeśli to konieczne, zainstaluj narzędzia bazy danych MongoDB. W poniższym przykładzie instalowana te narzędzia, za pomocą brew, zobacz [dokumentacją usługi MongoDB] [ install-mongo] innych opcji.

```azurecli-interactive
brew install mongodb
```

Załaduj dane do bazy danych.

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

Dane wyjściowe:

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Uruchom następujące polecenie, aby utworzyć funkcji. Zaktualizuj wartość `-g` argument przy użyciu adresu bramy OpenFaaS.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Po wdrożeniu, powinna zostać wyświetlona nowo utworzony OpenFaaS punktu końcowego dla tej funkcji.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Testowanie funkcji przy użyciu programu curl. Zaktualizuj adres IP z OpenFaaS adres bramy.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Dane wyjściowe:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Można również sprawdzić funkcji w Interfejsie użytkownika OpenFaaS.

![tekst alternatywny](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Następne kroki

Domyślnym wdrożeniu programu OpenFaas musi być zablokowany dla bramy OpenFaaS i funkcje. [Wpis w blogu Ellis Alexowi](https://blog.alexellis.io/lock-down-openfaas/) zawiera dodatkowe informacje na temat opcji konfiguracji bezpiecznego.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli