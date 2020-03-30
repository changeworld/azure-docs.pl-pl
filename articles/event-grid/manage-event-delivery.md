---
title: Martwe zasady i zasady ponawiania prób — usługa Azure Event Grid
description: W tym artykule opisano sposób dostosowywania opcji dostarczania zdarzeń dla siatki zdarzeń. Ustaw miejsce docelowe utraconych wiadomości i określ, jak długo ponowić próbę dostarczenia.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: spelluru
ms.openlocfilehash: caed3c077b4df5da5fd8541b2f7e85ef119604b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794036"
---
# <a name="dead-letter-and-retry-policies"></a>Martwe zasady dotyczące utraconych liter i ponownych prób

Podczas tworzenia subskrypcji zdarzeń można dostosować ustawienia dostarczania zdarzeń. W tym artykule pokazano, jak skonfigurować lokalizację martwej litery i dostosować ustawienia ponawiania. Aby uzyskać informacje o tych funkcjach, zobacz [Dostarczanie wiadomości w ucho i ponów próbę .](delivery-and-retry.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-dead-letter-location"></a>Ustawianie lokalizacji utraconych liter

Aby ustawić lokalizację utraconej litery, potrzebujesz konta magazynu do przechowywania zdarzeń, których nie można dostarczyć do punktu końcowego. Przykłady uzyskać identyfikator zasobu istniejącego konta magazynu. Tworzą subskrypcję zdarzeń, która używa kontenera na tym koncie magazynu dla punktu końcowego utraconej litery.

> [!NOTE]
> - Utwórz konto magazynu i kontener obiektów blob w magazynie przed uruchomieniem poleceń w tym artykule.
> - Usługa siatki zdarzeń tworzy obiekty blob w tym kontenerze. Nazwy obiektów blob będzie miał nazwę subskrypcji event grid ze wszystkimi literami w wielkich liter. Na przykład jeśli nazwa subskrypcji jest My-Blob-Subscription, nazwy obiektów blob dead letter będzie miał MY-BLOB-SUBSCRIPTION (myblobcontainer/MY-BLOB-SUBSCRIPTION/2019/8/8/5/111111111-1111-1111-1111-1111-1111111111.json). To zachowanie ma na celu ochronę przed różnicami w obsłudze przypadków między usługami platformy Azure.


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

Aby wyłączyć dead-lettering, uruchom ponownie polecenie, aby utworzyć subskrypcję zdarzenia, ale nie podaj wartości dla `deadletter-endpoint`. Nie musisz usuwać subskrypcji zdarzeń.

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

Aby wyłączyć dead-lettering, uruchom ponownie polecenie, aby utworzyć subskrypcję zdarzenia, ale nie podaj wartości dla `DeadLetterEndpoint`. Nie musisz usuwać subskrypcji zdarzeń.

> [!NOTE]
> Jeśli używasz usługi Azure Poweshell na komputerze lokalnym, użyj programu Azure PowerShell w wersji 1.1.0 lub większej. Pobierz i zainstaluj najnowszą usługę Azure PowerShell z [platformy Azure do pobrania.](https://azure.microsoft.com/downloads/)

## <a name="set-retry-policy"></a>Ustawianie zasad ponawiania prób

Podczas tworzenia subskrypcji usługi Event Grid można ustawić wartości, jak długo usługa Event Grid powinna próbować dostarczyć zdarzenie. Domyślnie usługa Event Grid próbuje przez 24 godziny (1440 minut) lub 30 razy. Można ustawić jedną z tych wartości dla subskrypcji sieci zdarzeń. Wartość czasu zdarzenia do żywo musi być całkowitej liczby od 1 do 1440. Wartość max ponownych prób musi być całkowitej liczby od 1 do 30.

Nie można skonfigurować [harmonogramu ponawiania prób](delivery-and-retry.md#retry-schedule-and-duration).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby ustawić czas zdarzenia na czas na żywo na wartość inną niż 1440 minut, należy użyć:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Aby ustawić maksymalne ponownych prób na wartość inną niż 30, należy użyć:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Jeśli ustawisz oba `event-ttl` i `max-deliver-attempts`, Event Grid używa pierwszego, aby wygasnąć, aby określić, kiedy zatrzymać dostarczanie zdarzeń.

### <a name="powershell"></a>PowerShell

Aby ustawić czas zdarzenia na czas na żywo na wartość inną niż 1440 minut, należy użyć:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Aby ustawić maksymalne ponownych prób na wartość inną niż 30, należy użyć:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Jeśli ustawisz oba `EventTtl` i `MaxDeliveryAttempt`, Event Grid używa pierwszego, aby wygasnąć, aby określić, kiedy zatrzymać dostarczanie zdarzeń.

## <a name="next-steps"></a>Następne kroki

* Dla przykładowej aplikacji, która używa aplikacji funkcji platformy Azure do przetwarzania zdarzeń utraconych list, zobacz [Przykłady utraconych listów usługi Azure Event Grid dla platformy .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Aby uzyskać informacje o dostarczaniu zdarzeń i ponownych próbach, [w ucho.](delivery-and-retry.md)
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z usługi Event Grid, zobacz [Tworzenie i rozsyłanie zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
