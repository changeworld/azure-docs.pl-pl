---
title: Publikowanie, subskrybowanie zdarzeń w chmurze — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Publikowanie, subskrybowanie zdarzeń w chmurze przy użyciu elementu Webhook z siatką zdarzeń w usłudze IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c82f1edfc3acd73c1d38425f963aaaf2976a1cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844597"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Samouczek: Publikowanie, subskrybowanie zdarzeń w chmurze

W tym artykule określ wszystkie kroki potrzebne do publikowania i subskrybowania zdarzeń przy użyciu usługi Event Grid w umyw usługi IoT Edge. W tym samouczku użyto funkcji platformy Azure jako program obsługi zdarzeń. Aby uzyskać dodatkowe typy miejsc docelowych, zobacz [programy obsługi zdarzeń](event-handlers.md).

Zobacz [Pojęcia siatki zdarzeń,](concepts.md) aby zrozumieć, co to jest temat siatki zdarzeń i subskrypcja przed kontynuowaniem.

## <a name="prerequisites"></a>Wymagania wstępne 
Aby ukończyć ten samouczek, trzeba będzie:

* **Subskrypcja platformy Azure** — utwórz [bezpłatne konto,](https://azure.microsoft.com/free) jeśli jeszcze go nie masz. 
* **Usługa Azure IoT Hub i urządzenie usługi IoT Edge** — postępuj zgodnie z instrukcjami w trybie szybkiego [uruchamiania](../../iot-edge/quickstart-linux.md) dla urządzeń z systemem Linux lub [Windows,](../../iot-edge/quickstart.md) jeśli jeszcze go nie masz.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Tworzenie funkcji platformy Azure w witrynie Azure portal

Wykonaj kroki opisane w [samouczku,](../../azure-functions/functions-create-first-azure-function.md) aby utworzyć funkcję platformy Azure. 

Zastąp fragment kodu następującym kodem:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

W nowej funkcji wybierz pozycję **Pobierz adres URL funkcji** w prawym górnym rogu, wybierz domyślny **(klawisz funkcyjny),** a następnie wybierz pozycję **Kopiuj**. Użyjesz funkcji wartość adresu URL w dalszej części samouczka.

> [!NOTE]
> Zapoznaj się z dokumentacją [usługi Azure Functions,](../../azure-functions/functions-overview.md) aby uzyskać więcej przykładów i samouczków dotyczących reagowania na zdarzenia przy użyciu wyzwalaczy zdarzeń EventGrid.

## <a name="create-a-topic"></a>Tworzenie tematu

Jako wydawca zdarzenia musisz utworzyć temat siatki zdarzeń. Temat odnosi się do punktu końcowego, do którego wydawcy mogą wysyłać zdarzenia.

1. Utwórz topic2.json z następującą zawartością. Szczegółowe informacje na temat ładunku można znaleźć w dokumentacji interfejsu [API.](api.md)

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Uruchom następujące polecenie, aby utworzyć temat. Kod stanu HTTP 200 OK powinny być zwracane.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. Uruchom następujące polecenie, aby sprawdzić, czy temat został utworzony pomyślnie. Kod stanu HTTP 200 OK powinny być zwracane.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   Przykładowe dane wyjściowe:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Tworzenie subskrypcji zdarzeń

Subskrybenci mogą rejestrować się w przypadku zdarzeń opublikowanych w temacie. Aby otrzymać dowolne zdarzenie, subskrybenci będą musieli utworzyć subskrypcję siatki zdarzeń na interesujący temat.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Utwórz plik subscription2.json z następującą zawartością. Szczegółowe informacje na temat ładunku można znaleźć w dokumentacji interfejsu [API.](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > Typ **punktu końcowego** określa, że subskrybent jest elementem Webhook.  **EndpointUrl** określa adres URL, pod którym subskrybent nasłuchuje zdarzeń. Ten adres URL odpowiada przykładowi funkcji platformy Azure, który został wcześniej skonfigurowany.
2. Uruchom następujące polecenie, aby utworzyć subskrypcję. Kod stanu HTTP 200 OK powinny być zwracane.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. Uruchom następujące polecenie, aby sprawdzić subskrypcję został utworzony pomyślnie. Kod stanu HTTP 200 OK powinny być zwracane.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    Przykładowe dane wyjściowe:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Publikowanie wydarzenia

1. Utwórz plik event2.json z następującą zawartością. Szczegółowe informacje na temat ładunku można znaleźć w dokumentacji interfejsu [API.](api.md)

    ```json
        [
          {
            "id": "eventId-func-1",
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
1. Uruchom następujące polecenie, aby opublikować zdarzenie

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Weryfikowanie dostarczania zdarzeń

Można wyświetlić zdarzenie dostarczone w witrynie Azure portal w obszarze **Monitor** opcji funkcji.

## <a name="cleanup-resources"></a>Oczyszczanie zasobów

* Uruchom następujące polecenie, aby usunąć temat i wszystkie jego subskrypcje

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Usuń funkcję platformy Azure utworzoną w witrynie Azure portal.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono temat siatki zdarzeń, subskrypcję i opublikowane zdarzenia. Teraz, gdy znasz podstawowe kroki, zobacz następujące artykuły:

* Aby rozwiązać problemy z używaniem usługi Azure Event Grid w usłudze IoT Edge, zobacz [Przewodnik rozwiązywania problemów](troubleshoot.md).
* Tworzenie/aktualizowanie subskrypcji za pomocą [filtrów](advanced-filtering.md).
* Konfigurowanie trwałości modułu Siatki zdarzeń w [systemie Linux](persist-state-linux.md) lub [Windows](persist-state-windows.md)
* Postępuj zgodnie [z dokumentacją,](configure-client-auth.md) aby skonfigurować uwierzytelnianie klienta
* Przesyłanie dalej zdarzeń do usługi Azure Event Grid w chmurze, wykonując ten [samouczek](forward-events-event-grid-cloud.md)
* [Monitorowanie tematów i subskrypcji na krawędzi](monitor-topics-subscriptions.md)
