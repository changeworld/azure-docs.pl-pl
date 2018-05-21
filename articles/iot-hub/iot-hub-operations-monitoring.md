---
title: Operacje platformy Azure IoT Hub monitorowania | Dokumentacja firmy Microsoft
description: Jak używać operacji centrum IoT Azure monitorowania do monitorowania stanu operacji w Centrum IoT w czasie rzeczywistym.
services: iot-hub
documentationcenter: ''
author: nberdy
manager: timlt
editor: ''
ms.assetid: a299f3a5-b14d-4586-9c3b-44aea14ed013
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: nberdy
ms.openlocfilehash: 98bc3896e68d8ddd694bbfd2bd0e1d41b3e35fc8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="iot-hub-operations-monitoring"></a>Monitorowanie operacji centrum IoT

Monitorowanie operacji centrum IoT umożliwia monitorowanie stanu operacji w Centrum IoT w czasie rzeczywistym. Centrum IoT śledzi zdarzenia przez różne kategorie działań. Można włączyć do wysyłania zdarzeń z co najmniej jednej kategorii do punktu końcowego Centrum IoT do przetwarzania. Możesz monitorować dane błędy lub konfigurowanie bardziej złożonych przetwarzania na podstawie wzorców danych.

>[!NOTE]
>Monitorowanie operacji centrum IoT jest przestarzała i zostanie usunięte z Centrum IoT na 10 października 2018. Do monitorowania operacji i kondycji Centrum IoT, zobacz [monitorowanie kondycji Azure IoT Hub i szybkie diagnozowanie problemów][lnk-monitor]. Aby uzyskać więcej informacji na temat osi czasu przestarzałą zobacz [monitorować rozwiązań Azure IoT z monitorem Azure oraz kondycja zasobów Azure][lnk-blog-announcement].

Centrum IoT monitoruje sześć kategorii zdarzeń:

* Operacje tożsamości urządzenia
* Telemetrii urządzenia
* Komunikaty chmury do urządzenia
* Połączenia
* Przekazywania plików
* Kierowanie komunikatów

> [!IMPORTANT]
> Monitorowanie operacji centrum IoT nie gwarantuje, niezawodne i uporządkowane dostarczanie zdarzeń. W zależności od podstawowej infrastruktury Centrum IoT niektóre zdarzenia mogą utracone lub dostarczony poza kolejnością. Użyj operacji monitorowanie, aby generować alerty oparte na sygnały błąd takich jak nieudanych prób połączenia lub rozłączeń wysokiej częstotliwości dla konkretnych urządzeń. Nie będą miały działań monitorowania zdarzeń, aby utworzyć spójne magazyn dla stanu urządzenia, np. Magazyn śledzenia połączone lub rozłączone stan urządzenia. 

## <a name="how-to-enable-operations-monitoring"></a>Jak włączyć operacje monitorowania

1. Tworzenie Centrum IoT. Instrukcje można znaleźć na temat sposobu tworzenia Centrum IoT w [wprowadzenie] [ lnk-get-started] przewodnik.

1. Otwarcie bloku Centrum IoT. Z tego miejsca, kliknij przycisk **operacje monitorowania**.

    ![Operacje związane z dostępem monitorowania konfiguracji w portalu][1]

1. Wybierz kategorie monitorowania, chcesz monitorować, a następnie kliknij przycisk **zapisać**. Zdarzenia są dostępne do odczytu z punktu końcowego Centrum zdarzeń zgodnych na liście **ustawienia monitorowania**. Nosi nazwę punktu końcowego Centrum IoT `messages/operationsmonitoringevents`.

    ![Skonfiguruj operacje monitorowania na Centrum IoT][2]

> [!NOTE]
> Wybieranie **pełne** monitorowania **połączeń** category powoduje, że Centrum IoT można wygenerować komunikaty dodatkowych diagnostyczne. Dla wszystkich innych kategoriach **pełne** zawiera zmiany ustawień ilość informacji Centrum IoT w każdy komunikat o błędzie.

## <a name="event-categories-and-how-to-use-them"></a>Kategorie zdarzeń i sposobu ich użycia

Każdy operacje monitorowania śledzi kategorii ma inny typ interakcji z Centrum IoT i każda kategoria monitorowania schematu definiującego struktury zdarzenia w tej kategorii.

