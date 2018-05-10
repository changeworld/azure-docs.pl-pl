---
title: Mapowania pól niestandardowych schemat Azure zdarzeń siatki
description: Opisuje sposób konwertowania schemat niestandardowego schematu Azure zdarzeń siatki.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 8426d03d5c3058638fecc0fe27a03a7699a23add
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mapowanie pól niestandardowych do schematu siatki zdarzeń

Jeśli dane zdarzenie nie odpowiada oczekiwanej [schematu siatki zdarzeń](event-schema.md), nadal można siatki zdarzeń zdarzeń trasy do subskrybentów. W tym artykule opisano sposób mapowania schematu do schematu zdarzeń siatki.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Oryginalny schematu zdarzeń

Załóżmy, że masz aplikację, która wysyła zdarzenia w następującym formacie:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Chociaż ten format nie jest zgodny wymagany schemat, siatki zdarzeń umożliwia mapowanie pól do schematu. Lub może odbierać wartości w oryginalnym schematu.

## <a name="create-custom-topic-with-mapped-fields"></a>Tworzenie niestandardowego tematu z zamapowanych pól

Podczas tworzenia niestandardowego tematu, należy określić sposób mapowania pól z oryginalnego zdarzenie do schematu siatki zdarzeń. Istnieją trzy właściwości, które służy do dostosowywania mapowania:

* `--input-schema` Parametr określa typ schematu. Dostępne opcje to: *cloudeventv01schema*, *customeventschema*, i *eventgridschema*. Wartość domyślna to eventgridschema. Tworząc niestandardowe mapowanie między schemat i schematu siatki zdarzeń, użyj customeventschema. W przypadku zdarzeń w schemacie CloudEvents, użyj cloudeventv01schema.

* `--input-mapping-default-values` Parametr określa wartości domyślne pól w schemacie zdarzeń siatki. Można ustawić wartości domyślne dla *podmiotu*, *eventtype*, i *dataversion*. Zazwyczaj ten parametr używane w sytuacji, gdy schemat niestandardowy nie zawiera pola, które odpowiada jednej z tych trzech pól. Na przykład określić, że dataversion ma zawsze wartość **1.0**.

* `--input-mapping-fields` Parametr mapowania pól schematu do schematu siatki zdarzeń. Możesz określić wartości w pary klucz wartość oddzielone miejsca. Jako nazwę klucza należy użyć nazwy pola siatki zdarzeń. Wartość Użyj nazwy pola. Można użyć nazwy kluczy dla *identyfikator*, *tematu*, *eventtime*, *podmiotu*, *eventtype*i *dataversion*.

Poniższy przykład tworzy niestandardowe temat niektóre zamapowane i domyślne pola:

```azurecli-interactive
az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

## <a name="subscribe-to-event-grid-topic"></a>Subskrybowanie zdarzeń siatki tematu

Podczas subskrybowania niestandardowego tematu, należy określić schematu, którego chcesz użyć do odbierania zdarzeń. Możesz użyć `--event-delivery-schema` parametru i wartości *cloudeventv01schema*, *eventgridschema*, lub *inputeventschema*. Wartość domyślna to eventgridschema.

Przykłady w tej sekcji używać magazynu kolejek obsługi zdarzeń. Aby uzyskać więcej informacji, zobacz [kierować zdarzeń niestandardowych do magazynu kolejek Azure](custom-event-to-queue-storage.md).

W poniższym przykładzie subskrybuje temat siatki zdarzenia i korzysta z domyślnego schematu siatki zdarzeń:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub1 \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

W następnym przykładzie użyto schemat danych wejściowych zdarzenia:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub2 \
  --event-delivery-schema inputeventschema \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

## <a name="publish-event-to-topic"></a>Publikowanie zdarzeń do tematu

Teraz można przystąpić do wysyłania zdarzeń do niestandardowego tematu i wyświetlić wyniki mapowania. Poniższy skrypt, aby przesłać zdarzenia w [schematu przykład](#original-event-schema):

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/mapeventfields.json)'")

curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Teraz Obejrzyj magazynu kolejki. Dwa subskrypcje dostarczyć zdarzenia w różnych schematach.

Pierwszy subskrypcji używane schematu siatki zdarzeń. Format dostarczonego zdarzeń jest:

```json
{
  "Id": "016b3d68-881f-4ea3-8a9c-ed9246582abe",
  "EventTime": "2018-05-01T20:00:25.2606434Z",
  "EventType": "Created",
  "DataVersion": "1.0",
  "MetadataVersion": "1",
  "Topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "Subject": "DefaultSubject",
  "Data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": { "someDataField1": "SomeDataFieldValue" } 
  }
}
```

Te pola zawierają mapowania z niestandardowego tematu. **myEventTypeField** jest mapowany na **EventType**. Wartości domyślne **DataVersion** i **podmiotu** są używane. **Danych** obiekt zawiera oryginalne pola schematu zdarzeń.

Drugi subskrypcji używany schemat zdarzenie wejściowe. Format dostarczonego zdarzeń jest:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": { "someDataField1": "SomeDataFieldValue" }
}
```

Należy zauważyć, że oryginalne pola zostały dostarczone.

## <a name="next-steps"></a>Kolejne kroki

* Informacje dotyczące dostarczania zdarzeń i ponownych prób [dostarczanie komunikatów zdarzeń siatki i ponów próbę](delivery-and-retry.md).
* Aby obejrzeć wprowadzenie do siatki zdarzeń, zobacz [o siatki zdarzeń](overview.md).
* Aby szybko rozpocząć korzystanie z siatki zdarzeń, zobacz [tworzenie i tras niestandardowych zdarzeń siatki zdarzeń Azure](custom-event-quickstart.md).
