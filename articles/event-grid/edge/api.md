---
title: INTERFEJS API REST — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: INTERFEJS API REST w siatce zdarzeń w usłudze IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 19f86b1d8233e05844201e1095c1f79324955cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841833"
---
# <a name="rest-api"></a>Interfejs API REST
W tym artykule opisano interfejsy API REST usługi Azure Event Grid w usłudze IoT Edge

## <a name="common-api-behavior"></a>Typowe zachowanie interfejsu API

### <a name="base-url"></a>Podstawowy adres URL
Usługa Event Grid na stronie IoT Edge ma następujące interfejsy API udostępniane za pośrednictwem protokołu HTTP (port 5888) i HTTPS (port 4438).

* Podstawowy adres URL protokołu HTTP:http://eventgridmodule:5888
* Podstawowy adres URL protokołu HTTPS:https://eventgridmodule:4438

### <a name="request-query-string"></a>Żądanie ciągu zapytania
Wszystkie żądania interfejsu API wymagają następującego parametru ciągu kwerendy:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Typ zawartości żądania
Wszystkie żądania interfejsu API muszą mieć **typ zawartości**.

W przypadku **EventGridSchema** lub **CustomSchema**wartość Content-Type może być jedną z następujących wartości:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

W przypadku **CloudEventSchemaV1_0** w trybie strukturalnym wartość Content-Type może być jedną z następujących wartości:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

W przypadku **CloudEventSchemaV1_0** w trybie binarnym, zapoznaj się z [dokumentacją,](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) aby uzyskać szczegółowe informacje.

### <a name="error-response"></a>Odpowiedź na błąd
Wszystkie interfejsy API zwracają błąd z następującym ładunkiem:

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>Zarządzanie tematami

### <a name="put-topic-create--update"></a>Umieść temat (tworzenie / aktualizacja)

**Prośba**:``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Ładowność**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Odpowiedź**: HTTP 200

**Ładowność**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>Pobierz temat

**Prośba**:``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Odpowiedź**: HTTP 200

**Ładowność**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>Uzyskaj wszystkie tematy

**Prośba**:``` GET /topics?api-version=2019-01-01-preview ```

**Odpowiedź**: HTTP 200

**Ładowność**:
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>Usuń temat

**Prośba**:``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Odpowiedź**: HTTP 200, pusty ładunek

## <a name="manage-event-subscriptions"></a>Zarządzanie subskrypcjami zdarzeń
Przykłady w tej `EndpointType=Webhook;`sekcji użyć . Próbki json `EndpointType=EdgeHub / EndpointType=EventGrid` znajdują się w następnej sekcji. 

### <a name="put-event-subscription-create--update"></a>Umieść subskrypcję zdarzeń (tworzenie / aktualizowanie)

**Prośba**:``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Ładowność**:
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**Odpowiedź**: HTTP 200

**Ładowność**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>Uzyskaj subskrypcję wydarzenia

**Prośba**:``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Odpowiedź**: HTTP 200

**Ładowność**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>Uzyskaj subskrypcje zdarzeń

**Prośba**:``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Odpowiedź**: HTTP 200

**Ładowność**:
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>Usuwanie subskrypcji zdarzeń

**Prośba**:``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Odpowiedź**: HTTP 200, bez ładunku


## <a name="publish-events-api"></a>Interfejs API publikowania zdarzeń

### <a name="send-batch-of-events-in-event-grid-schema"></a>Wyślij partię zdarzeń (w schemacie siatki zdarzeń)

**Prośba**:``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**Odpowiedź**: HTTP 200, pusty ładunek


**Opisy pól ładunku**
- ```Id```jest obowiązkowe. Może to być dowolna wartość ciągu, która jest wypełniona przez wywołującego. Usługa Event Grid NIE wykrywa duplikatów ani nie wymusza żadnych semantyki w tym polu.
- ```Topic```jest opcjonalna, ale jeśli określono, musi być zgodna z topic_name z adresu URL żądania
- ```Subject```jest obowiązkowe, może być dowolną wartością ciągu
- ```EventType```jest obowiązkowe, może być dowolną wartością ciągu
- ```EventTime```jest obowiązkowe, nie jest weryfikowany, ale powinien być odpowiedni DateTime.
- ```DataVersion```jest obowiązkowe
- ```MetadataVersion```jest opcjonalny, jeśli określono, że musi być ciągiem z wartością```"1"```
- ```Data```jest opcjonalny i może być dowolny token JSON (liczba, ciąg, logiczny, tablica, obiekt)

### <a name="send-batch-of-events-in-custom-schema"></a>Wysyłanie partii zdarzeń (w schemacie niestandardowym)

**Prośba**:``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Odpowiedź**: HTTP 200, pusty ładunek


**Ograniczenia dotyczące ładowności**
- MUSI być tablicą zdarzeń.
- Każdy wpis tablicy MUSI być obiektem JSON.
- Żadne inne ograniczenia (inne niż rozmiar ładunku).

## <a name="examples"></a>Przykłady

### <a name="set-up-topic-with-eventgrid-schema"></a>Konfigurowanie tematu za pomocą schematu EventGrid
Konfiguruje temat, aby wymagać publikowania zdarzeń w **eventgridschema**.

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Konfigurowanie tematu ze schematem niestandardowym
Konfiguruje temat, aby wymagać `customschema`publikowania zdarzeń w pliku .

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Konfigurowanie tematu za pomocą schematu zdarzeń w chmurze
Konfiguruje temat, aby wymagać `cloudeventschema`publikowania zdarzeń w pliku .

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>Konfigurowanie webhook jako miejsca docelowego, zdarzenia, które mają być dostarczane w eventgridschema
Ten typ miejsca docelowego służy do wysyłania zdarzeń do dowolnego innego modułu (który obsługuje punkt końcowy HTTP) lub do dowolnego punktu końcowego adresowalny HTTP w sieci/Internecie.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

