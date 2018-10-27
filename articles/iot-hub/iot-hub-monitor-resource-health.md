---
title: Monitorowanie kondycji usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Monitor i Azure Resource Health monitorowanie Centrum IoT Hub i szybkie diagnozowanie problemów
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/09/2018
ms.author: kgremban
ms.openlocfilehash: c400a084a78af6313e355d65bcbc07a520f55514
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50156055"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Monitorowanie kondycji usługi Azure IoT Hub i szybkie diagnozowanie problemów

Firmy, które implementują usługi Azure IoT Hub oczekiwane niezawodność, wydajność z zasobami. Aby ułatwić utrzymania Zamknij wyrażenia kontrolnego na operacje usługi IoT Hub to w pełni zintegrowana z [usługi Azure Monitor] [ lnk-AM] i [usługi Azure Resource Health] [ lnk-ARH]. Te dwie usługi działają w połączeniu sekcją, aby udostępniać dane, należy zachować rozwiązania IoT w górę i uruchamiania w dobrej kondycji. 

Usługa Azure Monitor to pojedyncze źródło monitorowania i rejestrowania dla wszystkich usług platformy Azure. Możesz wysłać dzienniki diagnostyczne, które generuje usługi Azure Monitor do usługi Log Analytics, usługi Event Hubs lub usługi Azure Storage do niestandardowego przetwarzania. Usługa Azure Monitor ustawienia metryki i Diagnostyka zapewniają wgląd w wydajność zasobów. Przeczytaj ten artykuł, aby dowiedzieć się, jak [użycia usługi Azure Monitor](#use-azure-monitor) za pomocą usługi IoT hub. 

> [!IMPORTANT]
> Zdarzenia wyemitowane przez usługę IoT Hub przy użyciu dzienników diagnostycznych usługi Azure Monitor nie musi być niezawodne lub są uporządkowane. Niektóre zdarzenia mogły zostać utracone albo dostarczony poza kolejnością. Dzienniki diagnostyczne również nie należy traktować jako w czasie rzeczywistym i może potrwać kilka minut, zanim zdarzenia do zarejestrowania się w wybranej lokalizacji docelowej.

Usługa Azure Resource Health pomaga diagnozować i uzyskać pomoc techniczną, gdy problemy z platformy Azure wpłynie na Twoje zasoby. Spersonalizowany pulpit nawigacyjny zawiera stan bieżącej i przeszłej kondycji dla usługi IoT Hub. Przeczytaj ten artykuł, aby dowiedzieć się, jak [użycia usługi Azure Resource Health](#use-azure-resource-health) za pomocą usługi IoT hub. 

Oprócz integracji z tych dwóch usług, IoT Hub udostępnia swoje własne metryki, które można użyć, aby poznać stan zasobów IoT. Aby dowiedzieć się więcej, zobacz [metryki zrozumieć usługi IoT Hub][lnk-metrics].

## <a name="use-azure-monitor"></a>Korzystanie z usługi Azure Monitor

Usługa Azure Monitor udostępnia informacje diagnostyczne poziomie zasobów, co oznacza, monitorować operacje, które odbywają się w Centrum IoT hub. 

Zastępuje ustawienia diagnostyki usługi Azure Monitor. monitorowanie operacji usługi IoT Hub. Jeśli obecnie używasz monitorowanie operacji, można dokonać migracji przepływów pracy. Aby uzyskać więcej informacji, zobacz [migracja z usługi operations ustawienia monitorowania do diagnostyki][lnk-migrate].

Aby dowiedzieć się więcej na temat określonych metryk i zdarzeń usługi Azure Monitor obserwuje, zobacz [metryki obsługiwane z usługą Azure Monitor] [ lnk-AM-metrics] i [obsługiwane usługi, schematów i kategorie dla platformy Azure Dzienniki diagnostyczne][lnk-AM-schemas].

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Objaśnienie dzienników

Usługa Azure Monitor śledzi różnych operacji występujących w usłudze IoT Hub. Każda kategoria ma schemat, który definiuje sposób zgłaszania zdarzeń w danej kategorii. 



#### <a name="connections"></a>Połączenia

Urządzenia śledzi kategorii połączenia Połącz i odłączanie zdarzeń usługi IoT hub, jak również błędy. Śledzenie tej kategorii jest przydatne do identyfikowania próby nawiązania połączenia nieautoryzowanych i śledzenia, gdy połączenie zostanie przerwane dla urządzeń w obszarach niską łączności.

> [!NOTE]
> Stan niezawodnego połączenia urządzeń można znaleźć [pulsu urządzenia][lnk-devguide-heartbeat].

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Information",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}", 
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-commands"></a>Polecenia chmura-urządzenie

Kategorii poleceń chmura urządzenie do śledzenia błędów, które występują w Centrum IoT hub i są powiązane z potok komunikatów z chmury do urządzenia. Ta kategoria obejmuje błędów występujących podczas wysyłania komunikatów z chmury do urządzeń (na przykład nieautoryzowanego nadawcę), odbieranie komunikatów z chmury do urządzeń (np. Przekroczono licznik dostaw) i odbieranie opinii komunikatów z chmury do urządzenia (takie jak opinii wygasła). Ta kategoria nie może przechwytywać błędy z urządzenia, obsługujący nieprawidłowo komunikatu chmura urządzenie, jeśli komunikatu chmura urządzenie zostało pomyślnie dostarczone.

```json
{
    "records": 
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddresss\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-identity-operations"></a>Operacje dotyczące tożsamości urządzenia

Kategoria operacje tożsamości urządzenia do śledzenia błędów występujących podczas próby utworzenia, aktualizacji lub usuń wpis w rejestrze tożsamości usługi IoT hub. Śledzenie tej kategorii jest przydatne w przypadku inicjowania obsługi scenariuszy.

```json
{
    "records": 
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",    
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="routes"></a>Trasy

Kategoria routingu wiadomości do śledzenia błędów występujących podczas oceny trasy wiadomości i punktu końcowego kondycji postrzeganiu przez usługę IoT Hub. Ta kategoria zawiera zdarzenia, np. gdy reguła zwraca "undefined", gdy usługi IoT Hub oznacza punktu końcowego jako martwe i inne błędy otrzymane od punktu końcowego. Ta kategoria nie obejmuje określone błędy dotyczące komunikatów samodzielnie (na przykład urządzenie błędy ograniczania przepływności), które zostały zgłoszone w kategorii "danych telemetrycznych z urządzenia".

```json
{
    "records": 
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "endpointUnhealthy",
            "category": "Routes",
            "level": "Error",
            "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-telemetry"></a>Danych telemetrycznych z urządzenia

Kategorię danych telemetrycznych urządzenia do śledzenia błędów, które występują w Centrum IoT hub i są powiązane z potoku danych telemetrycznych. Ta kategoria zawiera błędy, które występują podczas wysyłania danych telemetrycznych zdarzeń (takie jak ograniczanie przepustowości) i odbieranie danych telemetrycznych zdarzeń (np. czytnik nieautoryzowany). Ta kategoria nie umożliwia przechwytywania błędów spowodowanych przez kod działający na samym urządzeniu.

```json
{
    "records": 
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

#### <a name="file-upload-operations"></a>Operacje przekazywania plików

Kategoria przekazywania pliku do śledzenia błędów, które występują w Centrum IoT hub i są związane z funkcjonalnością przekazywania plików. Ta kategoria obejmuje:

* Błędy występujące z identyfikatora URI połączenia SAS, takie jak kiedy wygasa przed urządzenia powiadamia Centrum przekazywanie zostało ukończone.
* Nie powiodło się przekazywania zgłoszona przez urządzenie.
* Błędy, które występują, gdy nie można odnaleźć pliku w magazynie podczas tworzenia komunikatu powiadomienia usługi IoT Hub.

Ta kategoria nie umożliwia przechwytywania błędów występujących bezpośrednio podczas przekazywania pliku z urządzenia do usługi storage.

```json
{
    "records": 
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-twin-operations"></a>Operacje bliźniaczej reprezentacji chmury do urządzenia

Kategoria operacje chmury do urządzeń, bliźniacza reprezentacja śledzi zdarzenia zainicjowanych przez usługę na bliźniaczych reprezentacji urządzeń. Te operacje można obejmują bliźniaczej reprezentacji get, zaktualizować lub tagów, Zastąp i aktualizacji lub zastąpić żądane właściwości. 

```json
{
    "records": 
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}", 
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-to-cloud-twin-operations"></a>Operacje bliźniaczej reprezentacji urządzenia do chmury

Kategoria operacje bliźniaczej reprezentacji urządzenia do chmury śledzi zdarzenia zainicjowane przez urządzenie na bliźniaczych reprezentacji urządzeń. Te operacje można obejmują bliźniaczej reprezentacji get, aktualizowania zgłoszonych właściwości i subskrybować żądane właściwości.

```json
{
    "records": 
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}", 
            "location": "Resource location"
        }
    ]
}
```

#### <a name="twin-queries"></a>Zapytania dotyczące bliźniaczych reprezentacji

Kategoria zapytań bliźniaczych reprezentacji raporty dotyczące żądań zapytań dla bliźniaczych reprezentacji urządzeń, które są inicjowane w chmurze. 

```json
{
    "records": 
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}", 
            "location": "Resource location"
        }
    ]
}
```

#### <a name="jobs-operations"></a>Operacje zadań

Kategoria operacje zadań zgłasza żądania dotyczące zadań zaktualizować bliźniaczych reprezentacji urządzeń lub wywoływanie metod bezpośrednich na wielu urządzeniach. Te żądania są inicjowane w chmurze. 

```json
{
    "records": 
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}", 
            "location": "Resource location"
        }
    ]
}
```

#### <a name="direct-methods"></a>Metody bezpośrednie

Kategoria metod bezpośrednich śledzi interakcje odpowiedź na żądanie wysłane do poszczególnych urządzeń. Te żądania są inicjowane w chmurze. 

```json
{
    "records": 
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\", \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}", 
            "location": "Resource location"
        }
    ]
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Czytelne dzienniki z usługi Azure Event Hubs

Po skonfigurowaniu rejestrowania za pomocą ustawień diagnostycznych zdarzeń możesz tworzyć aplikacje, które odczytywane dzienniki, aby można podjąć odpowiednie działania, w oparciu o informacje zawarte w nich. Ten przykładowy kod pobiera dzienniki z Centrum zdarzeń:

```csharp
class Program 
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}"; 
    static string monitoringEndpointName = "{your AMS event hub endpoint name}"; 
    static EventHubClient eventHubClient; 
//This is the Diagnostic Settings schema 
    class AzureMonitorDiagnosticLog 
    { 
        string time { get; set; } 
        string resourceId { get; set; } 
        string operationName { get; set; } 
        string category { get; set; } 
        string level { get; set; } 
        string resultType { get; set; } 
        string resultDescription { get; set; } 
        string durationMs { get; set; } 
        string callerIpAddress { get; set; } 
        string correlationId { get; set; } 
        string identity { get; set; } 
        string location { get; set; } 
        Dictionary<string, string> properties { get; set; } 
    }; 
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
                var deserializer = new JavaScriptSerializer(); 
                //deserialize json data to azure monitor object 
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result); 
 
            } 
        } 
    } 
} 
```

## <a name="use-azure-resource-health"></a>Korzystać z usługi Azure Resource Health

Użyj usługi Azure Resource Health, aby monitorować, czy Centrum IoT hub jest uruchomiona. Możesz także dowiedzieć się czy awaria regionalna ma wpływ na kondycję usługi IoT hub. Aby poznać szczegółowe informacje o stanie kondycji usługi Azure IoT Hub, zaleca się, że możesz [użycia usługi Azure Monitor](#use-azure-monitor). 

Usługa Azure IoT Hub wskazuje kondycji na poziomie regionalnym. Jeśli istnieje awaria regionalna wywierania wpływu na usługi IoT hub, stan kondycji jest wyświetlany jako **nieznany**. Aby dowiedzieć się więcej na temat kontroli kondycji konkretnego, wykonywanych przez usługi Azure Resource Health, zobacz [typów zasobów i kontrole kondycji w usłudze Azure resource health][lnk-ARH-checks].

Aby sprawdzić kondycję usługi IoT Hub, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **Service Health** > **Resource health**.
1. Z pola listy rozwijanej wybierz swoją subskrypcję i **usługi IoT Hub**.

Aby dowiedzieć się więcej o tym, jak interpretować dane dotyczące kondycji, zobacz [Przegląd kondycji zasobów platformy Azure][lnk-ARH]

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie usługi IoT Hub metryki][lnk-metrics]
- [Zdalne monitorowanie IoT i powiadomień za pomocą usługi Azure Logic Apps, łącząc usługę IoT hub i skrzynki pocztowej][lnk-monitoring-notifications]


[lnk-AM]: ../azure-monitor/index.yml
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-migrate]: iot-hub-migrate-to-diagnostics-settings.md
[lnk-AM-metrics]: ../monitoring-and-diagnostics/monitoring-supported-metrics.md
[lnk-AM-schemas]: ../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md
[lnk-ARH-checks]: ../service-health/resource-health-checks-resource-types.md
[lnk-monitoring-notifications]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
[lnk-devguide-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
