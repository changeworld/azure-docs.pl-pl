---
title: Mapy niestandardowe pole do schematu usługi Azure Event Grid
description: W tym artykule opisano sposób konwertowania niestandardowego schematu do schematu usługi Azure Event Grid.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: tomfitz
ms.openlocfilehash: f79fa096484edc34294ea0a69584e12788dba647
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043401"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mapowanie pól niestandardowych na schemat usługi Event Grid

Jeśli dane zdarzenie nie odpowiada oczekiwanej [schematu usługi Event Grid](event-schema.md), można nadal używać usługi Event Grid zdarzenia trasy dla subskrybentów. W tym artykule opisano sposób mapowania schematu do schematu usługi Event Grid.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Oryginalny schemat zdarzeń

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

Mimo że ten format nie jest zgodny wymagany schemat, usługi Event Grid umożliwia mapowanie pól w schemacie. Ewentualnie można odbierać wartości w oryginalnym schematem.

## <a name="create-custom-topic-with-mapped-fields"></a>Tworzenie tematu niestandardowego przy użyciu zamapowane pola

Podczas tworzenia niestandardowego tematu, należy określić sposób mapowania pól z oryginalnego zdarzenia do schematu siatki zdarzeń. Istnieją trzy właściwości, które umożliwia dostosowywanie mapowania:

* `--input-schema` Parametr określa typ schematu. Dostępne opcje to: *cloudeventv01schema*, *customeventschema*, i *eventgridschema*. Wartość domyślna to eventgridschema. Podczas tworzenia niestandardowego mapowanie między schematu i schemat siatki zdarzeń, użyj customeventschema. Gdy zdarzenia są schematu CloudEvents, należy użyć cloudeventv01schema.

* `--input-mapping-default-values` Parametr określa wartości domyślne dla pól w schemacie usługi Event Grid. Można ustawić wartości domyślne dla `subject`, `eventtype`, i `dataversion`. Zazwyczaj używa się tego parametru podczas gdy niestandardowego schematu nie zawiera pola, które odpowiada jednej z tych trzech pól. Na przykład można określić tej wersji danych jest zawsze równa **1.0**.

* `--input-mapping-fields` Parametr mapowania pól schematu do schematu siatki zdarzeń. Możesz określić wartości w pary klucz/wartość rozdzielonych spacjami. Nazwa klucza musi zawierać nazwę pola siatki zdarzeń. Dla wartości Użyj nazwy pola. Można użyć nazw dla `id`, `topic`, `eventtime`, `subject`, `eventtype`, i `dataversion`.

Poniższy przykład tworzy niestandardowy temat z niektórymi mapowane i domyślne pola:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema \
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

## <a name="subscribe-to-event-grid-topic"></a>Subskrybowanie do tematu usługi event grid

Podczas subskrybowania tematu niestandardowego, należy określić schemat, którego chcesz użyć do odbierania zdarzeń. Możesz użyć `--event-delivery-schema` parametru i ustaw ją na *cloudeventv01schema*, *eventgridschema*, lub *inputeventschema*. Wartość domyślna to eventgridschema.

Przykłady w tej sekcji Użyj usługi Queue storage dla programu obsługi zdarzeń. Aby uzyskać więcej informacji, zobacz [kierowanie zdarzeń niestandardowych do usługi Azure Queue storage](custom-event-to-queue-storage.md).

Poniższy przykład ilustruje subskrybowanie tematu usługi event grid i używa schematu siatki zdarzeń:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
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

## <a name="publish-event-to-topic"></a>Publikowanie zdarzenia do tematu

Teraz możesz przystąpić do wysłania zdarzenia do tematu niestandardowego i zobaczyć wyniki tego mapowania. Poniższy skrypt w celu opublikowania zdarzenia w [schematu przykład](#original-event-schema):

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

event='[ { "myEventTypeField":"Created", "resource":"Users/example/Messages/1000", "resourceData":{"someDataField1":"SomeDataFieldValue"} } ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

Teraz Przyjrzyj się usługi Queue storage. Obie subskrypcje zdarzeń są dostarczane w różnych schematach.

Pierwsza używany schemat siatki zdarzeń. Format dostarczonego zdarzenia jest następujący:

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

Te pola zawierają mapowania z tematu niestandardowego. **myEventTypeField** jest mapowany na **Typzdarzenia**. Wartości domyślne **DataVersion** i **podmiotu** są używane. **Danych** obiekt zawiera pola oryginalnego schematu zdarzeń.

Drugiej subskrypcji używany schemat danych wejściowych zdarzeń. Format dostarczonego zdarzenia jest następujący:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": { "someDataField1": "SomeDataFieldValue" }
}
```

Należy zauważyć, że oryginalne pola zostały dostarczone.

## <a name="next-steps"></a>Kolejne kroki

* Informacje o dostarczanie zdarzeń i ponownych prób [dostarczanie komunikatów usługi Event Grid i ponów próbę](delivery-and-retry.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć pracę, przy użyciu usługi Event Grid, zobacz [Utwórz i wyznaczać trasy zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