Ograniczenia atrybutu: `endpointUrl`
- Musi być bez wartości null.
- Musi to być bezwzględny adres URL.
- Jeśli outbound__webhook__httpsOnly jest ustawiona na true w ustawieniach EventGridModule, musi być tylko HTTPS.
- Jeśli outbound__webhook__httpsOnly ustawiona na false, może to być HTTP lub HTTPS.

Ograniczenia dotyczące `eventDeliverySchema` właściwości:
- Musi być zgodny ze schematem wejściowym tematu subskrybowania.
- Może być null. Domyślnie schemat wejściowy tematu.

### <a name="set-up-iot-edge-as-destination"></a>Konfigurowanie ioT Edge jako miejsca docelowego

Użyj tego miejsca docelowego, aby wysłać zdarzenia do usługi IoT Edge Hub i zostać poddane podsystemowi routingu/filtrowania/przekazywania koncentratora usługi Edge Hub.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Konfigurowanie chmury usługi Event Grid jako miejsca docelowego

Użyj tego miejsca docelowego, aby wysłać zdarzenia do siatki zdarzeń w chmurze (Azure). Przed utworzeniem subskrypcji zdarzeń na krawędzi należy najpierw skonfigurować temat użytkownika w chmurze, do którego zdarzenia powinny być wysyłane.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

Punkt końcowyUrl:
- Musi być bez wartości null.
- Musi to być bezwzględny adres URL.
- Ścieżka `/api/events` musi być zdefiniowana w ścieżce adresu URL żądania.
- Musi mieć `api-version=2018-01-01` w ciągu zapytania.
- Jeśli outbound__eventgrid__httpsOnly jest ustawiona na true w ustawieniach EventGridModule (domyślnie true), musi być tylko HTTPS.
- Jeśli outbound__eventgrid__httpsOnly jest ustawiona na false, może to być HTTP lub HTTPS.
- Jeśli outbound__eventgrid__allowInvalidHostnames jest ustawiona na false (false domyślnie), musi być skierowany do jednego z następujących punktów końcowych:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Musi być bez wartości null.

Nazwa tematu:
- Jeśli Subscription.EventDeliverySchema jest ustawiona na EventGridSchema, wartość z tego pola jest umieszczana w polu tematu każdego zdarzenia przed przekazaniem do siatki zdarzeń w chmurze.
- Jeśli Subscription.EventDeliverySchema jest ustawiona na CustomEventSchema, ta właściwość jest ignorowana i ładunek zdarzenia niestandardowego jest przekazytywnie dokładnie tak, jak zostało odebrane.

## <a name="set-up-event-hubs-as-a-destination"></a>Konfigurowanie centrów zdarzeń jako miejsca docelowego

Aby opublikować w Centrum `endpointType` zdarzeń, ustaw do `eventHub` i podaj:

* connectionString: Parametry połączenia dla określonego centrum zdarzeń, na które kierowane są reklamy generowane za pośrednictwem zasad dostępu współdzielonego.

    >[!NOTE]
    > Parametry połączenia muszą być specyficzne dla jednostki. Użycie ciągu połączenia obszaru nazw nie będzie działać. Można wygenerować ciąg połączenia określonej jednostki, przechodząc do określonego Centrum zdarzeń, które chcesz opublikować w witrynie Azure Portal i klikając pozycję **Zasady dostępu współdzielonego w** celu wygenerowania nowego ciągu connecection określonego przez jednostkę.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>Konfigurowanie kolejek usługi Service Bus jako miejsca docelowego

Aby opublikować w kolejce `endpointType` usługi `serviceBusQueue` Service Bus, ustaw do i podaj:

* connectionString: Parametry połączenia dla określonej kolejki magistrali usług, na którą kierowano, generowane za pośrednictwem zasad dostępu współdzielonego.

    >[!NOTE]
    > Parametry połączenia muszą być specyficzne dla jednostki. Użycie ciągu połączenia obszaru nazw nie będzie działać. Generowanie ciągu połączenia określonego encji przez przejście do określonej kolejki usługi Service Bus, które chcesz opublikować w witrynie Azure Portal i kliknięcie ikon **dostępu udostępnionego** w celu wygenerowania nowego ciągu connecection określonego przez jednostkę.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>Konfigurowanie tematów usługi Service Bus jako miejsca docelowego

Aby opublikować w temacie usługi `endpointType` `serviceBusTopic` Service Bus, ustaw do i podaj:

* connectionString: Parametry połączenia dla określonego tematu usługi Service Bus, na który kierowano generowane za pośrednictwem zasad dostępu współdzielonego.

    >[!NOTE]
    > Parametry połączenia muszą być specyficzne dla jednostki. Użycie ciągu połączenia obszaru nazw nie będzie działać. Generowanie ciągu połączenia określonego encji przez przejście do określonego tematu usługi Service Bus, który chcesz opublikować w witrynie Azure Portal i kliknięcie ikon **dostępu udostępnionego** w celu wygenerowania nowego ciągu connecection określonego przez jednostkę.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>Konfigurowanie kolejek magazynu jako miejsca docelowego

Aby opublikować w kolejce `endpointType` `storageQueue` magazynu, ustaw do i podaj:

* nazwa queueName: Nazwa kolejki magazynu, do której publikujesz.
* connectionString: Parametry połączenia dla konta magazynu, w które znajduje się kolejka magazynu.

    >[!NOTE]
    > Unline Event Hubs, Kolejki magistrali usług i Tematy usługi Service Bus, ciąg połączenia używany dla kolejek magazynu nie jest specyficzny dla encji. Zamiast tego musi, ale parametry połączenia dla konta magazynu.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```