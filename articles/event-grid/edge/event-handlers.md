---
title: Programy obsługi zdarzeń i miejsca docelowe — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Programy obsługi zdarzeń i miejsca docelowe w siatce zdarzeń na krawędzi
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 35bf5af90aa5f0456aa8d68f0e4e8aaacc6cf84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849749"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Programy obsługi zdarzeń i miejsca docelowe w siatce zdarzeń na krawędzi

Program obsługi zdarzeń jest miejscem, w którym zdarzenie do dalszych działań lub do przetwarzania zdarzenia. Za pomocą modułu Event Grid on Edge program obsługi zdarzeń może znajdować się na tym samym urządzeniu brzegowym, innym urządzeniu lub w chmurze. Można użyć dowolnego elementu WebHook do obsługi zdarzeń lub wysłać zdarzenia do jednego z programów obsługi natywnych, takich jak usługa Azure Event Grid.

Ten artykuł zawiera informacje na temat konfigurowania każdego z nich.

## <a name="webhook"></a>Element WebHook

Aby opublikować w punkcie końcowym elementu `endpointType` `WebHook` WebHook, ustaw do i podaj:

* endpointUrl: adres URL punktu końcowego elementu webhook

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure Event Grid

Aby opublikować w punkcie końcowym chmury `endpointType` zdarzeń `eventGrid` platformy Azure, ustaw do i podaj:

* endpointUrl: adres URL tematu siatki zdarzeń w chmurze
* sasKey: Klucz SAS tematu siatki zdarzeń
* topicName: Nazwa stemplowania wszystkich zdarzeń wychodzących do siatki zdarzeń. Nazwa tematu jest przydatna podczas publikowania w temacie Domena siatki zdarzeń.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                 "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
    }
   ```

## <a name="iot-edge-hub"></a>Koncentrator krawędzi IoT

Aby opublikować w module Koncentratora krawędzi, ustaw `endpointType` do `edgeHub` i podaj:

* outputName: dane wyjściowe, na których moduł siatki zdarzeń będzie kierować zdarzenia, które pasują do tej subskrypcji do edgeHub. Na przykład zdarzenia, które pasują do poniższej subskrypcji zostaną zapisane do /messages/modules/eventgridmodule/outputs/sampleSub4.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "edgeHub",
              "properties": {
                "outputName": "sampleSub4"
              }
            }
          }
        }
    ```

## <a name="event-hubs"></a>Usługa Event Hubs

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

## <a name="service-bus-queues"></a>Kolejki usługi Service Bus

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

## <a name="service-bus-topics"></a>Tematy dotyczące usługi Service Bus

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

## <a name="storage-queues"></a>Kolejki usługi Storage

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