---
title: Konfigurowanie menedżera danych blockchain przy użyciu interfejsu wiersza polecenia platformy Azure — usługa Azure Blockchain
description: Tworzenie menedżera danych blockchain dla usługi Azure Blockchain i zarządzanie nim przy użyciu interfejsu wiersza polecenia platformy Azure
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: a8061aad6d6a1513de70e7c2bc57aa109c666611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455940"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Konfigurowanie menedżera danych łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure

Skonfiguruj menedżera danych blockchain dla usługi Azure Blockchain, aby przechwytywać dane łańcucha bloków, aby wysyłać je do tematu usługi Azure Event Grid.

Aby skonfigurować wystąpienie menedżera danych blockchain, należy:

* Tworzenie wystąpienia Menedżera łańcucha bloków
* Tworzenie danych wejściowych do węzła transakcji usługi Azure Blockchain
* Tworzenie danych wyjściowych w temacie siatki zdarzeń platformy Azure
* Dodawanie aplikacji blockchain
* Uruchamianie wystąpienia

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj najnowszą [platformę](https://docs.microsoft.com/cli/azure/install-azure-cli) Cli `az login`platformy Azure i zalogowano się przy użyciu programu .
* Ukończ [szybki start: łączenie się z siecią konsorcjum usługi Azure Blockchain Service za pomocą kodu programu Visual Studio](connect-vscode.md)
* Tworzenie [tematu siatki zdarzeń](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Dowiedz się więcej o [programach obsługi zdarzeń w usłudze Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w [https://shell.azure.com/bash](https://shell.azure.com/bash)osobnej karcie przeglądarki, przechodząc do . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Jeśli wolisz zainstalować i używać interfejsu wiersza polecenia lokalnie, ten szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.51 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli chcesz zainstalować lub uaktualnić, zobacz [instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Utwórz wystąpienie

Wystąpienie menedżera danych blockchain monitoruje węzeł transakcji usługi Azure Blockchain Service. Wystąpienie przechwytuje wszystkie nieprzetworzone dane transakcji bloku i nieprzetworzonego z węzła transakcji.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| Parametr | Opis |
|-----------|-------------|
| resource-group | Nazwa grupy zasobów, gdzie można utworzyć wystąpienie Menedżera danych blockchain. |
| name | Nazwa instancji Blockchain Data Manager. |
| typ zasobu | Typ zasobu wystąpienia Menedżera danych blockchain to **Microsoft.blockchain/watchers**. |
| is-full-object | Wskazuje, że właściwości zawierają opcje zasobu obserwatora. |
| properties | Ciąg w formacie JSON zawierający właściwości zasobu obserwatora. Może być przekazywana jako ciąg lub plik.  |

### <a name="create-instance-examples"></a>Tworzenie przykładów wystąpień

Przykład konfiguracji JSON do tworzenia wystąpienia Menedżera łańcucha bloków w regionie **Wschodnie stany USA.**

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Element | Opis |
|---------|-------------|
| location | Region, w którym można utworzyć zasób obserwatora |
| properties | Właściwości ustawione podczas tworzenia zasobu obserwatora |

Utwórz wystąpienie Menedżera danych blockchain o nazwie *mywatcher* przy użyciu ciągu JSON dla konfiguracji.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Utwórz wystąpienie Menedżera danych blockchain o nazwie *mywatcher* przy użyciu pliku konfiguracyjnego JSON.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Tworzenie danych wejściowych

Dane wejściowe łączy Menedżera danych blockchain z węzłem transakcji usługi Azure Blockchain Service. Tylko użytkownicy z dostępem do węzła transakcji mogą utworzyć połączenie.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| Parametr | Opis |
|-----------|-------------|
| resource-group | Nazwa grupy zasobów, gdzie należy utworzyć zasób wejściowy. |
| name | Nazwa danych wejściowych. |
| namespace | Użyj obszaru nazw dostawcy **microsoft.blockchain.** |
| typ zasobu | Typ zasobu dla danych wejściowych Blockchain Data Manager jest **dane wejściowe**. |
| Nadrzędny | Ścieżka do obserwatora, z którym jest skojarzone dane wejściowe. Na przykład **obserwatorzy/mywatcher**. |
| is-full-object | Wskazuje, że właściwości zawierają opcje dla zasobu wejściowego. |
| properties | Ciąg w formacie JSON zawierający właściwości zasobu wejściowego. Może być przekazywana jako ciąg lub plik. |

### <a name="input-examples"></a>Przykłady wprowadzania

Przykład JSON konfiguracji, aby utworzyć zasób wejściowy \<w\>regionie *Wschodnie stany USA,* który jest połączony z członkiem łańcucha bloków .

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| Element | Opis |
|---------|-------------|
| location | Region, w którym należy utworzyć zasób wejściowy. |
| inputType | Typ księgi członka usługi Azure Blockchain. Obecnie **Ethereum** jest obsługiwane. |
| resourceId | Węzeł transakcji, do którego jest podłączone dane wejściowe. Zamień \<\>identyfikator \<subskrypcji, \<grupę\> \>zasobów i członka łańcucha bloków wartościami zasobu węzła transakcji. Dane wejściowe łączy się z domyślnym węzłem transakcji dla członka usługi Azure Blockchain Service. |

Utwórz dane wejściowe o nazwie *myInput* dla *mywatcher* przy użyciu ciągu JSON dla konfiguracji.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

Utwórz dane wejściowe o nazwie *myInput* dla *mywatcher* przy użyciu pliku konfiguracyjnego JSON.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>Tworzenie danych wyjściowych

Połączenie wychodzące wysyła dane łańcucha bloków do usługi Azure Event Grid. Możesz wysyłać dane łańcucha bloków do jednego miejsca docelowego lub wysyłać dane łańcucha bloków do wielu miejsc docelowych. Blockchain Data Manager obsługuje wiele połączeń wychodzących w temacie siatki zdarzeń dla danego wystąpienia Menedżera danych Blockchain.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| Parametr | Opis |
|-----------|-------------|
| resource-group | Nazwa grupy zasobów, gdzie należy utworzyć zasób wyjściowy. |
| name | Nazwa danych wyjściowych. |
| namespace | Użyj obszaru nazw dostawcy **microsoft.blockchain.** |
| typ zasobu | Typ zasobu dla wyjścia Blockchain Data Manager jest **wyprowadza .** |
| Nadrzędny | Ścieżka do obserwatora, z którym jest skojarzone dane wyjściowe. Na przykład **obserwatorzy/mywatcher**. |
| is-full-object | Wskazuje, że właściwości zawierają opcje dla zasobu wyjściowego. |
| properties | Ciąg w formacie JSON zawierający właściwości zasobu wyjściowego. Może być przekazywana jako ciąg lub plik. |

### <a name="output-examples"></a>Przykłady danych wyjściowych

Przykład JSON konfiguracji w celu utworzenia zasobu wyjściowego w regionie \< *Wschodnie stany USA,* który jest połączony z tematem siatki zdarzeń o nazwie temat\>siatki zdarzeń .

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| Element | Opis |
|---------|-------------|
| location | Region, w którym należy utworzyć zasób wyjściowy. |
| Outputtype | Typ wyjścia. Obecnie **EventGrid** jest obsługiwany. |
| resourceId | Zasób, do którego jest podłączone dane wyjściowe. Zamień \<\>identyfikator \<subskrypcji, \<grupę\> \>zasobów i członka łańcucha bloków wartościami zasobu siatki zdarzeń. |

Utwórz dane wyjściowe o nazwie *myoutput* dla *mywatcher,* który łączy się z tematem siatki zdarzeń przy użyciu ciągu konfiguracji JSON.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

Utwórz dane wyjściowe o nazwie *myoutput* dla *mywatcher,* który łączy się z tematem siatki zdarzeń przy użyciu pliku konfiguracji JSON.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>Dodaj aplikację blockchain

Jeśli dodasz aplikację blockchain, Blockchain Data Manager dekoduje zdarzenia i stan właściwości dla aplikacji. W przeciwnym razie wysyłane są tylko nieprzetworzone dane transakcji bloku i nieprzetworzonego. Blockchain Data Manager odnajduje również adresy umów po wdrożeniu umowy. Do wystąpienia Menedżera danych Blockchain można dodać wiele aplikacji blockchain.


> [!IMPORTANT]
> Obecnie aplikacje łańcucha bloków, które deklarują [typy tablic](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) solidity lub [typy mapowania,](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) nie są w pełni obsługiwane. Właściwości zadeklarowane jako typy tablic lub mapowania nie zostaną zdekodowane w *komunikatach ContractPropertiesMsg* lub *DecodedContractEventsMsg.*

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| Parametr | Opis |
|-----------|-------------|
| resource-group | Nazwa grupy zasobów, gdzie można utworzyć zasób aplikacji. |
| name | Nazwa aplikacji. |
| namespace | Użyj obszaru nazw dostawcy **microsoft.blockchain.** |
| typ zasobu | Typ zasobu aplikacji Blockchain Data Manager to **artefakty.** |
| Nadrzędny | Ścieżka do obserwatora, z którym jest skojarzona aplikacja. Na przykład **obserwatorzy/mywatcher**. |
| is-full-object | Wskazuje, że właściwości zawierają opcje dla zasobu aplikacji. |
| properties | Ciąg w formacie JSON zawierający właściwości zasobu aplikacji. Może być przekazywana jako ciąg lub plik. |

### <a name="blockchain-application-examples"></a>Przykłady zastosowań łańcucha bloków

Przykład JSON konfiguracji, aby utworzyć zasób aplikacji w regionie *Wschodnie stany USA,* który monitoruje inteligentnego kontraktu zdefiniowane przez kontrakt ABI i kod bajtowy.

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| Element | Opis |
|---------|-------------|
| location | Region, w którym należy utworzyć zasób aplikacji. |
| artifactType | Typ aplikacji. Obecnie **EthereumSmartContract** jest obsługiwany. |
| abiFileUrl | Adres URL inteligentnego kontraktu ABI JSON. Aby uzyskać więcej informacji na temat uzyskiwania kontraktu ABI i tworzenia adresu URL, zobacz [Uzyskiwanie abi kontraktu i kodu bajtowego](data-manager-portal.md#get-contract-abi-and-bytecode) oraz [tworzenie umowy ABI i adresu URL kodu bajtowego](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| bajtecodeFileUrl | Adres URL inteligentnego kontraktu wdrożonego pliku JSON kodu bajtowego. Aby uzyskać więcej informacji na temat uzyskiwania inteligentnego kontraktu wdrożonego kodu bajtowego i tworzenia adresu URL, zobacz [Uzyskiwanie abi kontraktu i kodu bajtowego](data-manager-portal.md#get-contract-abi-and-bytecode) oraz [tworzenie umowy ABI i adresu URL kodu bajtowego](data-manager-portal.md#create-contract-abi-and-bytecode-url). Uwaga: Blockchain Data Manager wymaga **wdrożonego kodu bajtowego**. |
| queryTargetTypes (Typy docelowe) | Opublikowane typy wiadomości. Określanie **ContractProperties** publikuje *ContractPropertiesMsg* typu wiadomości. Określanie **ContractEvents** publikuje *decodedContractEventsMsg* typu komunikatu. Uwaga: *RawBlockAndTransactionMsg* i *RawTransactionContractCreationMsg* typy komunikatów są zawsze publikowane. |

Utwórz aplikację o nazwie *myApplication* dla *mywatcher,* która monitoruje inteligentny kontrakt zdefiniowany przez ciąg JSON.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

Utwórz aplikację o nazwie *myApplication* dla *mywatcher,* która obserwuje inteligentną umowę zdefiniowaną przy użyciu pliku konfiguracyjnego JSON.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>Wystąpienie startowe

Podczas uruchamiania wystąpienie Menedżera łańcucha bloków monitoruje zdarzenia łańcucha bloków ze zdefiniowanych danych wejściowych i wysyła dane do zdefiniowanych wyjść.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parametr | Opis |
|-----------|-------------|
| action | Użyj **startu,** aby uruchomić obserwatora. |
| Identyfikatory | Identyfikator zasobu obserwatora. Zamień \<\>identyfikator \<subskrypcji, grupę\>zasobów i \<nazwę\> obserwatora wartościami zasobu obserwatora.|

### <a name="start-instance-example"></a>Przykład wystąpienia początkowego

Uruchom wystąpienie Menedżera danych blockchain o nazwie *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Zatrzymaj wystąpienie

Zatrzymaj wystąpienie Menedżera danych blockchain.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parametr | Opis |
|-----------|-------------|
| action | Użyj **stop,** aby zatrzymać obserwatora. |
| Identyfikatory | Imię i nazwisko obserwatora. Zamień \<\>identyfikator \<subskrypcji, grupę\>zasobów i \<nazwę\> obserwatora wartościami zasobu obserwatora. |

### <a name="stop-watcher-example"></a>Przykład obserwatora stopu

Zatrzymaj wystąpienie o nazwie *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Usuń wystąpienie

Usuń wystąpienie Menedżera danych blockchain.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Parametr | Opis |
|-----------|-------------|
| resource-group | Nazwa grupy zasobów obserwatora do usunięcia. |
| name | Nazwa obserwatora do usunięcia. |
| typ zasobu | Typem zasobu dla obserwatora Blockchain Data Manager jest **Microsoft.blockchain/ watchers**. |

### <a name="delete-instance-example"></a>Przykład usuwania wystąpienia

Usuń wystąpienie o nazwie *mywatcher* w grupie zasobów *myRG.*

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Następne kroki

Wypróbuj następny samouczek tworzenia eksploratora komunikatów transakcji łańcucha bloków przy użyciu menedżera danych blockchain i usługi Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Wysyłanie danych do usługi Azure Cosmos DB za pomocą menedżera danych łańcucha bloków](data-manager-cosmosdb.md)
