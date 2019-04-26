---
title: Usługi OpenFaaS za pomocą usługi Azure Kubernetes Service (AKS)
description: Wdrażanie i używanie usługi OpenFaaS z usługi Azure Kubernetes Service (AKS)
services: container-service
author: justindavies
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: 5ed6e0b21b00ede3f78a102fd004e5706ae3cea5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60464886"
---
# <a name="using-openfaas-on-aks"></a>Używanie usługi OpenFaaS w usłudze AKS

[Usługi OpenFaaS] [ open-faas] to architektura służąca do tworzenia funkcje niewymagające użycia serwera za pomocą kontenerów. Jako projekt typu open source zdobyła wdrożenia na dużą skalę w społeczności. W tym dokumencie przedstawiono Instalowanie i używanie usługi OpenFaas w klastrze usługi Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące elementy.

* Podstawową wiedzę na temat usługi Kubernetes.
* Klaster Azure Kubernetes Service (AKS) i skonfigurowane w systemie deweloperskim poświadczenia usługi AKS.
* Wiersza polecenia platformy Azure zainstalowany w systemie deweloperskim.
* Narzędzia wiersza polecenia Git zainstalowana w systemie.

## <a name="add-the-openfaas-helm-chart-repo"></a>Dodaj repozytorium usługi OpenFaaS wykresu helm

Usługi OpenFaaS przechowuje własnego wykresów rozwiązania helm, aby na bieżąco z najnowszymi zmianami.

```azurecli-interactive
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>Deploy OpenFaaS

Dobrym rozwiązaniem jest usługi OpenFaaS i usługi OpenFaaS funkcji powinny znajdować się w ich przestrzeniach nazw usługi Kubernetes.

Tworzenie przestrzeni nazw usługi OpenFaaS systemu i funkcje:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Generowanie hasła dla usługi OpenFaaS interfejsu użytkownika portalu i interfejsu API REST:

```azurecli-interactive
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Można uzyskać wartość wpisu tajnego z `echo $PASSWORD`.

Hasło, które w tym miejscu utworzymy będzie służyć przez wykresu helm, aby włączyć uwierzytelnianie podstawowe w bramie usługi OpenFaaS jest uwidaczniany w Internecie za pośrednictwem chmury usługi równoważenia obciążenia.

Wykres narzędzia Helm dla usługi OpenFaaS znajduje się w sklonowanego repozytorium. Skorzystaj z tej tabeli, aby wdrażać usługi OpenFaaS do klastra usługi AKS.

```azurecli-interactive
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
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

Publiczny adres IP jest tworzony na potrzeby uzyskiwania dostępu do bramy usługi OpenFaaS. Aby uzyskać ten adres IP, należy użyć [kubectl get-service] [ kubectl-get] polecenia. Może zająć minutę dla adresu IP, który ma być przypisane do usługi.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Dane wyjściowe.

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Aby przetestować systemu usługi OpenFaaS, przejdź do zewnętrznego adresu IP na porcie 8080, `http://52.186.64.52:8080` w tym przykładzie. Zostanie wyświetlony monit logowania. Aby pobrać hasło, wprowadź `echo $PASSWORD`.

![OpenFaaS UI](media/container-service-serverless/openfaas.png)

Na koniec zainstaluj interfejs wiersza polecenia usługi OpenFaaS. W tym przykładzie użyto brew, zobacz [dokumentacji interfejsu wiersza polecenia usługi OpenFaaS] [ open-faas-cli] więcej opcji.

```console
brew install faas-cli
```

Ustaw `$OPENFAAS_URL` do publicznego adresu IP znaleziony powyżej.

Zaloguj się przy użyciu wiersza polecenia platformy Azure:

```azurecli-interactive
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Tworzenie pierwszej funkcji

Teraz, że działa usługi OpenFaaS Tworzenie funkcji przy użyciu portalu usługi OpenFaas.

Kliknij pozycję **Wdrażanie nowej funkcji** i wyszukaj **Figlet**. Wybierz funkcję Figlet, a następnie kliknij przycisk **Wdróż**.

![Figlet](media/container-service-serverless/figlet.png)

Korzystanie z programu curl do wywołania funkcji. Zamień adres IP w poniższym przykładzie w przypadku bramy usługi OpenFaas.

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

## <a name="create-second-function"></a>Utwórz drugi — funkcja

Teraz Utwórz Druga funkcja. W tym przykładzie zostanie wdrożony przy użyciu interfejsu wiersza polecenia usługi OpenFaaS i obejmuje niestandardowego obrazu kontenera i pobieranie danych z usługi Cosmos DB. Kilka elementów muszą być skonfigurowana przed utworzeniem funkcji.

Najpierw utwórz nową grupę zasobów dla usługi Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Wdróż wystąpienie usługi CosmosDB rodzaju `MongoDB`. Wystąpienie wymaga unikatowej nazwy, zaktualizuj `openfaas-cosmos` unikatowym dla danego środowiska.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Pobieranie parametrów połączenia bazy danych Cosmos i zapisz go w zmiennej.

Zaktualizuj wartość `--resource-group` argument do nazwy grupy zasobów, a `--name` argument do nazwy usługi Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Teraz można wypełnić Cosmos DB przy użyciu danych testowych. Utwórz plik o nazwie `plans.json` i skopiuj następujące dane json.

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

Użyj *mongoimport* narzędzie, aby załadować wystąpienia bazy danych cosmos DB przy użyciu danych.

Jeśli to konieczne, zainstaluj narzędzia bazy danych MongoDB. Następujący przykład powoduje zainstalowanie tych narzędzi, za pomocą brew, zobacz [dokumentacją usługi MongoDB] [ install-mongo] innych opcji.

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

Uruchom następujące polecenie, aby utworzyć funkcję. Zaktualizuj wartość `-g` argumentów za pomocą usługi OpenFaaS adresu bramy.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Po wdrożeniu, powinien być widoczny nowo utworzonej usługi OpenFaaS punktu końcowego usługi dla tej funkcji.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Przetestować funkcję, używając programu curl. Zaktualizuj adres IP przy użyciu usługi OpenFaaS adres bramy.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Dane wyjściowe:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Możesz również przetestować funkcję w Interfejsie użytkownika usługi OpenFaaS.

![tekst alternatywny](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o workshop usługi OpenFaaS za pomocą zestawu z warsztatów stanowiących obejmować tematy takie jak tworzenie własnych bot usługi GitHub można kontynuować korzystanie z wpisów tajnych oraz ich wyświetlanie metryk i skalowanie automatyczne.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
