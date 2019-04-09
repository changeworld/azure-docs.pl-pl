---
title: Operacji na platformie Azure IoT Hub monitorowania (przestarzałe) | Dokumentacja firmy Microsoft
description: Jak używać usługi Azure IoT Hub operacji monitoring do monitorowania stanu operacji w Centrum IoT w czasie rzeczywistym.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: nberdy
ms.openlocfilehash: 84f28a1cb411e7df156fc08fa683efe7f83eda64
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59258117"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>Operacje usługi IoT Hub monitorowania (przestarzałe)

Monitorowanie operacji usługi IoT Hub umożliwia monitorowanie stanu operacji w Centrum IoT w czasie rzeczywistym. Usługa IoT Hub śledzi zdarzenia w ramach kilku kategorii operacji. Możesz zdecydować się na wysyłanie zdarzeń z jedną lub więcej kategorii do punktu końcowego Centrum IoT Hub do przetworzenia. Można monitorować dane dotyczące błędów lub skonfigurować bardziej złożone przetwarzanie na podstawie wzorców danych.

>[!NOTE]
>Usługa IoT Hub **monitorowanie operacji jest przestarzały i został usunięty z usługi IoT Hub na 10 marca 2019**. Do monitorowania operacji i kondycji Centrum IoT Hub, zobacz [monitorowania kondycji usługi Azure IoT Hub i szybkie diagnozowanie problemów](iot-hub-monitor-resource-health.md). Aby uzyskać więcej informacji na temat osi czasu wycofywania, zobacz [Monitoruj rozwiązania Azure IoT za pomocą usługi Azure Monitor i Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health).

Usługa IoT Hub monitoruje sześć kategorie zdarzeń:

* Operacje dotyczące tożsamości urządzenia
* Danych telemetrycznych z urządzenia
* Komunikaty z chmury do urządzenia
* Połączenia
* Operacje przekazywania plików
* Kierowanie komunikatów

> [!IMPORTANT]
> Monitorowanie operacji usługi IoT Hub nie gwarantuje niezawodne lub są uporządkowane dostarczenia zdarzeń. W zależności od podstawowej infrastruktury usługi IoT Hub niektóre zdarzenia mogą utracone lub dostarczony poza kolejnością. Użyj operacji na monitorowanie w celu generowania alertów w oparciu o sygnały błędu, takie jak próby nawiązania połączenia nie powiodło się lub odłączenia o wysokiej częstotliwości dla określonych urządzeń. Nie należy polegać na operacje monitorowania zdarzeń, aby utworzyć magazyn spójnego stanu urządzenia, np. store, śledzenie połączone lub odłączona stan urządzenia. 

## <a name="how-to-enable-operations-monitoring"></a>Jak włączyć monitorowanie operacji

1. Utwórz centrum IoT. Instrukcje można znaleźć na temat tworzenia Centrum IoT hub w [wprowadzenie](quickstart-send-telemetry-dotnet.md) przewodnik.

2. Otwórz blok Centrum IoT hub. W tym miejscu, kliknij przycisk **monitorowanie operacji**.

    ![Operacje dostępu do monitorowania konfiguracji w portalu](./media/iot-hub-operations-monitoring/enable-OM-1.png)

3. Wybierz kategorie monitorowania, chcesz monitorować, a następnie kliknij przycisk **Zapisz**. Zdarzenia są dostępne do odczytywania z punktu końcowego zgodnego z Centrum zdarzeń, na liście **ustawienia monitorowania**. Następuje wywołanie punktu końcowego usługi IoT Hub `messages/operationsmonitoringevents`.

    ![Konfiguruj monitorowanie Centrum IoT operacji](./media/iot-hub-operations-monitoring/enable-OM-2.png)

> [!NOTE]
> Wybieranie **pełne** monitorowania **połączeń** category powoduje, że usługi IoT Hub można wygenerować komunikaty diagnostyczne dodatkowe. Dla wszystkich innych kategoriach **pełne** zmiany ustawień ilość informacji o usłudze IoT Hub zawiera każdy komunikat o błędzie.

## <a name="event-categories-and-how-to-use-them"></a>Kategorie zdarzeń i sposobu ich używania

Każdy śledzi kategorii monitorowanie operacji innego typu interakcji z IoT Hub, a każda kategoria monitorowania zawiera schemat definiujący strukturze zdarzeń w danej kategorii.

### <a name="device-identity-operations"></a>Operacje dotyczące tożsamości urządzenia

Kategoria operacje tożsamości urządzenia do śledzenia błędów występujących podczas próby utworzenia, aktualizacji lub usuń wpis w rejestrze tożsamości usługi IoT hub. Śledzenie tej kategorii jest przydatne w przypadku inicjowania obsługi scenariuszy.

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

### <a name="device-telemetry"></a>Danych telemetrycznych z urządzenia

Kategorię danych telemetrycznych urządzenia do śledzenia błędów, które występują w Centrum IoT hub i są powiązane z potoku danych telemetrycznych. Ta kategoria zawiera błędy, które występują podczas wysyłania danych telemetrycznych zdarzeń (takie jak ograniczanie przepustowości) i odbieranie danych telemetrycznych zdarzeń (np. czytnik nieautoryzowany). Ta kategoria nie umożliwia przechwytywania błędów spowodowanych przez kod działający na samym urządzeniu.

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

