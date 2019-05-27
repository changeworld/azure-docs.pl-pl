---
title: Utraconych wiadomości i zasady ponawiania prób dla subskrypcji usługi Azure Event Grid
description: W tym artykule opisano, jak dostosować opcje dostarczania zdarzeń usługi Event Grid. Zestaw docelowy utraconych wiadomości i określić, jak długo próbę dostarczania.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/06/2019
ms.author: spelluru
ms.openlocfilehash: a1b49fd3a2a85377a56c92aefd1b0056f91895b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66119564"
---
# <a name="dead-letter-and-retry-policies"></a>Utraconych wiadomości i zasady ponawiania prób

Podczas tworzenia subskrypcji zdarzeń, można dostosować ustawienia dostarczania zdarzeń. W tym artykule przedstawiono sposób ustawiania lokalizacji utraconych wiadomości i Dostosuj ustawienia ponawiania prób. Aby uzyskać informacje o tych funkcjach, zobacz [dostarczanie komunikatów usługi Event Grid i ponów próbę](delivery-and-retry.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-dead-letter-location"></a>Ustaw lokalizację utraconych wiadomości

Aby ustawić lokalizację utraconych wiadomości, potrzebne jest konto magazynu do przechowywania zdarzeń, których nie można dostarczyć do punktu końcowego. Przykłady Pobierz identyfikator zasobu istniejącego konta magazynu. Tworzą subskrypcji zdarzeń, która używa kontener na tym koncie magazynu dla punktu końcowego utraconych wiadomości.

> [!NOTE]
> Utwórz konto magazynu i kontener obiektów blob w magazynie, przed uruchomieniem polecenia w tym artykule.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Aby wyłączyć Obsługa utraconych komunikatów, uruchom ponownie polecenie, aby utworzyć subskrypcję zdarzeń, ale nie zapewniają wartość dla `deadletter-endpoint`. Nie ma potrzeby usuwania subskrypcji zdarzeń.

> [!NOTE]
> Jeśli używasz interfejsu wiersza polecenia platformy Azure na komputerze lokalnym, powinien on być w wersji co najmniej 2.0.56. Aby uzyskać instrukcje na temat instalowania najnowszej wersji interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

Aby wyłączyć Obsługa utraconych komunikatów, uruchom ponownie polecenie, aby utworzyć subskrypcję zdarzeń, ale nie zapewniają wartość dla `DeadLetterEndpoint`. Nie ma potrzeby usuwania subskrypcji zdarzeń.

> [!NOTE]
> Jeśli używasz Poweshell platformy Azure na komputerze lokalnym, użyj programu Azure PowerShell w wersji 1.1.0 lub nowszej. Pobierz i zainstaluj najnowszą wersję programu Azure PowerShell z [pliki do pobrania Azure](https://azure.microsoft.com/downloads/).

## <a name="set-retry-policy"></a>Ustawianie zasad ponawiania prób

Podczas tworzenia subskrypcji usługi Event Grid, można ustawić wartości dla ile usługi Event Grid należy dążyć do dostarczenia zdarzeń. Domyślnie usługi Event Grid próbuje przez 24 godziny (1440 minut) lub 30-krotnie. Możesz ustawić te wartości dla swojej subskrypcji usługi event grid. Wartość dla zdarzenia, czas wygaśnięcia musi być liczbą całkowitą z zakresu od 1 do 1440. Wartość maksymalną liczbę ponownych prób musi być liczbą całkowitą z zakresu od 1 do 30.

Nie można skonfigurować [harmonogram ponownych prób](delivery-and-retry.md#retry-schedule-and-duration).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby ustawić zdarzenia time to live wartość inna niż 1440 minut, należy użyć:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Aby ustawić maksymalną liczbę ponownych prób na wartość inną niż 30, należy użyć:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Jeśli ustawisz zarówno `event-ttl` i `max-deliver-attempts`, korzysta z usługi Event Grid pierwszy na wygaśnięcie, aby określić, kiedy przestać dostarczania zdarzeń.

### <a name="powershell"></a>PowerShell

Aby ustawić zdarzenia time to live wartość inna niż 1440 minut, należy użyć:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Aby ustawić maksymalną liczbę ponownych prób na wartość inną niż 30, należy użyć:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Jeśli ustawisz zarówno `EventTtl` i `MaxDeliveryAttempt`, korzysta z usługi Event Grid pierwszy na wygaśnięcie, aby określić, kiedy przestać dostarczania zdarzeń.

## <a name="next-steps"></a>Kolejne kroki

* Dla przykładowej aplikacji, która używa aplikacji funkcji platformy Azure do przetwarzania zdarzeń utraconych wiadomości, zobacz [przykładów utraconych siatki zdarzeń dla platformy Azure dla platformy .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Informacje o dostarczanie zdarzeń i ponownych prób [dostarczanie komunikatów usługi Event Grid i ponów próbę](delivery-and-retry.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć pracę, przy użyciu usługi Event Grid, zobacz [Utwórz i wyznaczać trasy zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
