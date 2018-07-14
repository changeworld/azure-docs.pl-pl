---
title: Zarządzanie ustawieniami dostarczania dla subskrypcji usługi Azure Event Grid
description: W tym artykule opisano, jak dostosować opcje dostarczania zdarzeń usługi Event Grid.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: tomfitz
ms.openlocfilehash: e91ee640d18e2cf804be33fd130bf48737c9efb1
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035673"
---
# <a name="manage-event-grid-delivery-settings"></a>Zarządzanie ustawieniami dostarczania usługi Event Grid

Podczas tworzenia subskrypcji zdarzeń, można dostosować ustawienia dostarczania zdarzeń. Można ustawić, jak długo usługi Event Grid próbuje dostarczyć wiadomości. Można ustawić konto magazynu do przechowywania zdarzeń, których nie można dostarczyć do punktu końcowego.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-retry-policy"></a>Ustawianie zasad ponawiania prób

Podczas tworzenia subskrypcji usługi Event Grid, można ustawić wartości dla ile usługi Event Grid należy dążyć do dostarczenia zdarzeń. Domyślnie usługi Event Grid prób przez 24 godziny (1440 minut) i próbuje maksymalnie 30-krotnie. Możesz ustawić te wartości dla swojej subskrypcji usługi event grid.

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

## <a name="set-dead-letter-location"></a>Ustaw lokalizację utraconych wiadomości

Kiedy usługi Event Grid może dostarczyć zdarzenia, może wysłać zdarzenia niedostarczone do konta magazynu. Ten proces jest nazywany Obsługa utraconych komunikatów. Domyślnie obsługa utraconych komunikatów nie Włącz usługi Event Grid. Aby ją włączyć, należy określić konto magazynu do przechowywania zdarzeń niedostarczone podczas tworzenia subskrypcji zdarzeń. Możesz pobierać zdarzenia z tego konta magazynu, aby rozwiązać dostaw.

Usługa Event Grid wysyła zdarzenie do lokalizacji utraconych czy wypróbuje wszystkich jego ponownych prób, jeżeli otrzyma komunikat o błędzie, który wskazuje dostarczania nigdy nie powiedzie się. Na przykład jeśli usługi Event Grid otrzyma błąd niewłaściwy format podczas dostarczania zdarzeń, natychmiast wysyła zdarzenia do lokalizacji utraconych wiadomości.

Przed ustawieniem lokalizacja utraconych wiadomości, musi mieć konto magazynu przy użyciu kontenera. Możesz podać punktu końcowego dla tego kontenera, podczas tworzenia subskrypcji zdarzeń. Punkt końcowy jest w formacie: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Poniższy skrypt pobiera identyfikator zasobu istniejącego konta magazynu i tworzy subskrypcji zdarzeń, która używa kontener na tym koncie magazynu dla punktu końcowego utraconych wiadomości.

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

Odpowiadanie na zdarzenia niedostarczone za pomocą usługi Event Grid [utworzysz subskrypcję zdarzeń](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) dla magazynu obiektów blob utraconych wiadomości. Za każdym razem, gdy magazynu obiektów blob utraconych odbiera niedostarczone zdarzeń usługi Event Grid powiadamia programu obsługi. Program obsługi odpowiada za pomocą akcji, które mają zostać podjęte, jaką niedostarczone zdarzenia. 

Aby wyłączyć Obsługa utraconych komunikatów, uruchom ponownie polecenie, aby utworzyć subskrypcję zdarzeń, ale nie zapewniają wartość dla `deadletter-endpoint`. Nie ma potrzeby usuwania subskrypcji zdarzeń.

## <a name="next-steps"></a>Kolejne kroki

* Informacje o dostarczanie zdarzeń i ponownych prób [dostarczanie komunikatów usługi Event Grid i ponów próbę](delivery-and-retry.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć pracę, przy użyciu usługi Event Grid, zobacz [Utwórz i wyznaczać trasy zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