### <a name="cloud-to-device-commands"></a>Polecenia chmura-urządzenie

Kategorii poleceń chmura urządzenie do śledzenia błędów, które występują w Centrum IoT hub i są powiązane z potok komunikatów z chmury do urządzenia. Ta kategoria obejmuje błędów występujących podczas wysyłania komunikatów z chmury do urządzeń (na przykład nieautoryzowanego nadawcę), odbieranie komunikatów z chmury do urządzeń (np. Przekroczono licznik dostaw) i odbieranie opinii komunikatów z chmury do urządzenia (takie jak opinii wygasła). Ta kategoria nie może przechwytywać błędy z urządzenia, obsługujący nieprawidłowo komunikatu chmura urządzenie, jeśli komunikatu chmura urządzenie zostało pomyślnie dostarczone.

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

Kategoria połączenia służy do śledzenia błędów, które występują, gdy urządzenia łączyć i rozłączać z Centrum IoT hub. Śledzenie tej kategorii jest przydatne do identyfikowania próby nawiązania połączenia nieautoryzowanych i śledzenia, gdy połączenie zostanie przerwane dla urządzeń w obszarach niską łączności.

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

### <a name="file-uploads"></a>Operacje przekazywania plików

Kategoria przekazywania pliku do śledzenia błędów, które występują w Centrum IoT hub i są związane z funkcjonalnością przekazywania plików. Ta kategoria obejmuje:

* Błędy występujące z identyfikatora URI połączenia SAS, takie jak kiedy wygasa przed urządzenia powiadamia Centrum przekazywanie zostało ukończone.

* Nie powiodło się przekazywania zgłoszona przez urządzenie.

* Błędy, które występują, gdy nie można odnaleźć pliku w magazynie podczas tworzenia komunikatu powiadomienia usługi IoT Hub.

Ta kategoria nie umożliwia przechwytywania błędów występujących bezpośrednio podczas przekazywania pliku z urządzenia do usługi storage.

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

Kategoria routingu wiadomości do śledzenia błędów występujących podczas oceny trasy wiadomości i punktu końcowego kondycji postrzeganiu przez usługę IoT Hub. Ta kategoria zawiera zdarzenia, np. gdy reguła zwraca "undefined", gdy usługi IoT Hub oznacza punktu końcowego jako martwe i inne błędy otrzymane od punktu końcowego. Ta kategoria nie obejmuje określone błędy dotyczące komunikatów samodzielnie (na przykład urządzenie błędy ograniczania przepływności), które zostały zgłoszone w kategorii "danych telemetrycznych z urządzenia".

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

## <a name="connect-to-the-monitoring-endpoint"></a>Nawiązać połączenie z monitorowania punktu końcowego

Punkt końcowy monitorowania w Centrum IoT to punkt końcowy zgodny z Centrum zdarzeń. Możesz użyć dowolnego mechanizmu, który współpracuje z usługą Event Hubs do odczytywania komunikatów monitorowania z tego punktu końcowego. Poniższy przykład tworzy czytnika podstawowego, który nie jest odpowiedni dla wdrożenia o wysokiej przepływności. Aby uzyskać więcej informacji na temat sposobu przetwarzania komunikatów z usługi Event Hubs, zobacz [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md) samouczka.

Aby połączyć z monitorowania punktu końcowego, należy parametry połączenia i nazwę punktu końcowego. Poniższe kroki pokazują, jak znaleźć potrzebne wartości w portalu:

1. W portalu przejdź do bloku zasobów usługi IoT Hub.

2. Wybierz **monitorowanie operacji**i zanotuj **nazwę zgodną z Centrum zdarzeń** i **punktu końcowego zgodnego z Centrum zdarzeń** wartości:

    ![Wartości punktu końcowego zgodnego z Centrum zdarzeń](./media/iot-hub-operations-monitoring/monitoring-endpoint.png)

3. Wybierz **zasady dostępu współdzielonego**, następnie wybierz **usługi**. Zwróć uwagę na **klucz podstawowy** wartość:

    ![Klucz podstawowy zasady dostępu współdzielonego usługi](./media/iot-hub-operations-monitoring/service-key.png)

Poniższy przykładowy kod języka C# jest pobierana z programu Visual Studio **Windows Classic Desktop** aplikację konsoli C#. Projekt ma **WindowsAzure.ServiceBus** zainstalowany pakiet NuGet.

* Zastąp symbol zastępczy parametrów połączenia parametrami połączenia, który używa **punktu końcowego zgodnego z Centrum zdarzeń** i usługa **klucz podstawowy** wartościami zanotowanymi wcześniej, jak pokazano w poniższym przykładzie:

    ```csharp
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Zastąp monitorowania symbol zastępczy nazwy punktu końcowego przy użyciu **nazwę zgodną z Centrum zdarzeń** wartość zanotowanymi wcześniej.

```csharp
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

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych za pomocą usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)