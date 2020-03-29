---
title: Zdarzenia krawędzi do przodu do chmury usługi Event Grid — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Zdarzenia krawędzi przesyłania dalej do chmury usługi Event Grid
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7184fb5c45ce41de2bd63b55fb67cbd9ba6361e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844721"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Samouczek: Przesyłanie dalej zdarzeń do chmury usługi Event Grid

W tym artykule przedstawiono wszystkie kroki potrzebne do przekazania zdarzeń brzegowych do siatki zdarzeń w chmurze platformy Azure. Można to zrobić z następujących powodów:

* Reaguj na zdarzenia brzegowe w chmurze.
* Przesyłaj dalej zdarzenia do siatki zdarzeń w chmurze i korzystaj z usług Azure Event Hubs lub kolejek usługi Azure Storage w celu buforowania zdarzeń przed przetworzeniem ich w chmurze.

 Aby ukończyć ten samouczek, musisz mieć wiedzę na temat koncepcji usługi Event Grid na [platformie Edge](concepts.md) i [azure.](../concepts.md) Aby uzyskać dodatkowe typy miejsc docelowych, zobacz [programy obsługi zdarzeń](event-handlers.md). 

## <a name="prerequisites"></a>Wymagania wstępne 
Aby ukończyć ten samouczek, trzeba będzie:

* **Subskrypcja platformy Azure** — utwórz [bezpłatne konto,](https://azure.microsoft.com/free) jeśli jeszcze go nie masz. 
* **Usługa Azure IoT Hub i urządzenie usługi IoT Edge** — postępuj zgodnie z instrukcjami w trybie szybkiego [uruchamiania](../../iot-edge/quickstart-linux.md) dla urządzeń z systemem Linux lub [Windows,](../../iot-edge/quickstart.md) jeśli jeszcze go nie masz.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Tworzenie tematu i subskrypcji siatki zdarzeń w chmurze

Utwórz temat siatki zdarzeń i subskrypcji w chmurze, wykonując [ten samouczek](../custom-event-quickstart-portal.md). Zanotuj w `topicURL` `sasKey`dół , i `topicName` nowo utworzonego tematu, który będzie używany w dalszej części samouczka.

Na przykład jeśli utworzono `testegcloudtopic` temat o nazwie w zachodnie stany USA, wartości będą wyglądać mniej więcej tak:

* **TopicUrl**:`https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **Nazwa tematu**:`testegcloudtopic`
* **SasKey**: Dostępne pod **AccessKey** tematu. Użyj **klawisza1**.

## <a name="create-event-grid-topic-at-the-edge"></a>Tworzenie tematu siatki zdarzeń na krawędzi

1. Utwórz topic3.json z następującą zawartością. Szczegółowe informacje na temat ładunku można znaleźć w dokumentacji interfejsu [API.](api.md)

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Uruchom następujące polecenie, aby utworzyć temat. Kod stanu HTTP 200 OK powinny być zwracane.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. Uruchom następujące polecenie, aby sprawdzić, czy temat został utworzony pomyślnie. Kod stanu HTTP 200 OK powinny być zwracane.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   Przykładowe dane wyjściowe:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Tworzenie subskrypcji usługi Event Grid na krawędzi

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Utwórz plik subscription3.json z następującą zawartością. Szczegółowe informacje na temat ładunku można znaleźć w dokumentacji interfejsu [API.](api.md)

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

   >[!NOTE]
   > **EndpointUrl** określa, że adres URL tematu siatki zdarzeń w chmurze. **SasKey** odnosi się do klucza chmury usługi Event Grid. Wartość w **topicName** będzie używana do stemplowania wszystkich zdarzeń wychodzących do siatki zdarzeń. Może to być przydatne podczas publikowania w temacie domeny siatki zdarzeń. Aby uzyskać więcej informacji na temat domeny siatki zdarzeń, zobacz [Domeny zdarzeń](../event-domains.md)

    Na przykład:
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. Uruchom następujące polecenie, aby utworzyć subskrypcję. Kod stanu HTTP 200 OK powinny być zwracane.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. Uruchom następujące polecenie, aby sprawdzić subskrypcję został utworzony pomyślnie. Kod stanu HTTP 200 OK powinny być zwracane.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    Przykładowe dane wyjściowe:

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>Publikowanie wydarzenia na krawędzi

1. Utwórz plik event3.json z następującą zawartością. Zobacz [dokumentację interfejsu API,](api.md) aby uzyskać szczegółowe informacje na temat ładunku.

    ```json
        [
          {
            "id": "eventId-egcloud-0",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```

1. Uruchom następujące polecenie:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>Weryfikowanie zdarzenia krawędzi w chmurze

Aby uzyskać informacje na temat wyświetlania zdarzeń dostarczanych przez temat chmury, zobacz [samouczek](../custom-event-quickstart-portal.md).

## <a name="cleanup-resources"></a>Oczyszczanie zasobów

* Uruchom następujące polecenie, aby usunąć temat i wszystkie jego subskrypcje

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* Usuń również temat i subskrypcje utworzone w chmurze (usługa Azure Event Grid).

## <a name="next-steps"></a>Następne kroki

W tym samouczku opublikowano zdarzenie na krawędzi i przekazywane do usługi Event Grid w chmurze platformy Azure. Teraz, gdy znasz podstawowe kroki, aby przejść do usługi Event Grid w chmurze:

* Aby rozwiązać problemy z używaniem usługi Azure Event Grid w usłudze IoT Edge, zobacz [Przewodnik rozwiązywania problemów](troubleshoot.md).
* Przesyłaj dalej zdarzenia do usługi IoTHub, wykonując ten [samouczek](forward-events-iothub.md)
* Przesyłanie dalej zdarzeń do elementu Webhook w chmurze, wykonując ten [samouczek](pub-sub-events-webhook-cloud.md)
* [Monitorowanie tematów i subskrypcji na krawędzi](monitor-topics-subscriptions.md)
