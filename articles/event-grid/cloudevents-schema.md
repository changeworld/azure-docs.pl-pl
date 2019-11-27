---
title: Używanie Azure Event Grid ze zdarzeniami w schemacie CloudEvents
description: Opisuje sposób ustawiania schematu CloudEvents dla zdarzeń w Azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 6a0e24ce7fa11c6373fbaada40cd9f1b1e7f55a2
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325472"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Używanie schematu CloudEvents v 1.0 z Event Grid

Poza [domyślnym schematem zdarzeń](event-schema.md), Azure Event Grid natywnie obsługuje zdarzenia w [implementacji JSON CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) i [powiązania protokołu HTTP](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) to [otwarta Specyfikacja](https://github.com/cloudevents/spec/blob/v1.0/spec.md) dla opisywania danych zdarzenia.

CloudEvents upraszcza współdziałanie, dostarczając Typowy schemat zdarzeń do publikowania i zużywania zdarzeń opartych na chmurze. Ten schemat umożliwia korzystanie z jednolitych narzędzi, standardowych metod routingu & obsługi zdarzeń oraz uniwersalne sposoby deserializacji schematu zdarzenia zewnętrznego. Ze wspólnym schematem można łatwiej zintegrować prace między platformami.

CloudEvents jest tworzona przez kilku [współpracowników](https://github.com/cloudevents/spec/blob/master/community/contributors.md), w tym firmę Microsoft, za pośrednictwem [natywnej podstawy przetwarzania w chmurze](https://www.cncf.io/). Jest ona obecnie dostępna jako wersja 1,0.

W tym artykule opisano sposób używania schematu CloudEvents z Event Grid.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalowanie funkcji w wersji zapoznawczej

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>Schemat CloudEvent

Oto przykład zdarzenia usługi Azure Blob Storage w formacie CloudEvents:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

Szczegółowy opis dostępnych pól, ich typów i definicji w CloudEvents v 0,1 jest [dostępny tutaj](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Wartości nagłówków dla zdarzeń dostarczonych w schemacie CloudEvents i schemacie Event Grid są takie same, z wyjątkiem `content-type`. Dla schematu CloudEvents wartość tego nagłówka jest `"content-type":"application/cloudevents+json; charset=utf-8"`. Dla Event Grid schematu wartość tego nagłówka jest `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>Konfigurowanie Event Grid CloudEvents

Można użyć Event Grid zarówno dla danych wejściowych, jak i wyjściowych zdarzeń w schemacie CloudEvents. CloudEvents można używać dla zdarzeń systemowych, takich jak zdarzenia Blob Storage i zdarzenia IoT Hub i zdarzenia niestandardowe. Może również przekształcić te zdarzenia do tyłu i do przodu.


| Schemat wejściowy       | Schemat danych wyjściowych
|--------------------|---------------------
| Format CloudEvents | Format CloudEvents
| Event Grid format  | Format CloudEvents
| Format CloudEvents | Event Grid format
| Event Grid format  | Event Grid format

W przypadku wszystkich schematów zdarzeń Event Grid wymaga weryfikacji podczas publikowania w temacie Event Grid i podczas tworzenia subskrypcji zdarzeń. Aby uzyskać więcej informacji, zobacz [Event Grid zabezpieczenia i uwierzytelnianie](security-authentication.md).

### <a name="input-schema"></a>Schemat wejściowy

Schemat wejściowy dla tematu niestandardowego ustawia się podczas tworzenia tematu niestandardowego.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
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
  -InputSchema CloudEventSchemaV1_0
```

Bieżąca wersja programu CloudEvents nie obsługuje przetwarzania wsadowego zdarzeń. Aby opublikować zdarzenia ze schematem CloudEvent w temacie, Opublikuj każde wydarzenie osobno.

### <a name="output-schema"></a>Schemat danych wyjściowych

Schemat wyjściowy jest ustawiany podczas tworzenia subskrypcji zdarzeń.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

W przypadku programu PowerShell użyj polecenia:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 Obecnie nie można używać wyzwalacza Event Grid dla aplikacji Azure Functions, gdy zdarzenie jest dostarczane w schemacie CloudEvents. Użyj wyzwalacza HTTP. Przykłady implementacji wyzwalacza HTTP, który odbiera zdarzenia w schemacie CloudEvents, można znaleźć w temacie [Używanie wyzwalacza http jako wyzwalacza Event Grid](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger).

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Walidacja punktu końcowego z CloudEvents v 1.0

Jeśli znasz już Event Grid, możesz mieć świadomość, Event Grid uzgadniania sprawdzania poprawności punktu końcowego w celu zapobiegania nadużyciom. CloudEvents v 1.0 implementuje własną [semantykę ochrony przed nadużyciami](security-authentication.md#webhook-event-delivery) przy użyciu metody http Options. Więcej informacji na ten temat można znaleźć [tutaj](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). W przypadku korzystania ze schematu CloudEvents dla danych wyjściowych Event Grid jest używany z ochroną nadużycia CloudEvents v 1.0 zamiast mechanizmu zdarzenia walidacji Event Grid.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat monitorowania dostarczania zdarzeń, zobacz [monitorowanie Event Grid dostarczania komunikatów](monitor-event-delivery.md).
* Zachęcamy do testowania, komentowania i [współtworzenia](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) CloudEvents.
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
