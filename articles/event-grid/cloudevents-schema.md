---
title: Użyj siatki zdarzeń Azure ze zdarzeniami w schemacie CloudEvents
description: Opisuje sposób ustawiania schematu CloudEvents zdarzeń w siatce zdarzeń platformy Azure.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/30/2018
ms.author: babanisa
ms.openlocfilehash: a882073fce28be1b93a6c9118c40398062f61bc5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="use-cloudevents-schema-with-event-grid"></a>Użyj schematu CloudEvents siatki zdarzeń

Oprócz jego [domyślny schemat zdarzeń](event-schema.md), siatki zdarzeń Azure zapewnia natywną obsługę zdarzeń w [schematu CloudEvents JSON](https://github.com/cloudevents/spec/blob/master/json-format.md). [CloudEvents](http://cloudevents.io/) jest [Otwórz standardowej specyfikacji](https://github.com/cloudevents/spec/blob/master/spec.md) opisu danych zdarzeń w typowy sposób.

CloudEvents ułatwia współdziałanie, zapewniając wspólny schemat zdarzeń do publikowania i korzystających z chmury na podstawie zdarzeń. Ten schemat umożliwia uniform narzędzi, standardowe metody routingu i obsługi zdarzeń oraz uniwersalnych sposobów deserializacji schematu zewnętrzne zdarzeń. Z wspólny schemat można łatwiej zintegrować pracy na platformach.

CloudEvents są kompilowane przez kilka [współpracownicy](https://github.com/cloudevents/spec/blob/master/community/contributors.md), łącznie z firmy Microsoft, [chmury natywnego obliczeniowe Foundation](https://www.cncf.io/). Jest dostępna jako wersja 0,1.

W tym artykule opisano sposób użycia schematu CloudEvents zdarzeń siatki.

## <a name="cloudevent-schema"></a>CloudEvent schematu

Oto przykład zdarzenia magazynu obiektów Blob Azure w formacie CloudEvents:

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

| CloudEvents        | Typ     | Przykładowa wartość JSON             | Opis                                                        | Mapowanie siatki zdarzeń
|--------------------|----------|--------------------------------|--------------------------------------------------------------------|-------------------------
| Typ zdarzenia          | Ciąg   | "com.example.someevent"          | Typ wystąpienia, który wystąpił                                   | Typ zdarzenia
| eventTypeVersion   | Ciąg   | "1.0"                            | Wersja eventType (opcjonalnie)                            | dataVersion
| cloudEventsVersion | Ciąg   | "0,1"                            | Wersja specyfikacji CloudEvents, który używa zdarzenia        | *przekazywane*
| source             | Identyfikator URI      | "/ mycontext"                     | W tym artykule opisano producent zdarzeń                                       | temat #subject
| Identyfikator zdarzenia            | Ciąg   | "1234-1234-1234"                 | Identyfikator zdarzenia                                                    | id
| eventTime          | Sygnatura czasowa| "2018-04-05T17:31:00Z"           | Sygnatura czasowa gdy zdarzenie wystąpiło (opcjonalnie)                    | eventTime
| schemaURL          | Identyfikator URI      | „https://myschema.com”           | Łącze do schematu, zgodną atrybutu danych (opcjonalnie) | *nie jest używany*
| Typ zawartości        | Ciąg   | "application/json"               | Opisz format kodowania danych (opcjonalnie)                       | *nie jest używany*
| Rozszerzenia         | Mapa      | {"extA": "vA", "extB", "vB"}  | Wszelkie dodatkowe metadane (opcjonalnie)                                 | *nie jest używany*
| dane               | Obiekt   | {"objA": "vA", "objB", "vB"}  | Ładunek zdarzenia (opcjonalnie)                                       | dane

Aby uzyskać więcej informacji, zobacz [CloudEvents spec](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes).

## <a name="configure-event-grid-for-cloudevents"></a>Skonfiguruj siatki zdarzeń dla CloudEvents

Siatka zdarzeń Azure ma obecnie, Podgląd obsługę danych wejściowych formatu CloudEvents JSON i danych wyjściowych w **zachodnie centralnej nam**, **środkowe stany USA**, i **Europa Północna**.

Aby użyć CloudEvent, należy włączyć rozszerzenie dla wiersza polecenia platformy Azure:

```azurecli
az extension add –-name eventgrid
```

Dla danych wejściowych i wyjściowych zdarzeń w schemacie CloudEvents, można użyć zdarzenia siatki. Można użyć CloudEvents dla zdarzeń systemowych, takich jak zdarzenia magazynu obiektów Blob i zdarzenia Centrum IoT i zdarzeń niestandardowych. Można również przekształcać tych zdarzeń umieszczonego w obu kierunkach.


| Schemat danych wejściowych       | Schemat danych wyjściowych
|--------------------|---------------------
| CloudEvents format | CloudEvents format
| Format siatki zdarzeń  | CloudEvents format
| CloudEvents format | Format siatki zdarzeń
| Format siatki zdarzeń  | Format siatki zdarzeń

Dla wszystkich schematów zdarzeń siatki Zdarzenie wymaga weryfikacji podczas publikowania zdarzenia tematu siatki i podczas tworzenia subskrypcji zdarzeń. Aby uzyskać więcej informacji, zobacz [siatki zdarzeń zabezpieczeń i uwierzytelniania](security-authentication.md).

### <a name="input-schema"></a>Schemat danych wejściowych

Aby zdefiniować schemat danych wejściowych na temat CloudEvents na niestandardowe, użyj następującego parametru w wiersza polecenia platformy Azure, podczas tworzenia tematu `--input-schema cloudeventv01schema`. Niestandardowy temat teraz oczekuje zdarzenia przychodzące w formacie v0.1 CloudEvents.

Aby utworzyć temat siatki zdarzenia, należy użyć:

```azurecli
az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
```

Bieżąca wersja CloudEvents nie obsługuje przetwarzanie wsadowe zdarzeń. Aby opublikować zdarzenia ze schematem CloudEvent tematu, publikować indywidualnie każdego zdarzenia.

### <a name="output-schema"></a>Schemat danych wyjściowych

Aby ustawić subskrypcję zdarzeń do CloudEvents schemat danych wyjściowych, użyj następującego parametru w wiersza polecenia platformy Azure, podczas tworzenia subskrypcji zdarzeń `--event-delivery-schema cloudeventv01schema`. Zdarzenia dla tej subskrypcji zdarzenia są teraz można dostarczyć w formacie v0.1 CloudEvents.

Aby utworzyć subskrypcję zdarzeń, należy użyć:

```azurecli
az eventgrid event-subscription create \
  --name <event_subscription_name> \  
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

Bieżąca wersja CloudEvents nie obsługuje przetwarzanie wsadowe zdarzeń. Subskrypcję zdarzeń skonfigurowanego dla schematu CloudEvent indywidualnie odbiera każdego zdarzenia.

## <a name="next-steps"></a>Kolejne kroki

* Informacje o monitorowaniu dostaw zdarzeń, zobacz [dostarczanie komunikatów Monitora zdarzeń siatki](monitor-event-delivery.md).
* Firma Microsoft zachęca do testowania, komentarza, i [współtworzenia](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) do CloudEvents.
* Aby uzyskać więcej informacji o tworzeniu subskrypcji platformy Azure zdarzeń siatki, zobacz [schematu subskrypcji zdarzeń siatki](subscription-creation-schema.md).
