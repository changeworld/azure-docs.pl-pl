---
title: Utraconych wiadomości i zasady ponawiania prób dla subskrypcji usługi Azure Event Grid
description: W tym artykule opisano, jak dostosować opcje dostarczania zdarzeń usługi Event Grid. Zestaw docelowy utraconych wiadomości i określić, jak długo próbę dostarczania.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: tomfitz
ms.openlocfilehash: 5db53567b1df9e726fb0c507f0302536ea45b7f4
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603774"
---
# <a name="dead-letter-and-retry-policies"></a>Utraconych wiadomości i zasady ponawiania prób

Podczas tworzenia subskrypcji zdarzeń, można dostosować ustawienia dostarczania zdarzeń. W tym artykule przedstawiono sposób ustawiania lokalizacji utraconych wiadomości i Dostosuj ustawienia ponawiania prób. Aby uzyskać informacje o tych funkcjach, zobacz [dostarczanie komunikatów usługi Event Grid i ponów próbę](delivery-and-retry.md).

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>Ustaw lokalizację utraconych wiadomości

Aby ustawić lokalizację utraconych wiadomości, potrzebne jest konto magazynu do przechowywania zdarzeń, których nie można dostarczyć do punktu końcowego. Poniższy skrypt pobiera identyfikator zasobu istniejącego konta magazynu i tworzy subskrypcji zdarzeń, która używa kontener na tym koncie magazynu dla punktu końcowego utraconych wiadomości.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

storagename=demostorage
containername=testcontainer

storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Aby wyłączyć Obsługa utraconych komunikatów, uruchom ponownie polecenie, aby utworzyć subskrypcję zdarzeń, ale nie zapewniają wartość dla `deadletter-endpoint`. Nie ma potrzeby usuwania subskrypcji zdarzeń.

## <a name="set-retry-policy"></a>Ustawianie zasad ponawiania prób

Podczas tworzenia subskrypcji usługi Event Grid, można ustawić wartości dla ile usługi Event Grid należy dążyć do dostarczenia zdarzeń. Domyślnie usługi Event Grid prób przez 24 godziny (1440 minut) i próbuje maksymalnie 30-krotnie. Możesz ustawić te wartości dla swojej subskrypcji usługi event grid. Wartość dla zdarzenia, czas wygaśnięcia musi być liczbą całkowitą z zakresu od 1 do 1440. Wartość kolejnymi próbami dostarczenia maksymalna musi być liczbą całkowitą z zakresu od 1 do 30.

Nie można skonfigurować [interwał ponawiania prób](delivery-and-retry.md#retry-intervals-and-duration).

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

Jeśli ustawisz zarówno `event-ttl` i `max-deliver-attempts`, korzysta z usługi Event Grid pierwszy wygaśnie dla ponownych prób.

## <a name="next-steps"></a>Kolejne kroki

* Dla przykładowej aplikacji, która używa aplikacji funkcji platformy Azure do przetwarzania zdarzeń utraconych wiadomości, zobacz [przykładów utraconych siatki zdarzeń dla platformy Azure dla platformy .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Informacje o dostarczanie zdarzeń i ponownych prób [dostarczanie komunikatów usługi Event Grid i ponów próbę](delivery-and-retry.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć pracę, przy użyciu usługi Event Grid, zobacz [Utwórz i wyznaczać trasy zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
