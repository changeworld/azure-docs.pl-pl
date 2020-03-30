---
title: Bliźniaczy moduł — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Konfiguracja za pomocą modułu Twin.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5c23b9ef280a4a4e3458d279ecf060d2e3d50295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992147"
---
# <a name="module-twin-json-schema"></a>Podwójny schemat JSON modułu

Usługa Event Grid on IoT Edge integruje się z ekosystemem usługi IoT Edge i obsługuje tworzenie tematów i subskrypcji za pośrednictwem modułu Bliźniaczego. Raportuje również bieżący stan wszystkich tematów i subskrypcji zdarzeń do zgłoszonych właściwości na module Twin.

> [!WARNING]
> Ze względu na ograniczenia w ekosystemie usługi IoT Edge wszystkie elementy tablicy w poniższym przykładzie json zostały zakodowane jako ciągi json. Zobacz `EventSubscription.Filter.EventTypes` `EventSubscription.Filter.AdvancedFilters` i klucze w poniższym przykładzie.

## <a name="desired-properties-json"></a>Żądane właściwości JSON

* Wartość każdej pary klucz-wartość w sekcji tematów ma dokładnie ten sam schemat `Topic.Properties` JSON, który jest używany w interfejsie API podczas tworzenia tematów.
* Wartość każdej pary klucz-wartość w **eventSubscriptions** sekcji ma dokładnie ten sam schemat `EventSubscription.Properties` json, który jest używany w interfejsie API podczas tworzenia tematów.
* Aby usunąć temat, ustaw `null` jego wartość we właściwościach żądanych.
* Usuwanie subskrypcji zdarzeń za pośrednictwem żądanych właściwości nie jest obsługiwane.

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    }
}
```

## <a name="reported-properties-json"></a>Zgłoszone właściwości JSON

Zgłoszona sekcja właściwości bliźniaczej reprezentacji modułu zawiera następujące informacje:

* Zestaw tematów i subskrypcji, które istnieją w magazynie modułu
* Wszelkie błędy napotkane podczas tworzenia żądanych tematów/subskrypcji zdarzeń
* Wszelkie błędy rozruchu (takie jak żądane właściwości analizowania JSON nie powiodły się)

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    },
    "errors": {
        "bootupMessage": "",
        "bootupException": "",
        "topics": {},
        "eventSubscriptions": {
            "twinTopic1EventGridUserTopicSub": "HttpStatusCode='BadRequest' ErrorCode='InvalidDestination' Message='EventSubscription.Properties.Destination failed validation. Reason: EndpointUrl must target the /api/events API of Azure Event Grid in the cloud..'"
        }
    }
}
```
