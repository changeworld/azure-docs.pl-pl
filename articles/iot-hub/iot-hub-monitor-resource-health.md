---
title: Monitorowanie kondycji Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Użyj monitora Azure i kondycji zasobów platformy Azure do monitorowania Centrum IoT i szybkie diagnozowanie problemów
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: kgremban
ms.openlocfilehash: ed0bde0945a36c4fa194059f3b17557d566047a2
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Monitorowanie kondycji Azure IoT Hub i szybkie diagnozowanie problemów

Firm, które implementują Centrum IoT Azure oczekiwać niezawodnej wydajności z zasobami. Aby ułatwić Obsługa Zamknij czujki na operacje, Centrum IoT jest w pełni zintegrowana z [Azure Monitor] [ lnk-AM] i [kondycja zasobów Azure] [ lnk-ARH]. Te dwie usługi działa w połączeniu ze dane potrzebne do przechowywania Twojego rozwiązania IoT w górę i uruchomiony w dobrej kondycji. 

Azure Monitor jest jednego źródła, monitorowania i rejestrowania dla wszystkich usług platformy Azure. Możesz wysłać dzienniki, generowane przez Azure Monitor Log Analytics, centra zdarzeń lub usługi Azure Storage do przetwarzania niestandardowego. Ustawienia metryki i diagnostyki Azure Monitor zapewniają wgląd w czasie rzeczywistym w wydajności zasobów. Kontynuuj czytanie tego artykułu, aby dowiedzieć się, jak [Użyj monitora Azure](#use-azure-monitor) z Centrum IoT. 

Kondycja zasobów Azure ułatwia diagnozowanie i uzyskać pomoc techniczną, gdy problemy Azure ma wpływ na zasoby. Spersonalizowane pulpit nawigacyjny zawiera stan kondycji bieżących i starszych dla Twojej centra IoT. Kontynuuj czytanie tego artykułu, aby dowiedzieć się, jak [kondycja zasobów Azure użyj](#use-azure-resource-health) z Centrum IoT. 

Oprócz integracji z tych dwóch usług, Centrum IoT zapewnia własną metryki, które umożliwiają poznanie stanu zasobów IoT. Aby dowiedzieć się więcej, zobacz [metryki zrozumieć Centrum IoT][lnk-metrics].

## <a name="use-azure-monitor"></a>Korzystanie z usługi Azure Monitor

Azure Monitor udostępnia informacje diagnostyczne poziom zasobów, co oznacza monitorować działania, które odbywają się w Centrum IoT. 

Zastępuje ustawienia diagnostyki Azure Monitor monitorować operacji centrum IoT. Jeśli użytkownik aktualnie operacji użytkownika monitorowanie, należy zmigrować przepływów pracy. Aby uzyskać więcej informacji, zobacz [migracji z operacji ustawienia monitorowania do diagnostyki][lnk-migrate].

Aby dowiedzieć się więcej na temat określonych metryk i zdarzeń, które Azure Monitor Obserwujący, zobacz [obsługiwane metryki z monitorem Azure] [ lnk-AM-metrics] i [obsługiwane usługi, schematów i kategorie dla platformy Azure Dzienniki diagnostyczne][lnk-AM-schemas].

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Zrozumienie dzienniki

Azure Monitor śledzi różne operacje, które występują w Centrum IoT. Każda kategoria ma schematu, który definiuje sposób zgłaszania zdarzeń w tej kategorii. 

#### <a name="connections"></a>Połączenia

Kategoria połączenia śledzi błędów występujących podczas urządzeń Połącz lub Rozłącz z Centrum IoT. Śledzenie tej kategorii jest przydatne do identyfikowania próby nieautoryzowanego połączenia i śledzenia, gdy połączenie zostanie przerwane dla urządzeń w zakresie łączności niska.

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

#### <a name="cloud-to-device-commands"></a>Polecenia chmury do urządzenia

Kategoria polecenia chmury do urządzenia śledzi błędy występują w Centrum IoT, które są powiązane z potokiem wiadomości chmury do urządzenia. Ta kategoria zawiera błędy występujące podczas wysyłania wiadomości chmury do urządzenia (takich jak nieautoryzowanego nadawcę), odbierania wiadomości chmury do urządzenia (takich jak przekroczona liczba dostarczania) i odbieranie komunikatu chmura urządzenie opinii (takie jak opinii wygasł). Ta kategoria nie przechwytuje błędy z urządzenia, które nieprawidłowo obsługuje komunikat chmury do urządzenia, jeśli komunikatu chmura urządzenie zostało pomyślnie dostarczone.

```json
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
```

#### <a name="device-identity-operations"></a>Operacje tożsamości urządzenia

Kategoria operacje tożsamości urządzenia śledzi błędów występujących podczas próby utworzenia, zaktualizować lub usunąć wpis w rejestrze tożsamości Centrum IoT. Ta kategoria śledzenia jest przydatne w przypadku inicjowania obsługi scenariuszy.

```json
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
```

#### <a name="routes"></a>Trasy

Kategoria routingu wiadomości śledzi błędów występujących podczas oceny trasy wiadomości i punktu końcowego kondycję postrzegane przez Centrum IoT. Ta kategoria zawiera zdarzenia, np. gdy reguła zwraca "undefined", gdy Centrum IoT oznacza punkt końcowy jako wiadomości i innych błędów, odbierane z punktu końcowego. Ta kategoria nie ma określonych błędów o komunikatach się (na przykład urządzenie ograniczania błędy), które zostały zgłoszone w kategorii "telemetrii urządzenia".

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "endpointUnhealthy",
    "category": "Routes",
    "level": "Error",
    "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
    "location": "Resource location"
}
```

#### <a name="device-telemetry"></a>Telemetrii urządzenia

Kategoria telemetrii urządzenia śledzi błędy występują w Centrum IoT, które są powiązane z potokiem telemetrii. Ta kategoria zawiera błędy występujące podczas wysyłania zdarzenia telemetrii (takie jak dławienie) i odbieranie zdarzeń telemetrii (np. czytnik nieautoryzowanych). Ta kategoria nie może przechwycić błędów spowodowanych przez kod działający na urządzeniu.

```json
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
```

#### <a name="file-upload-operations"></a>Operacje przekazywania plików

Kategoria przekazywania pliku śledzenia błędów, które występują w Centrum IoT i są związane z funkcji przekazywania plików. Ta kategoria zawiera:

* Błędy, które występują w przypadku identyfikatora URI połączenia SAS, takie jak kiedy wygasa przed urządzenia powiadamia koncentratora przekazywanie zostało ukończone.
* Przekazywanie zgłoszonych przez urządzenia nie powiodła się.
* Błędy występujące po plik nie zostanie znaleziony w magazynie podczas tworzenia komunikatu powiadomienia Centrum IoT.

Ta kategoria nie może przechwycić błędów, które są wykonywane bezpośrednio, gdy urządzenie jest przekazywany do magazynu.

```json
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
```

#### <a name="cloud-to-device-twin-operations"></a>Operacje dwie chmury do urządzenia

Kategoria operacje dwie chmury do urządzenia śledzi zdarzenia inicjowanych przez usługi na twins urządzenia. Te operacje można obejmują dwie get, zaktualizuj właściwości zgłoszone i subskrybowanie odpowiednie właściwości

```json
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
```

#### <a name="device-to-cloud-twin-operations"></a>Operacje dwie urządzenia do chmury

Kategoria operacje dwie urządzenia do chmury śledzi zdarzenia inicjowanych przez urządzenia na urządzeniu twins. Te operacje można obejmują dwie get, zaktualizować lub Zastąp znaczników i zaktualizować lub zastąpić odpowiednie właściwości. 

```json
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
```

#### <a name="twin-queries"></a>Dwie zapytań

Kategoria zapytania dwie raporty dotyczące żądań zapytań dla twins urządzenia, które są inicjowane w chmurze. 

```json
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
```

#### <a name="jobs-operations"></a>Operacje zadań

Kategoria operacje zadania raporty dotyczące żądań zadania aktualizacji twins urządzenia lub wywołanie metody bezpośrednio na wielu urządzeniach. Te żądania są inicjowane w chmurze. 

```json
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
```

#### <a name="direct-methods"></a>Bezpośrednie metody

Kategoria bezpośredniego metody śledzi interakcje odpowiedzi żądania wysyłane do poszczególnych urządzeń. Te żądania są inicjowane w chmurze. 

```json
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
```

### <a name="read-logs-from-azure-event-hubs"></a>Odczytaj dzienniki usługi Azure Event Hubs

Po skonfigurowaniu rejestrowania za pomocą ustawień diagnostycznych zdarzeń można utworzyć aplikacje, które zapoznały się dzienniki, dzięki czemu można podjąć odpowiednie działania w oparciu o informacje zawarte w nich. Ten przykładowy kod pobiera dzienniki z Centrum zdarzeń:

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

## <a name="use-azure-resource-health"></a>Użyj kondycji zasobów platformy Azure

Kondycja zasobów Azure umożliwia monitorowanie, czy Centrum IoT jest uruchomiona. Możesz także dowiedzieć się czy regionalnej awarii wpływające na kondycję Centrum IoT. Aby poznać konkretne szczegółowe informacje o stanie kondycji Centrum IoT Azure, zaleca się, że możesz [Użyj monitora Azure](#use-azure-monitor). 

Centrum IoT Azure wskazuje kondycji na poziomie regionalnym. Jeśli istnieje regionalnej awarii wpływające na Centrum IoT, stan kondycji jest pokazywana jako **nieznany**. Aby dowiedzieć się więcej na temat kontroli określonych kondycji, które wykonuje kondycji zasobów platformy Azure, zobacz [typów zasobów i kondycji sprawdza w kondycji zasobów platformy Azure][lnk-ARH-checks].

Aby sprawdzić kondycję Twojego centra IoT, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **kondycja usługi** > **kondycja zasobów**.
1. Wybierz subskrypcję z pola listy rozwijanej i **Centrum IoT**.

Aby dowiedzieć się więcej o tym, jak interpretować dane kondycji, zobacz [Przegląd kondycji zasobów platformy Azure][lnk-ARH]

## <a name="next-steps"></a>Kolejne kroki

- [Zrozumienie metryki Centrum IoT][lnk-metrics]
- [Zdalne monitorowanie IoT i powiadomienia przy użyciu usługi Azure Logic Apps łączenia z Centrum IoT i skrzynek pocztowych][lnk-monitoring-notifications]


[lnk-AM]: ../monitoring-and-diagnostics/index.yml
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-migrate]: iot-hub-migrate-to-diagnostics-settings.md
[lnk-AM-metrics]: ../monitoring-and-diagnostics/monitoring-supported-metrics.md
[lnk-AM-schemas]: ../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md
[lnk-ARH-checks]: ../service-health/resource-health-checks-resource-types.md
[lnk-monitoring-notifications]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
