---
title: Mapy niestandardowe pole do schematu usługi Azure Event Grid
description: W tym artykule opisano sposób konwertowania niestandardowego schematu na schemat Azure Event Grid, gdy dane zdarzenia nie są zgodne ze schematem Event Grid.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: e8077068a265d659cf6009eb7762188637c373d6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721663"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mapowanie pól niestandardowych na schemat usługi Event Grid

Jeśli dane zdarzenia nie są zgodne z oczekiwanym [schematem Event Grid](event-schema.md), można nadal używać Event Grid do kierowania zdarzenia do subskrybentów. W tym artykule opisano sposób mapowania schematu do schematu usługi Event Grid.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalowanie funkcji w wersji zapoznawczej

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

Podczas tworzenia niestandardowego tematu, należy określić sposób mapowania pól z oryginalnego zdarzenia do schematu siatki zdarzeń. Istnieją trzy wartości, które umożliwia dostosowywanie mapowania:

* Wartość **schematu wejściowego** określa typ schematu. Dostępne opcje to: schematu CloudEvents, schemacie zdarzenie niestandardowe lub schematu usługi Event Grid. Wartość domyślna to schemat usługi Event Grid. Podczas tworzenia niestandardowego mapowanie między schematu i schemat siatki zdarzeń, Użyj schematu zdarzeń niestandardowych. Gdy zdarzenia są schematu CloudEvents, używanie schematu Cloudevents.

* Właściwość **mapowania wartości domyślnych** określa wartości domyślne dla pól w schemacie Event Grid. Można ustawić wartości domyślne dla `subject`, `eventtype`i `dataversion`. Zazwyczaj używa się tego parametru podczas gdy niestandardowego schematu nie zawiera pola, które odpowiada jednej z tych trzech pól. Można na przykład określić, że wersja danych ma zawsze wartość **1,0**.

* Wartość **pola mapowania** mapuje pola ze schematu do schematu usługi Event Grid. Możesz określić wartości w pary klucz/wartość rozdzielonych spacjami. Nazwa klucza musi zawierać nazwę pola siatki zdarzeń. Dla wartości Użyj nazwy pola. Można użyć nazw kluczy dla `id`, `topic`, `eventtime`, `subject`, `eventtype`i `dataversion`.

Aby utworzyć niestandardowego tematu przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
# If you have not already installed the extension, do it now.
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

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName myResourceGroup `
  -Name demotopic `
  -Location eastus2 `
  -InputSchema CustomEventSchema `
  -InputMappingField @{eventType="myEventTypeField"} `
  -InputMappingDefaultValue @{subject="DefaultSubject"; dataVersion="1.0" }
```

## <a name="subscribe-to-event-grid-topic"></a>Subskrybowanie do tematu usługi event grid

Podczas subskrybowania tematu niestandardowego, należy określić schemat, którego chcesz użyć do odbierania zdarzeń. Określasz schematu CloudEvents, zdarzenie niestandardowe schematu lub schemat usługi Event Grid. Wartość domyślna to schemat usługi Event Grid.

Poniższy przykład ilustruje subskrybowanie tematu usługi event grid i używa schematu usługi Event Grid. W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

W następnym przykładzie użyto schemat danych wejściowych zdarzenia:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

Poniższy przykład ilustruje subskrybowanie tematu usługi event grid i używa schematu usługi Event Grid. W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

W następnym przykładzie użyto schemat danych wejściowych zdarzenia:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Publikowanie zdarzenia do tematu

Teraz możesz przystąpić do wysłania zdarzenia do tematu niestandardowego i zobaczyć wyniki tego mapowania. Następujący skrypt do opublikowania zdarzenia w [przykładowym schemacie](#original-event-schema):

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

event='[ { "myEventTypeField":"Created", "resource":"Users/example/Messages/1000", "resourceData":{"someDataField1":"SomeDataFieldValue"} } ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demotopic).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName myResourceGroup -Name demotopic

$htbody = @{
    myEventTypeField="Created"
    resource="Users/example/Messages/1000"
    resourceData= @{
        someDataField1="SomeDataFieldValue"
    }
}

$body = "["+(ConvertTo-Json $htbody)+"]"
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Teraz Spójrz na punkt końcowy usługi elementu WebHook. Obie subskrypcje zdarzeń są dostarczane w różnych schematach.

Pierwsza używany schemat siatki zdarzeń. Format dostarczonego zdarzenia jest następujący:

```json
{
  "id": "aa5b8e2a-1235-4032-be8f-5223395b9eae",
  "eventTime": "2018-11-07T23:59:14.7997564Z",
  "eventType": "Created",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "subject": "DefaultSubject",
  "data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": {
      "someDataField1": "SomeDataFieldValue"
    }
  }
}
```

Te pola zawierają mapowania z tematu niestandardowego. **myEventTypeField** jest mapowany na **Typ zdarzenia**. Używane są wartości domyślne dla elementu **dataversion** i **subject** . Obiekt **danych** zawiera oryginalne pola schematu zdarzenia.

Drugiej subskrypcji używany schemat danych wejściowych zdarzeń. Format dostarczonego zdarzenia jest następujący:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

Należy zauważyć, że oryginalne pola zostały dostarczone.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje dotyczące dostarczania i ponawiania zdarzeń, [Event Grid dostarczania komunikatów i ponawiania próby](delivery-and-retry.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z Event Grid, zobacz [Tworzenie i kierowanie zdarzeń niestandardowych z Azure Event Grid](custom-event-quickstart.md).
