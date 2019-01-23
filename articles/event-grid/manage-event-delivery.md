---
title: Utraconych wiadomości i zasady ponawiania prób dla subskrypcji usługi Azure Event Grid
description: W tym artykule opisano, jak dostosować opcje dostarczania zdarzeń usługi Event Grid. Zestaw docelowy utraconych wiadomości i określić, jak długo próbę dostarczania.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/06/2019
ms.author: spelluru
ms.openlocfilehash: 8660f09c41cf6226f2ffb173508d37c260522b80
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474255"
---
# <a name="dead-letter-and-retry-policies"></a>Utraconych wiadomości i zasady ponawiania prób

Podczas tworzenia subskrypcji zdarzeń, można dostosować ustawienia dostarczania zdarzeń. W tym artykule przedstawiono sposób ustawiania lokalizacji utraconych wiadomości i Dostosuj ustawienia ponawiania prób. Aby uzyskać informacje o tych funkcjach, zobacz [dostarczanie komunikatów usługi Event Grid i ponów próbę](delivery-and-retry.md).

## <a name="install-preview-feature"></a>Instalowanie funkcji w wersji zapoznawczej

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>Ustaw lokalizację utraconych wiadomości

Aby ustawić lokalizację utraconych wiadomości, potrzebne jest konto magazynu do przechowywania zdarzeń, których nie można dostarczyć do punktu końcowego. Przykłady Pobierz identyfikator zasobu istniejącego konta magazynu. Tworzą subskrypcji zdarzeń, która używa kontener na tym koncie magazynu dla punktu końcowego utraconych wiadomości.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

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

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

$containername = "testcontainer"

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzureRmStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

Aby wyłączyć Obsługa utraconych komunikatów, uruchom ponownie polecenie, aby utworzyć subskrypcję zdarzeń, ale nie zapewniają wartość dla `DeadLetterEndpoint`. Nie ma potrzeby usuwania subskrypcji zdarzeń.

## <a name="set-retry-policy"></a>Ustawianie zasad ponawiania prób

Podczas tworzenia subskrypcji usługi Event Grid, można ustawić wartości dla ile usługi Event Grid należy dążyć do dostarczenia zdarzeń. Domyślnie usługi Event Grid próbuje przez 24 godziny (1440 minut) lub 30-krotnie. Możesz ustawić te wartości dla swojej subskrypcji usługi event grid. Wartość dla zdarzenia, czas wygaśnięcia musi być liczbą całkowitą z zakresu od 1 do 1440. Wartość maksymalną liczbę ponownych prób musi być liczbą całkowitą z zakresu od 1 do 30.

Nie można skonfigurować [harmonogram ponownych prób](delivery-and-retry.md#retry-schedule-and-duration).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby ustawić zdarzenia time to live wartość inna niż 1440 minut, należy użyć:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

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
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Aby ustawić maksymalną liczbę ponownych prób na wartość inną niż 30, należy użyć:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
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
