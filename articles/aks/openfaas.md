---
title: Używanie usługi OpenFaaS z usługą Azure Kubernetes Service (AKS)
description: Dowiedz się, jak wdrożyć i używać OpenFaaS w klastrze usługi Azure Kubernetes (AKS) do tworzenia funkcji bezserwerowych z kontenerami.
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: 95039573c607f516755f08f1ebad8b968416ec8b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631473"
---
# <a name="using-openfaas-on-aks"></a>Korzystanie z OpenFaaS w aks

[OpenFaaS][open-faas] jest platformą do tworzenia funkcji bezserwerowych za pomocą kontenerów. Jako projekt open source zyskał on przyjęcie na dużą skalę w społeczności. Ten dokument zawiera szczegółowe informacje dotyczące instalowania i używania programu OpenFaas w klastrze usługi Azure Kubernetes (AKS).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebujesz następujących czynności.

* Podstawowa wiedza o Kubernetes.
* Klaster usługi Azure Kubernetes (AKS) i poświadczenia usługi AKS skonfigurowane w systemie deweloperskim.
* Narzędzie interfejsu wiersza polecenia platformy Azure zainstalowane w systemie dewelopera.
* Narzędzia wiersza polecenia Git zainstalowane w systemie.

## <a name="add-the-openfaas-helm-chart-repo"></a>Dodawanie repozytorium wykresu helm OpenFaaS

Przejdź [https://shell.azure.com](https://shell.azure.com) do otwierania usługi Azure Cloud Shell w przeglądarce.

OpenFaaS utrzymuje własne wykresy helm, aby być na bieżąco ze wszystkimi najnowszymi zmianami.

```console
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>Wdrażanie OpenFaaS

Dobrym rozwiązaniem jest, że funkcje OpenFaaS i OpenFaaS powinny być przechowywane we własnej przestrzeni nazw Kubernetes.

Utwórz obszar nazw dla systemu OpenFaaS i funkcji:

```console
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Generowanie hasła do interfejsu API interfejsu użytkownika OpenFaaS i interfejsu API REST:

```console
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Możesz uzyskać wartość klucza `echo $PASSWORD`tajnego za pomocą .

Hasło, które tutaj tworzymy, będzie używane przez wykres helm, aby włączyć uwierzytelnianie podstawowe w bramie OpenFaaS, która jest narażona na Działanie Internetu za pośrednictwem programu LoadBalancer w chmurze.

Wykres helm dla OpenFaaS znajduje się w sklonowanym repozytorium. Ten wykres służy do wdrażania programu OpenFaaS w klastrze AKS.

```console
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
```

Dane wyjściowe:

```output
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

Publiczny adres IP jest tworzony w celu uzyskania dostępu do bramy OpenFaaS. Aby pobrać ten adres IP, użyj polecenia [kubectl get service.][kubectl-get] Przypisanie adresu IP do usługi może potrwać minutę.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Wyjście.

```output
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Aby przetestować system OpenFaaS, przejdź do zewnętrznego adresu IP na `http://52.186.64.52:8080` porcie 8080, w tym przykładzie. Zostanie wyświetlony monit o zalogowanie się. Aby pobrać hasło, `echo $PASSWORD`wprowadź .

![Interfejs użytkownika OpenFaaS](media/container-service-serverless/openfaas.png)

Na koniec zainstaluj plik CLI OpenFaaS. W tym przykładzie użyto brew, zobacz [Dokumentacja interfejsu wiersza polecenia OpenFaaS, aby][open-faas-cli] uzyskać więcej opcji.

```console
brew install faas-cli
```

Ustaw `$OPENFAAS_URL` publiczny adres IP znaleziony powyżej.

Zaloguj się za pomocą interfejsu wiersza polecenia platformy Azure:

```console
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Tworzenie pierwszej funkcji

Teraz, gdy OpenFaaS działa, utwórz funkcję za pomocą portalu OpenFaas.

Kliknij na **Wdrażanie nowej funkcji** i wyszukaj **Figlet**. Wybierz funkcję Figlet i kliknij przycisk **Wdrażanie**.

![Figlet](media/container-service-serverless/figlet.png)

Użyj curl wywołać funkcję. Zastąp adres IP w poniższym przykładzie adresem bramy OpenFaas.

```console
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Dane wyjściowe:

```output
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Tworzenie drugiej funkcji

Teraz utwórz drugą funkcję. W tym przykładzie zostanie wdrożony przy użyciu interfejsu wiersza polecenia OpenFaaS i zawiera niestandardowy obraz kontenera i pobieranie danych z usługi Cosmos DB. Kilka elementów należy skonfigurować przed utworzeniem funkcji.

Najpierw utwórz nową grupę zasobów dla usługi Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Wdrażanie wystąpienia usługi CosmosDB w rodzaju `MongoDB`. Wystąpienie wymaga unikatowej `openfaas-cosmos` nazwy, aktualizacji do czegoś unikatowego dla środowiska.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Pobierz ciąg połączenia bazy danych Cosmos i przechowywać go w zmiennej.

Zaktualizuj `--resource-group` wartość argumentu do nazwy grupy `--name` zasobów, a argument do nazwy bazy danych usługi Cosmos.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Teraz wypełnij cosmos DB danymi testowymi. Utwórz plik `plans.json` o nazwie i skopiuj w następującym json.

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

Użyj narzędzia *mongoimport,* aby załadować wystąpienie usługi CosmosDB z danymi.

W razie potrzeby zainstaluj narzędzia MongoDB. Poniższy przykład instaluje te narzędzia przy użyciu naparowania, zobacz [dokumentację MongoDB][install-mongo] dla innych opcji.

```console
brew install mongodb
```

Załaduj dane do bazy danych.

```console
mongoimport --uri=$COSMOS -c plans < plans.json
```

Dane wyjściowe:

```output
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Uruchom następujące polecenie, aby utworzyć funkcję. Zaktualizuj `-g` wartość argumentu za pomocą adresu bramy OpenFaaS.

```console
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Po wdrożeniu powinien zostać wyświetlony nowo utworzony punkt końcowy OpenFaaS dla tej funkcji.

```output
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Przetestuj funkcję za pomocą curl. Zaktualizuj adres IP za pomocą adresu bramy OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Dane wyjściowe:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Można również przetestować funkcję w interfejsie użytkownika OpenFaaS.

![tekst alternatywny](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Następne kroki

Możesz nadal uczyć się z warsztatów OpenFaaS za pośrednictwem zestawu praktycznych laboratoriów, które obejmują takie tematy, jak tworzenie własnego bota GitHub, korzystanie z wpisów tajnych, wyświetlanie metryk i automatyczne skalowanie.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
