---
title: Mapowanie pola niestandardowego do schematu siatki zdarzeń platformy Azure
description: W tym artykule opisano sposób konwertowania schematu niestandardowego do schematu usługi Azure Event Grid, gdy dane zdarzeń nie są zgodne ze schematem siatki zdarzeń.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: e8077068a265d659cf6009eb7762188637c373d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721663"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mapowanie pól niestandardowych na schemat usługi Event Grid

Jeśli dane zdarzenia nie są zgodne ze [schematem oczekiwanej siatki zdarzeń,](event-schema.md)nadal można użyć usługi Event Grid do kierowania zdarzenia do subskrybentów. W tym artykule opisano sposób mapowania schematu do schematu siatki zdarzeń.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalowanie funkcji w wersji zapoznawczej

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Schemat oryginalnego zdarzenia

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

Mimo że ten format nie jest zgodny z wymaganym schematem, usługa Event Grid umożliwia mapowanie pól do schematu. Lub można odbierać wartości w oryginalnym schemacie.

## <a name="create-custom-topic-with-mapped-fields"></a>Tworzenie tematu niestandardowego z mapowanymi polami

Podczas tworzenia tematu niestandardowego określ sposób mapowania pól z oryginalnego zdarzenia do schematu siatki zdarzeń. Istnieją trzy wartości używane do dostosowywania mapowania:

* Wartość **schematu wejściowego** określa typ schematu. Dostępne opcje to schemat CloudEvents, niestandardowy schemat zdarzeń lub schemat siatki zdarzeń. Wartością domyślną jest schemat siatki zdarzeń. Podczas tworzenia mapowania niestandardowego między schematem a schematem siatki zdarzeń należy użyć niestandardowego schematu zdarzeń. Gdy zdarzenia znajdują się w schemacie CloudEvents, użyj schematu Cloudevents.

* Właściwość **wartości domyślnych mapowania** określa wartości domyślne dla pól w schemacie siatki zdarzeń. Można ustawić wartości `subject`domyślne dla , `eventtype`i `dataversion`. Zazwyczaj ten parametr jest używany, gdy schemat niestandardowy nie zawiera pola, które odpowiada jednemu z tych trzech pól. Na przykład można określić, że wersja danych jest zawsze ustawiona na **1.0**.

* **Wartości pól mapowania** mapuje pola ze schematu do schematu siatki zdarzeń. Należy określić wartości w pary kluczy/wartości oddzielonych spacją. W przypadku nazwy klucza należy użyć nazwy pola siatki zdarzenia. W przypadku wartości użyj nazwy pola. Można użyć nazw `id`kluczy dla `eventtype`, `dataversion` `topic`, `eventtime` `subject`, , i .

Aby utworzyć temat niestandardowy za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

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

## <a name="subscribe-to-event-grid-topic"></a>Subskrybuj temat siatki zdarzeń

Podczas subskrybowania tematu niestandardowego, należy określić schemat, który ma być używany do odbierania zdarzeń. Należy określić schemat CloudEvents, schemat zdarzeń niestandardowych lub schemat siatki zdarzeń. Wartością domyślną jest schemat siatki zdarzeń.

Poniższy przykład subskrybuje temat siatki zdarzeń i używa schematu siatki zdarzeń. W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

W następnym przykładzie użyto schematu wejściowego zdarzenia:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

Poniższy przykład subskrybuje temat siatki zdarzeń i używa schematu siatki zdarzeń. W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

W następnym przykładzie użyto schematu wejściowego zdarzenia:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Publikowanie zdarzenia w temacie

Teraz możesz wysłać zdarzenie do tematu niestandardowego i wyświetlić wynik mapowania. Następujący skrypt do opublikowania zdarzenia w [przykładowym schemacie:](#original-event-schema)

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

Teraz spójrz na punkt końcowy webhook. Dwie subskrypcje dostarczone zdarzenia w różnych schematów.

Pierwsza subskrypcja używane schemat siatki zdarzeń. Format dostarczonego zdarzenia jest:

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

Te pola zawierają mapowania z tematu niestandardowego. **myEventTypeField** jest mapowany na **Typ zdarzenia**. Używane są wartości domyślne dla **DataVersion** i **Subject.** **Data** Obiekt zawiera oryginalne pola schematu zdarzeń.

Druga subskrypcja używane schemat zdarzenia wejściowego. Format dostarczonego zdarzenia jest:

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

* Aby uzyskać informacje o dostarczaniu zdarzeń i ponownych próbach, [w ucho.](delivery-and-retry.md)
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z usługi Event Grid, zobacz [Tworzenie i rozsyłanie zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
