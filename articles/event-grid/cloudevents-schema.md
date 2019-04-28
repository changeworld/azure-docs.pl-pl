---
title: Za pomocą usług Azure Event Grid zdarzenia w schematu CloudEvents
description: W tym artykule opisano sposób ustawiania schematu CloudEvents zdarzeń w usłudze Azure Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: babanisa
ms.openlocfilehash: 0195ce82396a7b05335242a38a2881e1b2d1afb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61436605"
---
# <a name="use-cloudevents-schema-with-event-grid"></a>Używanie schematu CloudEvents za pomocą usługi Event Grid

W uzupełnieniu do jego [domyślny schemat zdarzeń](event-schema.md), Azure Event Grid zapewnia natywną obsługę zdarzeń w [schematu CloudEvents JSON](https://github.com/cloudevents/spec/blob/master/json-format.md). [CloudEvents](https://cloudevents.io/) jest [Otwórz specyfikacji](https://github.com/cloudevents/spec/blob/master/spec.md) opisu danych zdarzenia.

CloudEvents ułatwia współdziałanie, zapewniając wspólny schemat zdarzeń do publikowania i korzystających z chmury na podstawie zdarzeń. Ten schemat umożliwia jednolitego narzędzi, sposób standardowy routing i obsługa zdarzeń i uniwersalnej sposoby deserializacji schematu zdarzeń zewnętrzne. Za pomocą wspólnego schematu można łatwiej zintegrować pracy między platformami.

CloudEvents jest konstruowany przez kilka [współpracowników](https://github.com/cloudevents/spec/blob/master/community/contributors.md), Microsoft, w tym za pośrednictwem [Cloud natywne przetwarzanie Foundation](https://www.cncf.io/). Jest obecnie dostępna w wersji 0,1.

W tym artykule opisano używanie schematu CloudEvents za pomocą usługi Event Grid.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalowanie funkcji w wersji zapoznawczej

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent schematu

Oto przykład zdarzenia usługi Azure Blob Storage w formacie CloudEvents:

``` JSON
{
    "cloudEventsVersion" : "0.1",
    "eventType" : "Microsoft.Storage.BlobCreated",
    "eventTypeVersion" : "",
    "source" : "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}#blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "eventID" : "173d9985-401e-0075-2497-de268c06ff25",
    "eventTime" : "2018-04-28T02:18:47.1281675Z",
    "data" : {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
}
```

CloudEvents v0.1 ma następujące właściwości dostępne:

| CloudEvents        | Type     | Przykład wartości JSON             | Opis                                                        | Mapowanie siatki zdarzeń
|--------------------|----------|--------------------------------|--------------------------------------------------------------------|-------------------------
| eventType          | String   | "com.example.someevent"          | Typ wystąpienia, które wystąpiły w                                   | eventType
| eventTypeVersion   | String   | "1.0"                            | Wersja typu zdarzenia (opcjonalnie)                            | dataVersion
| cloudEventsVersion | String   | "0.1"                            | Wersja specyfikacji CloudEvents, który używa zdarzenia        | *przekazany przez*
| source             | URI      | "/ mycontext"                     | W tym artykule opisano producenta zdarzeń                                       | temat #subject
| Identyfikator zdarzenia            | String   | "1234-1234-1234"                 | Identyfikator zdarzenia                                                    | id
| eventTime          | Znacznik czasu| "2018-04-05T17:31:00Z"           | Sygnatura czasowa gdy zdarzenie wystąpiło (opcjonalnie)                    | eventTime
| schemaURL          | URI      | "https:\//myschema.com"           | Link do schematu, zgodną atrybutu danych (opcjonalnie) | *nie jest używany*
| contentType        | String   | "application/json"               | Opisz format kodowania danych (opcjonalnie)                       | *nie jest używany*
| Rozszerzenia         | Mapa      | { "extA": "vA", "extB", "vB" }  | Wszelkie dodatkowe metadane (opcjonalnie)                                 | *nie jest używany*
| dane               | Object   | {"objA": "oceny luk w zabezpieczeniach", "objB", "vB"}  | Ładunek zdarzenia (opcjonalnie)                                       | dane

Aby uzyskać więcej informacji, zobacz [Specyfikacja CloudEvents](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes).

Wartości nagłówków zapewniane przez schemat usługi Event Grid i schematu CloudEvents zdarzenia są takie same, z wyjątkiem `content-type`. Dla schematu CloudEvents, ta wartość nagłówka jest `"content-type":"application/cloudevents+json; charset=utf-8"`. Dla schematu usługi Event Grid, że wartość nagłówka jest `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>Konfigurowanie usługi Event Grid dla CloudEvents

Zarówno dla danych wejściowych i danych wyjściowych, zdarzeń w schematu CloudEvents, można użyć usługi Event Grid. Można użyć CloudEvents dla zdarzenia systemowe, takie jak zdarzenia usługi Blob Storage i zdarzeń usługi IoT Hub i zdarzeń niestandardowych. Można również przekształcać tych zdarzeń, jak i z powrotem.


| Schemat danych wejściowych       | Schemat danych wyjściowych
|--------------------|---------------------
| CloudEvents format | CloudEvents format
| Format siatki zdarzeń  | CloudEvents format
| CloudEvents format | Format siatki zdarzeń
| Format siatki zdarzeń  | Format siatki zdarzeń

Dla wszystkich schematów zdarzeń usługi Event Grid wymaga weryfikacji podczas publikowania na temat usługi event grid i podczas tworzenia subskrypcji zdarzeń. Aby uzyskać więcej informacji, zobacz [usługi Event Grid zabezpieczeń i uwierzytelniania](security-authentication.md).

### <a name="input-schema"></a>Schemat danych wejściowych

Schemat danych wejściowych dla tematu niestandardowego można ustawić po utworzeniu tematu niestandardowego.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventV01Schema
```

Bieżąca wersja CloudEvents nie obsługuje tworzenie partii zdarzeń. Do publikowania zdarzeń ze schematem CloudEvent tematu, publikować indywidualnie każdego zdarzenia.

### <a name="output-schema"></a>Schemat danych wyjściowych

Schemat danych wyjściowych można ustawić podczas tworzenia subskrypcji zdarzeń.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

W przypadku programu PowerShell użyj polecenia:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventV01Schema
```

Bieżąca wersja CloudEvents nie obsługuje tworzenie partii zdarzeń. Subskrypcję zdarzeń, który jest skonfigurowany dla schematu CloudEvent odbiera każde zdarzenie indywidualnie. Obecnie nie można użyć wyzwalacza usługi Event Grid dla aplikacji usługi Azure Functions, gdy zdarzenie jest dostarczany za schematu CloudEvents. Użyj wyzwalacza HTTP. Przykłady implementacji wyzwalacza HTTP, który odbiera zdarzenia w schematu CloudEvents, zobacz [użyć wyzwalacza HTTP jako wyzwalacz usługi Event Grid](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger).

## <a name="next-steps"></a>Kolejne kroki

* Informacje o monitorowaniu dostarczenie zdarzeń, zobacz [dostarczanie komunikatów Monitor Event Grid](monitor-event-delivery.md).
* Zachęcamy do testowania, komentarz, a [współtworzyć](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) do CloudEvents.
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