### <a name="device-identity-operations"></a>Operacje tożsamości urządzenia

Kategoria operacje tożsamości urządzenia śledzi błędów występujących podczas próby utworzenia, zaktualizować lub usunąć wpis w rejestrze tożsamości Centrum IoT. Ta kategoria śledzenia jest przydatne w przypadku inicjowania obsługi scenariuszy.

```json
{
    "time": "UTC timestamp",
    "operationName": "create",
    "category": "DeviceIdentityOperations",
    "level": "Error",
    "statusCode": 4XX,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "durationMs": 1234,
    "userAgent": "userAgent",
    "sharedAccessPolicy": "accessPolicy"
}
```

### <a name="device-telemetry"></a>Telemetrii urządzenia

Kategoria telemetrii urządzenia śledzi błędy występują w Centrum IoT, które są powiązane z potokiem telemetrii. Ta kategoria zawiera błędy występujące podczas wysyłania zdarzenia telemetrii (takie jak dławienie) i odbieranie zdarzeń telemetrii (np. czytnik nieautoryzowanych). Ta kategoria nie może przechwycić błędów spowodowanych przez kod działający na urządzeniu.

```json
{
    "messageSizeInBytes": 1234,
    "batching": 0,
    "protocol": "Amqp",
    "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="cloud-to-device-commands"></a>Polecenia chmury do urządzenia

Kategoria polecenia chmury do urządzenia śledzi błędy występują w Centrum IoT, które są powiązane z potokiem wiadomości chmury do urządzenia. Ta kategoria zawiera błędy występujące podczas wysyłania wiadomości chmury do urządzenia (takich jak nieautoryzowanego nadawcę), odbierania wiadomości chmury do urządzenia (takich jak przekroczona liczba dostarczania) i odbieranie komunikatu chmura urządzenie opinii (takie jak opinii wygasł). Ta kategoria nie przechwytuje błędy z urządzenia, które nieprawidłowo obsługuje komunikat chmury do urządzenia, jeśli komunikatu chmura urządzenie zostało pomyślnie dostarczone.

```json
{
    "messageSizeInBytes": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "deliveryAcknowledgement": 0,
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "C2DCommands",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="connections"></a>Połączenia

Kategoria połączenia śledzi błędów występujących podczas urządzeń Połącz lub Rozłącz z Centrum IoT. Śledzenie tej kategorii jest przydatne do identyfikowania próby nieautoryzowanego połączenia i śledzenia, gdy połączenie zostanie przerwane dla urządzeń w zakresie łączności niska.

```json
{
    "durationMs": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID"
}
```

### <a name="file-uploads"></a>Przekazywania plików

Kategoria przekazywania pliku śledzenia błędów, które występują w Centrum IoT i są związane z funkcji przekazywania plików. Ta kategoria zawiera:

* Błędy, które występują w przypadku identyfikatora URI połączenia SAS, takie jak kiedy wygasa przed urządzenia powiadamia koncentratora przekazywanie zostało ukończone.
* Przekazywanie zgłoszonych przez urządzenia nie powiodła się.
* Błędy występujące po plik nie zostanie znaleziony w magazynie podczas tworzenia komunikatu powiadomienia Centrum IoT.

Ta kategoria nie może przechwycić błędów, które są wykonywane bezpośrednio, gdy urządzenie jest przekazywany do magazynu.

```json
{
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "HTTP",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "fileUpload",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "blobUri": "http//bloburi.com",
    "durationMs": 1234
}
```

### <a name="message-routing"></a>Kierowanie komunikatów

Kategoria routingu wiadomości śledzi błędów występujących podczas oceny trasy wiadomości i punktu końcowego kondycję postrzegane przez Centrum IoT. Ta kategoria zawiera zdarzenia, np. gdy reguła zwraca "undefined", gdy Centrum IoT oznacza punkt końcowy jako wiadomości i innych błędów, odbierane z punktu końcowego. Ta kategoria nie ma określonych błędów o komunikatach się (na przykład urządzenie ograniczania błędy), które zostały zgłoszone w kategorii "telemetrii urządzenia".

```json
{
    "messageSizeInBytes": 1234,
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "routes",
    "level": "Error",
    "deviceId": "device-ID",
    "messageId": "ID of message",
    "routeName": "myroute",
    "endpointName": "myendpoint",
    "details": "ExternalEndpointDisabled"
}
```

## <a name="view-events"></a>Wyświetlanie zdarzeń

Można użyć *explorer Centrum iothub* narzędzia do testowania szybkie, że Centrum IoT generuje monitorowania zdarzeń. Aby zainstalować narzędzie, zobacz instrukcje w [explorer Centrum iothub] [ lnk-iothub-explorer] repozytorium GitHub.

1. Upewnij się, że **połączeń** monitorowania kategorii ustawiono **pełne** w portalu.

1. W wierszu polecenia Uruchom następujące polecenie, aby odczytać z punktu końcowego monitorowania:

    ```
    iothub-explorer monitor-ops --login {your iothubowner connection string}
    ```

1. W innego wiersza polecenia Uruchom następujące polecenie, aby symulować urządzenia wysyłania wiadomości urządzenia do chmury:

    ```
    iothub-explorer simulate-device {your device name} --send "My test message" --login {your iothubowner connection string}
    ```

1. Pierwszy wiersz polecenia zawiera zdarzenia monitorowania, jak symulowane urządzenie łączy się z Centrum IoT.

## <a name="connect-to-the-monitoring-endpoint"></a>Połącz z punktem końcowym monitorowania

Punkt końcowy monitorowania w Centrum IoT jest punktem końcowym zgodnego Centrum zdarzeń. Można użyć dowolnego mechanizm, który współpracuje z usługą Event Hubs można odczytać monitorowania wiadomości z tego punktu końcowego. W poniższym przykładzie tworzone podstawowe reader, który nie jest odpowiedni dla wdrożenia wysokiej przepływności. Więcej informacji na temat przetwarzania komunikatów z usługi Event Hubs znajduje się w samouczku [Rozpoczynanie pracy z usługą Event Hubs][lnk-eventhubs-tutorial].

Aby połączyć się punkt końcowy monitorowania, należy ciąg połączenia i nazwę punktu końcowego. Poniższe kroki pokazują, jak znaleźć niezbędnych wartości w portalu:

1. W portalu przejdź do bloku zasobów z Centrum IoT.

1. Wybierz **operacje monitorowania**i zanotuj **nazwę Centrum zdarzeń zgodnych** i **punktu końcowego Centrum zdarzeń zgodnych** wartości:

    ![Wartości punktu końcowego zgodnych z Centrum zdarzeń][img-endpoints]

1. Wybierz **zasady dostępu współużytkowanego**, a następnie wybierz **usługi**. Zanotuj **klucz podstawowy** wartość:

    ![Klucz podstawowy zasady dostępu współdzielonego usługi][img-service-key]

Poniższy przykład kodu C# jest pobierana z programu Visual Studio **Windows Desktop klasycznego** aplikacji konsolowej C#. Projekt posiada **WindowsAzure.ServiceBus** zainstalowany pakiet NuGet.

* Zastąp symbol zastępczy parametrów połączenia przy użyciu parametrów połączenia, który używa **punktu końcowego Centrum zdarzeń zgodnych** i usługa **klucz podstawowy** wartości zanotowany wcześniej, jak pokazano w poniższym przykładzie:

    ```cs
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Zastąp monitorowania symbol zastępczy Nazwa punktu końcowego z **nazwę Centrum zdarzeń zgodnych** wartość zanotowany wcześniej.

```cs
class Program
{
    static string connectionString = "{your monitoring endpoint connection string}";
    static string monitoringEndpointName = "{your monitoring endpoint name}";
    static EventHubClient eventHubClient;

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();

        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }

        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }

            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));

            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }
    }
}
```

## <a name="next-steps"></a>Kolejne kroki
Aby dokładniej analizować możliwości Centrum IoT, zobacz:

* [Przewodnik dewelopera Centrum IoT][lnk-devguide]
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png
[img-endpoints]: media/iot-hub-operations-monitoring/monitoring-endpoint.png
[img-service-key]: media/iot-hub-operations-monitoring/service-key.png

[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-accelerators-with-azure-monitor-and-azure-resource-health
[lnk-monitor]: iot-hub-monitor-resource-health.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
