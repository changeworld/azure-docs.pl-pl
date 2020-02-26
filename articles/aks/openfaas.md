---
title: Korzystanie z OpenFaaS z usługą Azure Kubernetes Service (AKS)
description: Wdrażanie i używanie OpenFaaS z usługą Azure Kubernetes Service (AKS)
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: e684aee1469f855ec651567b805262c71aaf32e5
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594927"
---
# <a name="using-openfaas-on-aks"></a>Korzystanie z OpenFaaS na AKS

[OpenFaaS][open-faas] to struktura służąca do kompilowania funkcji bezserwerowych za pomocą kontenerów. Jako projekt Open Source uzyskano w społeczności wdrożenie na dużą skalę. Ten dokument zawiera szczegółowe informacje dotyczące instalowania i używania OpenFaas w klastrze usługi Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące elementy.

* Podstawowe informacje o Kubernetes.
* Klaster usługi Azure Kubernetes Service (AKS) i poświadczenia AKS skonfigurowane w systemie deweloperskim.
* Interfejs wiersza polecenia platformy Azure został zainstalowany w systemie deweloperskim.
* Narzędzia wiersza polecenia usługi git zainstalowane w systemie.

## <a name="add-the-openfaas-helm-chart-repo"></a>Dodawanie repozytorium wykresu OpenFaaS Helm

OpenFaaS utrzymuje własne wykresy Helm, aby zapewnić aktualność wraz ze wszystkimi najnowszymi zmianami.

```azurecli-interactive
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>Wdróż OpenFaaS

Dobrym sposobem jest przechowywanie funkcji OpenFaaS i OpenFaaS w ich własnej przestrzeni nazw Kubernetes.

Utwórz przestrzeń nazw dla systemu OpenFaaS i funkcji:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Generuj hasło dla portalu interfejsu użytkownika OpenFaaS i interfejsu API REST:

```azurecli-interactive
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Wartość wpisu tajnego można uzyskać przy użyciu `echo $PASSWORD`.

Utworzone tutaj hasło będzie używane przez wykres Helm w celu włączenia uwierzytelniania podstawowego na bramie OpenFaaS, która jest dostępna w Internecie za pomocą modułu równoważenia obciążenia w chmurze.

Wykres Helm dla OpenFaaS jest zawarty w sklonowanym repozytorium. Ten wykres służy do wdrażania OpenFaaS w klastrze AKS.

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

Publiczny adres IP jest tworzony w celu uzyskania dostępu do bramy OpenFaaS. Aby pobrać ten adres IP, użyj polecenia [polecenia kubectl Get Service][kubectl-get] . Przypisanie adresu IP do usługi może potrwać minutę.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Rozdzielczości.

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Aby przetestować system OpenFaaS, przejdź do zewnętrznego adresu IP na porcie 8080, `http://52.186.64.52:8080` w tym przykładzie. Zostanie wyświetlony monit o zalogowanie się. Aby pobrać hasło, wprowadź `echo $PASSWORD`.

![OpenFaaS UI](media/container-service-serverless/openfaas.png)

Na koniec Zainstaluj interfejs wiersza polecenia OpenFaaS. W tym przykładzie użyto rozwiązania brew, zapoznaj się z [dokumentacją interfejsu wiersza polecenia OpenFaaS][open-faas-cli] w celu uzyskania dodatkowych opcji.

```console
brew install faas-cli
```

Ustaw `$OPENFAAS_URL` na publiczny adres IP znaleziony powyżej.

Zaloguj się przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Utwórz pierwszą funkcję

Teraz, gdy OpenFaaS działa, Utwórz funkcję przy użyciu portalu OpenFaas.

Kliknij pozycję **Wdróż nową funkcję** i Wyszukaj **FIGlet**. Wybierz funkcję FIGlet, a następnie kliknij pozycję **Wdróż**.

![Figlet](media/container-service-serverless/figlet.png)

Użyj zwinięciea, aby wywołać funkcję. W poniższym przykładzie Zastąp adres IP używany przez bramę OpenFaas.

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

## <a name="create-second-function"></a>Create — Druga funkcja

Teraz Utwórz drugą funkcję. Ten przykład zostanie wdrożony przy użyciu interfejsu wiersza polecenia OpenFaaS i zawiera niestandardowy obraz kontenera oraz pobranie danych z Cosmos DB. Przed utworzeniem funkcji należy skonfigurować kilka elementów.

Najpierw utwórz nową grupę zasobów dla Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Wdróż wystąpienie CosmosDB typu `MongoDB`. Wystąpienie musi mieć unikatową nazwę i aktualizować `openfaas-cosmos` do czegoś unikatowego dla Twojego środowiska.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Pobierz parametry połączenia z bazą danych Cosmos i Zapisz ją w zmiennej.

Zaktualizuj wartość argumentu `--resource-group` do nazwy grupy zasobów, a argument `--name` do nazwy Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Teraz Wypełnij Cosmos DB danymi testowymi. Utwórz plik o nazwie `plans.json` i skopiuj go do poniższego kodu JSON.

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

Użyj narzędzia *mongoimport* , aby załadować wystąpienie CosmosDB z danymi.

W razie konieczności Zainstaluj narzędzia MongoDB. Poniższy przykład instaluje te narzędzia za pomocą rozwiązania brew, zapoznaj się z [dokumentacją MongoDB][install-mongo] w celu uzyskania innych opcji.

```azurecli-interactive
brew install mongodb
```

Załaduj dane do bazy danych programu.

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

Dane wyjściowe:

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Uruchom następujące polecenie, aby utworzyć funkcję. Zaktualizuj wartość argumentu `-g` przy użyciu adresu bramy OpenFaaS.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Po wdrożeniu należy zobaczyć nowo utworzony punkt końcowy OpenFaaS dla funkcji.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Przetestuj funkcję przy użyciu zwinięcia. Zaktualizuj adres IP przy użyciu adresu bramy OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Dane wyjściowe:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Możesz również przetestować funkcję w interfejsie użytkownika OpenFaaS.

![tekst alternatywny](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Następne kroki

Możesz w dalszym ciągu dowiedzieć się więcej na temat warsztatów OpenFaaS za pomocą zestawu praktycznych laboratoriów, na przykład jak utworzyć własny bot GitHub, zużywać wpisy tajne, wyświetlać metryki i skalowanie automatyczne.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
