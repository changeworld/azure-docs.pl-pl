---
title: Publikowanie, subskrybowanie zdarzeń lokalnie — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Publikowanie, subskrybowanie zdarzeń lokalnie przy użyciu elementu Webhook z siatką zdarzeń w uktoreniu usługi IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba82b1bea4753cd51e275a78b248247032d79a01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281004"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>Samouczek: Publikowanie, subskrybowanie wydarzeń lokalnie

W tym artykule otrzymasz od wszystkich kroków potrzebnych do publikowania i subskrybowania zdarzeń przy użyciu usługi Event Grid w umyw usługi IoT Edge.

> [!NOTE]
> Aby dowiedzieć się więcej o tematach i subskrypcjach usługi Azure Event Grid, zobacz [Pojęcia dotyczące siatki zdarzeń](concepts.md).

## <a name="prerequisites"></a>Wymagania wstępne 
Aby ukończyć ten samouczek, trzeba będzie:

* **Subskrypcja platformy Azure** — utwórz [bezpłatne konto,](https://azure.microsoft.com/free) jeśli jeszcze go nie masz. 
* **Usługa Azure IoT Hub i urządzenie usługi IoT Edge** — postępuj zgodnie z instrukcjami w przewodniku Szybki start dla urządzeń [z systemem Linux](../../iot-edge/quickstart-linux.md) lub [Windows,](../../iot-edge/quickstart.md) jeśli jeszcze go nie masz.

## <a name="deploy-event-grid-iot-edge-module"></a>Wdrażanie modułu IoT Edge w siatce zdarzeń

Istnieje kilka sposobów wdrażania modułów na urządzeniu usługi IoT Edge i wszystkie z nich działają w usłudze Azure Event Grid w usłudze IoT Edge. W tym artykule opisano kroki wdrażania usługi Event Grid w usłudze IoT Edge z witryny Azure portal.

>[!NOTE]
> W tym samouczku wdrożysz moduł siatki zdarzeń bez trwałości. Oznacza to, że wszystkie tematy i subskrypcje utworzone w tym samouczku zostaną usunięte po przemieszczeniu modułu. Aby uzyskać więcej informacji na temat konfigurowania trwałości, zobacz następujące artykuły: [Utrwalić stan w systemie Linux](persist-state-linux.md) lub Persist state w systemie [Windows](persist-state-windows.md). W przypadku obciążeń produkcyjnych zaleca się zainstalowanie modułu event grid z trwałością.


### <a name="select-your-iot-edge-device"></a>Wybieranie urządzenia IoT Edge

1. Logowanie się do [witryny Azure portal](https://portal.azure.com)
1. Przejdź do centrum IoT Hub.
1. Wybierz pozycję **IoT Edge** z menu w sekcji **Automatyczne zarządzanie urządzeniami.** 
1. Kliknij identyfikator urządzenia docelowego z listy urządzeń
1. Wybierz pozycję **Ustaw moduły**. Zachowaj otwartą stronę. Będziesz kontynuować kroki w następnej sekcji.

### <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrażania

Manifest wdrożenia to dokument JSON, który opisuje, które moduły do wdrożenia, jak przepływy danych między modułami i żądane właściwości bliźniaczych reprezentacji modułu. Portal Azure ma kreatora, który przeprowadzi Cię przez tworzenie manifestu wdrożenia, zamiast ręcznego tworzenia dokumentu JSON.  Ma trzy kroki: **Dodaj moduły,** **Określ trasy**i **Przejrzyj wdrożenie**.

### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **Moduły wdrażania** wybierz pozycję **Dodaj**
1. Z typów modułów z listy rozwijanej wybierz opcję **Moduł krawędzi IoT**
1. Podaj nazwę, obraz, opcje tworzenia kontenera:

   * **Nazwa**: eventgridmodule
   * **Identyfikator URI obrazu**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opcje tworzenia kontenera:**

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. Kliknij **przycisk Zapisz**
 1. Przejdź do następnej sekcji, aby dodać moduł subskrybenta usługi Azure Event Grid przed ich wdrożeniem razem.

    >[!IMPORTANT]
    > W tym samouczku wdrożysz moduł siatki zdarzeń z wyłączonym uwierzytelnianiem klienta. W przypadku obciążeń produkcyjnych zaleca się włączenie uwierzytelniania klienta. Aby uzyskać więcej informacji na temat bezpiecznego konfigurowania modułu event grid, zobacz [Zabezpieczenia i uwierzytelnianie](security-authentication.md).
    > 
    > Jeśli używasz maszyny Wirtualnej platformy Azure jako urządzenia brzegowego, dodaj regułę portu przychodzącego, aby zezwolić na ruch przychodzący na porcie 4438. Aby uzyskać instrukcje dotyczące dodawania reguły, zobacz [Jak otworzyć porty na maszynie Wirtualnej](../../virtual-machines/windows/nsg-quickstart-portal.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Wdrażanie modułu usługi IoT Edge subskrybenta usługi Event Grid

W tej sekcji pokazano, jak wdrożyć inny moduł IoT, który będzie działać jako program obsługi zdarzeń, do których zdarzenia mogą być dostarczane.

### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **Moduły wdrażania** wybierz pozycję **Dodaj** ponownie. 
1. Z typów modułów z listy rozwijanej wybierz opcję **Moduł krawędzi IoT**
1. Podaj opcje tworzenia nazwy, obrazu i kontenera kontenera:

   * **Nazwa**: subskrybent
   * **Identyfikator URI obrazu**:`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Opcje tworzenia kontenera:** Brak
1. Kliknij **przycisk Zapisz**
1. Kliknij **przycisk Dalej,** aby przejść do sekcji Trasy

 ### <a name="setup-routes"></a>Ustawianie tras

Zachowaj trasy domyślne, a następnie wybierz pozycję **Dalej,** aby przejść do sekcji recenzji

### <a name="submit-the-deployment-request"></a>Prześlij żądanie wdrożenia

1. Sekcja przeglądu zawiera manifest wdrożenia JSON, który został utworzony na podstawie wybranych w poprzedniej sekcji. Upewnij się, że widzisz oba moduły: **eventgridmodule** i **subskrybenta** wymienione w JSON. 
1. Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Prześlij**. Po przesłaniu wdrożenia powrócisz do strony **urządzenia.**
1. W **sekcji Moduły**sprawdź, czy są wymienione zarówno moduły **eventgrid,** jak i **subskrybator.** Sprawdź, czy kolumny **Określone we wdrożeniu** i **Zgłoszone przez urządzenie** są ustawione na **Tak**.

    Może upłynąć kilka chwil, aby moduł został uruchomiony na urządzeniu, a następnie zgłoszony z powrotem do usługi IoT Hub. Odśwież stronę, aby wyświetlić zaktualizowany stan.

## <a name="create-a-topic"></a>Tworzenie tematu

Jako wydawca zdarzenia musisz utworzyć temat siatki zdarzeń. W usłudze Azure Event Grid temat odnosi się do punktu końcowego, do którego wydawcy mogą wysyłać zdarzenia.

1. Utwórz topic.json z następującą zawartością. Szczegółowe informacje na temat ładunku można znaleźć w [dokumentacji interfejsu API.](api.md)

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. Uruchom następujące polecenie, aby utworzyć temat siatki zdarzeń. Upewnij się, że widzisz `200 OK`kod stanu HTTP jest .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. Uruchom następujące polecenie, aby sprawdzić, czy temat został utworzony pomyślnie. Kod stanu HTTP 200 OK powinny być zwracane.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   Przykładowe dane wyjściowe:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Tworzenie subskrypcji zdarzeń

Subskrybenci mogą rejestrować się w przypadku zdarzeń opublikowanych w temacie. Aby otrzymać dowolne zdarzenie, musisz utworzyć subskrypcję usługi Event Grid dla interesującego tematu.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Utwórz plik subscription.json z następującą zawartością. Szczegółowe informacje na temat ładunku można znaleźć w [dokumentacji interfejsu API](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > Właściwość **endpointType** określa, że subskrybent jest elementem **Webhook**.  **EndpointUrl** określa adres URL, pod którym subskrybent nasłuchuje zdarzeń. Ten adres URL odpowiada przykładowi subskrybenta platformy Azure wdrożonym wcześniej.
2. Uruchom następujące polecenie, aby utworzyć subskrypcję dla tematu. Upewnij się, że widzisz `200 OK`kod stanu HTTP jest .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. Uruchom następujące polecenie, aby sprawdzić subskrypcję został utworzony pomyślnie. Kod stanu HTTP 200 OK powinny być zwracane.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    Przykładowe dane wyjściowe:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Publikowanie wydarzenia

1. Utwórz event.json z następującą zawartością. Szczegółowe informacje na temat ładunku można znaleźć w [dokumentacji interfejsu API.](api.md)

    ```json
        [
          {
            "id": "eventId-func-0",
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
1. Uruchom następujące polecenie, aby opublikować zdarzenie.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Weryfikowanie dostarczania zdarzeń

1. SSH lub RDP do maszyny wirtualnej IoT Edge.
1. Sprawdź dzienniki subskrybenta:

    W systemie Windows uruchom następujące polecenie:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   W systemie Linux uruchom następujące polecenie:

    ```sh
    sudo docker logs subscriber
    ```

    Przykładowe dane wyjściowe:

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>Oczyszczanie zasobów

* Uruchom następujące polecenie, aby usunąć temat i wszystkie jego subskrypcje.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* Usuń moduł subskrybenta z urządzenia Usługi IoT Edge.


## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono temat siatki zdarzeń, subskrypcję i opublikowane zdarzenia. Teraz, gdy znasz podstawowe kroki, zobacz następujące artykuły: 

- Aby rozwiązać problemy z używaniem usługi Azure Event Grid w usłudze IoT Edge, zobacz [Przewodnik rozwiązywania problemów](troubleshoot.md).
- Tworzenie/aktualizowanie subskrypcji za pomocą [filtrów](advanced-filtering.md).
- Włącz trwałość modułu Siatki zdarzeń w [systemie Linux](persist-state-linux.md) lub [Windows](persist-state-windows.md)
- Postępuj zgodnie [z dokumentacją,](configure-client-auth.md) aby skonfigurować uwierzytelnianie klienta
- Przesyłanie dalej zdarzeń do usługi Azure Functions w chmurze, wykonując ten [samouczek](pub-sub-events-webhook-cloud.md)
- [Reagowanie na zdarzenia magazynu obiektów blob w usłudze IoT Edge](react-blob-storage-events-locally.md)
- [Monitorowanie tematów i subskrypcji na krawędzi](monitor-topics-subscriptions.md)

