---
title: Przesyłanie dalej zdarzeń siatki do usługi IoTHub — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Przesyłanie dalej zdarzeń siatki zdarzeń do usługi IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d0034810ff86de2a40e275ca54a2f0f9cbc856c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844704"
---
# <a name="tutorial-forward-events-to-iothub"></a>Samouczek: Przesyłanie dalej zdarzeń do usługi IoTHub

W tym artykule przedstawiono wszystkie kroki potrzebne do przekazywania zdarzeń usługi Event Grid do innych modułów usługi IoT Edge, IoTHub przy użyciu tras. Można to zrobić z następujących powodów:

* Kontynuuj korzystanie z istniejących inwestycji już istniejących z routingiem edgeHub
* Preferuj kierowanie wszystkich zdarzeń z urządzenia tylko za pośrednictwem usługi IoT Hub

Aby ukończyć ten samouczek, musisz zrozumieć następujące pojęcia:

- [Pojęcia siatki zdarzeń](concepts.md)
- [Koncentrator usługi IoT Edge](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Wymagania wstępne 
Aby ukończyć ten samouczek, trzeba będzie:

* **Subskrypcja platformy Azure** — utwórz [bezpłatne konto,](https://azure.microsoft.com/free) jeśli jeszcze go nie masz. 
* **Usługa Azure IoT Hub i urządzenie usługi IoT Edge** — postępuj zgodnie z instrukcjami w trybie szybkiego [uruchamiania](../../iot-edge/quickstart-linux.md) dla urządzeń z systemem Linux lub [Windows,](../../iot-edge/quickstart.md) jeśli jeszcze go nie masz.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Tworzenie tematu

Jako wydawca zdarzenia musisz utworzyć temat siatki zdarzeń. Temat odnosi się do punktu końcowego, do którego wydawcy mogą następnie wysyłać zdarzenia.

1. Utwórz topic4.json z następującą zawartością. Szczegółowe informacje na temat ładunku można znaleźć w dokumentacji interfejsu [API.](api.md)

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Uruchom następujące polecenie, aby utworzyć temat. Kod stanu HTTP 200 OK powinny być zwracane.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Uruchom następujące polecenie, aby sprawdzić, czy temat został utworzony pomyślnie. Kod stanu HTTP 200 OK powinny być zwracane.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   Przykładowe dane wyjściowe:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>Tworzenie subskrypcji zdarzeń

Subskrybenci mogą rejestrować się w przypadku zdarzeń opublikowanych w temacie. Aby otrzymać dowolne zdarzenie, należy utworzyć subskrypcję siatki zdarzeń na interesujący temat.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Utwórz subscription4.json z poniższą zawartością. Szczegółowe informacje na temat ładunku można znaleźć w dokumentacji interfejsu [API.](api.md)

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

   >[!NOTE]
   > Określa, `endpointType` że subskrybent jest `edgeHub`. Określa `outputName` dane wyjściowe, na których moduł siatki zdarzeń będzie kierować zdarzenia, które pasują do tej subskrypcji edgeHub. Na przykład zdarzenia, które pasują do powyższej subskrypcji zostaną zapisane na `/messages/modules/eventgridmodule/outputs/sampleSub4`.
2. Uruchom następujące polecenie, aby utworzyć subskrypcję. Kod stanu HTTP 200 OK powinny być zwracane.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Uruchom następujące polecenie, aby sprawdzić subskrypcję został utworzony pomyślnie. Kod stanu HTTP 200 OK powinny być zwracane.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    Przykładowe dane wyjściowe:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>Konfigurowanie trasy koncentratora krawędzi

Zaktualizuj trasę centrum brzegowego, aby przesłać dalej zdarzenia subskrypcji zdarzeń, które mają być przekazywane do usługi IoTHub w następujący sposób:

1. Logowanie się do [witryny Azure portal](https://ms.portal.azure.com)
1. Przejdź do **centrum IoT Hub**.
1. Wybierz **IoT Edge** z menu
1. Wybierz identyfikator urządzenia docelowego z listy urządzeń.
1. Wybierz pozycję **Ustaw moduły**.
1. Wybierz **pozycję Dalej** i do sekcji trasy.
1. Na trasach dodaj nową trasę

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  Na przykład:

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > Powyższa trasa będzie przesyłać dalej wszystkie zdarzenia dopasowane dla tej subskrypcji, które mają być przekazywane do centrum IoT hub. Za pomocą funkcji [routingu koncentratora krawędzi](../../iot-edge/module-composition.md) można użyć do dalszego filtrowania i kierowania zdarzeń siatki zdarzeń do innych modułów usługi IoT Edge.

## <a name="setup-iot-hub-route"></a>Konfigurowanie trasy Centrum IoT

Zobacz [samouczek routingu usługi IoT Hub,](../../iot-hub/tutorial-routing.md) aby skonfigurować trasę z centrum IoT hub, dzięki czemu można wyświetlać zdarzenia przesyłane dalej z modułu siatki zdarzeń. Użyj `true` kwerendy, aby zachować samouczek proste.  



## <a name="publish-an-event"></a>Publikowanie wydarzenia

1. Utwórz plik event4.json z następującą zawartością. Szczegółowe informacje na temat ładunku można znaleźć w dokumentacji interfejsu [API.](api.md)

    ```json
        [
          {
            "id": "eventId-iothub-1",
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

1. Uruchom następujące polecenie, aby opublikować zdarzenie:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Weryfikowanie dostarczania zdarzeń

Zobacz [samouczek routingu](../../iot-hub/tutorial-routing.md) usługi IoT Hub, aby uzyskać instrukcje wyświetlania zdarzeń.

## <a name="cleanup-resources"></a>Oczyszczanie zasobów

* Uruchom następujące polecenie, aby usunąć temat i wszystkie jego subskrypcje na krawędzi:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Usuń wszystkie zasoby utworzone podczas konfigurowania routingu Usługi IoTHub również w chmurze.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono temat siatki zdarzeń, subskrypcję centrum krawędzi i opublikowane zdarzenia. Teraz, gdy znasz podstawowe kroki, aby przejść do koncentratora krawędzi, zobacz następujące artykuły:

* Aby rozwiązać problemy z używaniem usługi Azure Event Grid w usłudze IoT Edge, zobacz [Przewodnik rozwiązywania problemów](troubleshoot.md).
* Używanie filtrów trasy [koncentratora krawędzi](../../iot-edge/module-composition.md) do partycjonowania zdarzeń
* Konfigurowanie trwałości modułu Siatki zdarzeń w [systemie Linux](persist-state-linux.md) lub [Windows](persist-state-windows.md)
* Postępuj zgodnie [z dokumentacją,](configure-client-auth.md) aby skonfigurować uwierzytelnianie klienta
* Przesyłanie dalej zdarzeń do usługi Azure Event Grid w chmurze, wykonując ten [samouczek](forward-events-event-grid-cloud.md)
* [Monitorowanie tematów i subskrypcji na krawędzi](monitor-topics-subscriptions.md)