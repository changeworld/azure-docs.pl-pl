---
title: Zarządzanie ustawieniami dostarczania dla subskrypcji Azure zdarzeń siatki
description: Opisuje sposób dostosowywania opcje dostarczania zdarzeń dla zdarzenia siatki.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: tomfitz
ms.openlocfilehash: 65e79f492e96c418502e096b60992ba992868dd7
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036503"
---
# <a name="manage-event-grid-delivery-settings"></a>Zarządzanie ustawieniami dostarczania zdarzeń siatki

Podczas tworzenia subskrypcji zdarzeń, można dostosować ustawienia w celu dostarczania zdarzeń. Można ustawić, jak długo zdarzenia siatki próbuje dostarczenie wiadomości. Można ustawić konto magazynu, które będzie używane do przechowywania zdarzeń, których nie można dostarczyć do punktu końcowego.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-retry-policy"></a>Zasady ponawiania zestawu

Podczas tworzenia subskrypcji zdarzeń siatki, można ustawić wartości jak długo zdarzenia siatki należy dążyć do dostarczenia zdarzenia. Domyślnie zdarzenia siatki podejmuje przez 24 godziny (1440 minut), a próbuje maksymalnie 30 razy. Dla Twojej subskrypcji siatki zdarzeń można ustawić jedną z tych wartości.

Aby ustawić zdarzenia time-to-live, na wartość inną niż 1440 minut, należy użyć:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
  --event-ttl 720
```

Aby ustawić maksymalną liczbę ponownych prób na wartość inną niż 30, należy użyć:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
  --max-delivery-attempts 18
```

Jeżeli wartość `event-ttl` i `max-deliver-attempts`, siatka zdarzeń używa pierwszego wygaśnie dla ponawiania prób.

## <a name="set-dead-letter-location"></a>Ustaw lokalizację utraconych wiadomości

Kiedy siatki zdarzeń nie może dostarczyć zdarzenie, może wysłać zdarzenia niedostarczone do konta magazynu. Ten proces jest nazywany Obsługa utraconych komunikatów. Domyślnie zdarzenia siatki nie włączyć Obsługa utraconych komunikatów. Aby ją włączyć, należy określić konto magazynu, aby mógł pomieścić zdarzenia niedostarczone podczas tworzenia subskrypcji zdarzeń. Użytkownik uzyskuje zdarzenia z tego konta magazynu, aby rozwiązać dostaw.

Siatki zdarzeń wysyła zdarzenia do lokalizacji utraconych wiadomości, jeśli podjął wszystkie jego ponownych prób, lub jeśli odbiera komunikat o błędzie, który wskazuje dostarczania nigdy nie powiedzie się. Na przykład jeśli zdarzenie siatki odebrał błąd niewłaściwy format podczas dostarczania zdarzeń, natychmiast wysyła zdarzenia do lokalizacji utraconych wiadomości.

Przed ustawieniem lokalizacji utraconych wiadomości, musisz mieć konto magazynu, z kontenerem. Musisz podać punktu końcowego dla tego kontenera, podczas tworzenia subskrypcji zdarzeń. Punkt końcowy jest w formacie: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Poniższy skrypt pobiera identyfikator zasobu istniejącego konta magazynu i tworzy subskrypcję zdarzeń, która używa kontener na tym koncie magazynu dla punktu końcowego utraconych wiadomości.

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
  --endpoint <endpoint_URL>
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Na potrzeby zdarzeń siatki odpowiadanie na zdarzenia niedostarczone [utworzyć subskrypcję zdarzeń](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) dla magazynu obiektów blob utraconych wiadomości. Za każdym razem, gdy usługi magazynu obiektów blob utraconych odbiera zdarzenie niedostarczone siatki zdarzeń powiadamia użytkownika programu obsługi. Program obsługi odpowiada z akcjami, które mają zostać podjęte, jaką niedostarczone zdarzenia. 

## <a name="next-steps"></a>Kolejne kroki

* Informacje dotyczące dostarczania zdarzeń i ponownych prób [dostarczanie komunikatów zdarzeń siatki i ponów próbę](delivery-and-retry.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z siatki zdarzeń, zobacz [tworzenie i tras niestandardowych zdarzeń siatki zdarzeń Azure](custom-event-quickstart.md).
