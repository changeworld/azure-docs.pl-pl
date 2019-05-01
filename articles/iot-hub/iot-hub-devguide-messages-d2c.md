---
title: Poznanie routingu komunikatów usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dla deweloperów — jak wysyłać komunikaty urządzenie chmura z za pomocą routingu komunikatów. Zawiera informacje na temat wysyłania danych telemetrycznych i danych telemetrycznych innych.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: fddea12d4c6b7d09d87174d29c645ef6da54af6f
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917420"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Umożliwia routing komunikatów usługi IoT Hub wysyłanie komunikatów z urządzenia do chmury do różnych punktów końcowych

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Routing komunikatów umożliwia wysyłanie komunikatów z urządzenia do usług w chmurze w sposób zautomatyzowanych, skalowalnych i niezawodnych. Routing komunikatów może służyć do: 

* **Wysyłanie komunikatów z urządzeń danych telemetrycznych, a także zdarzenia** , zdarzenia cyklu życia urządzenia i zdarzenia zmian do wbudowanych-endpoint i niestandardowych punktów końcowych bliźniaczej reprezentacji urządzenia. Dowiedz się więcej o [routingu punktów końcowych](#routing-endpoints).

* **Filtrowanie danych przed przesłaniem go do różnych punktów końcowych** przez zastosowanie zaawansowanych zapytań. Routing komunikatów umożliwia zapytania właściwości wiadomości oraz treść wiadomości, a także tagów bliźniaczych reprezentacji urządzeń i właściwości bliźniaczych reprezentacji urządzeń. Dowiedz się więcej o korzystaniu z [zapytania w routingu komunikatów](iot-hub-devguide-routing-query-syntax.md).

Usługa IoT Hub wymaga dostępu do tych punktów końcowych usługi zapisu do rozsyłania wiadomości do pracy. Jeśli skonfigurujesz punktów końcowych za pośrednictwem witryny Azure portal, niezbędne uprawnienia są dodawane. Upewnij się, że konfigurowanie usługi do obsługi oczekiwanej przepływności. Podczas pierwszej konfiguracji rozwiązania IoT, może być konieczne monitorowanie dodatkowych punktów końcowych i wprowadź wymagane zmiany dotyczące rzeczywistego obciążenia.

Definiuje usługę IoT Hub [typowego formatu](iot-hub-devguide-messages-construct.md) dla wszystkich urządzeń z chmurą messaging współdziałania w ramach różnych protokołów. Jeśli komunikat pasuje wiele tras, które wskazują na ten sam punkt końcowy, IoT Hub dostarczy komunikatu do określonego punktu końcowego tylko raz. W związku z tym nie trzeba polecenia Konfiguruj deduplikację na tematu lub kolejki usługi Service Bus. Partycjonowane kolejki koligacji partycji gwarantuje, kolejność komunikatów. Użyj tego samouczka, aby dowiedzieć się, jak [skonfigurować routing komunikatów](tutorial-routing.md).

## <a name="routing-endpoints"></a>Punkty końcowe routingu

Centrum IoT hub ma domyślnego wbudowanych-w-punktu końcowego (**komunikaty/zdarzenia**) jest zgodna z usługą Event Hubs. Możesz utworzyć [niestandardowe punkty końcowe](iot-hub-devguide-endpoints.md#custom-endpoints) do wyznaczania tras, łącząc innych usług w Twojej subskrypcji do Centrum IoT. Usługa IoT Hub obecnie obsługuje następujące usługi jako niestandardowe punkty końcowe:

### <a name="built-in-endpoint"></a>Wbudowany punkt końcowy

Można użyć standardowego [integracji usługi Event Hubs i zestawy SDK](iot-hub-devguide-messages-read-builtin.md) do odbierania komunikatów z urządzenia do chmury z wbudowanego punktu końcowego (**komunikaty/zdarzenia**). Po utworzeniu trasy dane przestaną docierać do wbudowanych-w-punktu końcowego, chyba że trasa służy do tego punktu końcowego.

### <a name="azure-blob-storage"></a>Azure Blob Storage

Centrum IoT Hub obsługuje zapisywanie danych w usłudze Azure Blob Storage w [Apache Avro](https://avro.apache.org/) oraz JSON format. Funkcja do zakodowania w formacie JSON jest w wersji zapoznawczej we wszystkich regionach, w których IoT Hub jest dostępna, z wyjątkiem wschodnie stany USA, zachodnie stany USA i Europa Zachodnia. Wartość domyślna to AVRO. Format kodowania można ustawić tylko w przypadku skonfigurowania punktu końcowego magazynu obiektów blob. Nie można edytować format dla istniejącego punktu końcowego. Korzystając z formatu JSON, kodowanie, należy ustawić obiekt contentType na format JSON i contentEncoding na UTF-8 w komunikacie [właściwości systemu](iot-hub-devguide-routing-query-syntax.md#system-properties). Można wybrać format kodowania za pomocą Tworzenie Centrum IoT lub aktualizacji interfejsu API REST, w szczególności [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), witryny Azure Portal [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest) lub [platformy Azure Program PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0). Na poniższym diagramie przedstawiono sposób wybierania formatu kodowania w witrynie Azure Portal.

![Obiekt blob magazynu punktu końcowego kodowania](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

Usługa IoT Hub partii komunikatów i zapisuje dane do obiektu blob, zawsze, gdy zadanie wsadowe osiągnie określony rozmiar lub upłynął określoną ilość czasu. Usługa IoT Hub wartością domyślną jest następująca Konwencja nazewnictwa pliku:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Może używać dowolnego konwencję nazewnictwa plików, jednak należy użyć listy wszystkich tokenów. Usługi IoT Hub będzie zapisywać do pustego obiektu blob, jeśli nie ma żadnych danych do zapisu.

Routing do magazynu obiektów blob, firma Microsoft zaleca funkcji rejestrowanie obiektów blob, a następnie Iterowanie po nich, aby upewnić się, że wszystkie kontenery są odczytywane bez wprowadzania żadnych założeń partycji. Zakres partycji potencjalnie mogą ulec zmianie podczas [inicjowanych przez Microsoft trybu failover](iot-hub-ha-dr.md#microsoft-initiated-failover) lub usługi IoT Hub [ręczna praca awaryjna](iot-hub-ha-dr.md#manual-failover-preview). Możesz użyć [listy obiektów blob API](https://docs.microsoft.com/rest/api/storageservices/list-blobs) wyliczyć listy obiektów blob. Jako wskazówki, zobacz poniższy przykład.

   ```csharp
        public void ListBlobsInContainer(string containerName, string iothub)
        {
            var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
            var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
            if (cloudBlobContainer.Exists())
            {
                var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
                foreach (IListBlobItem item in results)
                {
                    Console.WriteLine(item.Uri);
                }
            }
        }
   ```

### <a name="service-bus-queues-and-service-bus-topics"></a>Kolejki usługi Service Bus i tematów usługi Service Bus

Kolejki usługi Service Bus i tematy, używane jako punktów końcowych usługi IoT Hub nie może mieć **sesje** lub **wykrywania duplikatów** włączone. Jeśli jedno z tych opcji są włączone, punkt końcowy jest wyświetlany jako **informujący** w witrynie Azure portal.

### <a name="event-hubs"></a>Event Hubs

Oprócz wbudowanych — Event Hubs punkt końcowy zgodny można również kierować dane do niestandardowych punktów końcowych typu Event Hubs. 

## <a name="reading-data-that-has-been-routed"></a>Odczyt danych, który został przesłany

Można skonfigurować trasę, postępując zgodnie z dokumentem [samouczek](tutorial-routing.md).

Użyj następujących samouczków dowiesz się, jak odczytać wiadomość z punktu końcowego.

* Odczytywanie z [wbudowanych-endpoint](quickstart-send-telemetry-node.md)

* Odczytywanie z [magazynu obiektów Blob](../storage/blobs/storage-blob-event-quickstart.md)

* Odczytywanie z [usługi Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Odczytywanie z [kolejki usługi Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Odczytywanie [tematy usługi Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Rezerwowy trasy

Rezerwowy trasa wysyła wszystkie komunikaty, które nie spełniają warunki zapytania na dowolnym istniejące trasy do wbudowanych w — Event Hubs (**komunikaty/zdarzenia**), która jest zgodna z [usługi Event Hubs](/azure/event-hubs/). Routing komunikatów jest włączona, po włączeniu możliwości rezerwowego trasy. Po utworzeniu trasy dane przestaną docierać do wbudowanych-w-punktu końcowego, chyba że zostanie utworzona trasa tego punktu końcowego. Jeśli nie ma żadnych tras do wbudowanych-w-punktu końcowego i rezerwowego trasy jest włączona, tylko komunikaty, które nie są zgodne wszelkie warunki zapytania na trasach, będą wysyłane do wbudowanych-w-punktu końcowego. Również usunięcie wszystkich istniejących tras rezerwowego trasy musi mieć możliwość odbierania wszystkie dane w wbudowanych-w-punkcie końcowym. 

Możesz włączyć/wyłączyć rezerwowego trasy w witrynie Azure Portal -> Routing komunikatów bloku. Można również użyć usługi Azure Resource Manager dla [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) do użycia niestandardowego punktu końcowego dla trasy rezerwowego.

## <a name="non-telemetry-events"></a>Zdarzenia inne niż telemetrii

Oprócz danych telemetrycznych z urządzenia routing komunikatów umożliwia także wysyłanie zdarzeń zmiany bliźniaczych reprezentacji urządzeń i zdarzenia cyklu życia urządzenia. Na przykład, jeśli trasa jest tworzony ze źródłem danych, ustaw **zdarzenia zmiany w bliźniaczej reprezentacji urządzenia**, usługi IoT Hub wysyła wiadomości do punktu końcowego, które zawierają zmiany w bliźniaczej reprezentacji urządzenia. Podobnie jeśli trasa jest tworzony ze źródłem danych, ustaw **zdarzenia cyklu życia urządzenia**, usługi IoT Hub będzie wysyłać komunikat wskazujący, czy urządzenia został usunięty lub utworzony. 

[Usługa IoT Hub integruje się również z usługi Azure Event Grid](iot-hub-event-grid.md) publikowanie zdarzenia urządzenia w celu obsługi integracji w czasie rzeczywistym i automatyzacji przepływów pracy na podstawie tych zdarzeń. Klucz [różnice między routing komunikatów i Event Grid](iot-hub-event-grid-routing-comparison.md) Aby dowiedzieć się, w której działa najlepiej dla danego scenariusza.

## <a name="testing-routes"></a>Testowanie trasy

Podczas tworzenia nowej trasy lub Edytuj istniejącą trasę, należy przetestować zapytania trasy z przykładowy komunikat. Można przetestować indywidualnych tras lub przetestuj wszystkie trasy tylko raz, a żadne komunikaty są kierowane do punktów końcowych podczas testu. Azure Portal, usługi Azure Resource Manager, programu Azure PowerShell i wiersza polecenia platformy Azure może służyć do testowania. Wyniki pomagają ustalić, czy przykładowy komunikat wynik kwerendy, komunikat jest niezgodna z zapytania lub nie przebiegu testu, ponieważ składnia komunikatu lub zapytanie przykładowe są niepoprawne. Aby dowiedzieć się więcej, zobacz [trasy testu](/rest/api/iothub/iothubresource/testroute) i [Testuj wszystkie trasy](/rest/api/iothub/iothubresource/testallroutes).

## <a name="latency"></a>Opóźnienie

Podczas rozsyłania komunikatów telemetrycznych przesyłanych z urządzenia do chmury za pomocą wbudowanych punktach końcowych po utworzeniu pierwszego trasy jest niewielki wzrost opóźnienia end-to-end.

W większości przypadków Średni czas oczekiwania jest mniej niż 500 ms. Można monitorować za pomocą opóźnienie **routingu: komunikat o opóźnieniu komunikaty/zdarzenia** lub **d2c.endpoints.latency.builtIn.events** metryki usługi IoT Hub. Tworzenie lub usuwanie jakiejkolwiek trasy po pierwszy z nich nie ma wpływu na opóźnienie end-to-end.

## <a name="monitoring-and-troubleshooting"></a>Monitorowanie i rozwiązywanie problemów

IoT Hub udostępnia kilka routingu i punktu końcowego powiązane metryki, aby Przegląd kondycji Centrum i wysłane wiadomości. Możesz połączyć informacje z wielu metryk, aby odkryć ich główną przyczynę problemów. Na przykład użyć metryki **routingu: porzucone komunikaty telemetryczne** lub **d2c.telemetry.egress.dropped** do identyfikowania liczbę wiadomości, które zostały usunięte, gdy nie odpowiadają one zapytań na żadnej trasy a trasy rezerwowego zostało wyłączone. [Metryki usługi IoT Hub](iot-hub-metrics.md) Wyświetla listę wszystkich metryk, które są domyślnie włączone dla usługi IoT Hub.

Można użyć interfejsu API REST [kondycji punktu końcowego uzyskać](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) można pobrać [stan kondycji](iot-hub-devguide-endpoints.md#custom-endpoints) punktów końcowych. Firma Microsoft zaleca używanie [metryki usługi IoT Hub](iot-hub-metrics.md) związane z routingu opóźnienie wiadomości, aby zidentyfikować i debugować błędy, gdy punkt końcowy kondycji jest nieużywany lub w złej kondycji. Na przykład w przypadku typu punktu końcowego usługi Event Hubs można monitorować **d2c.endpoints.latency.eventHubs**. Będzie można zaktualizować stan złej kondycji punktu końcowego do dobrej kondycji, gdy usługa IoT Hub ustanowiła ostatecznie spójny stan kondycji.

Za pomocą **trasy** dzienników diagnostycznych w usłudze Azure Monitor [ustawień diagnostycznych](../iot-hub/iot-hub-monitor-resource-health.md), możesz śledzi błędy podczas oceny routingu zapytania i punktu końcowego kondycji wyobrażenia Centrum IoT, na przykład gdy punkt końcowy nie istnieje. Te dzienniki diagnostyczne mogą być wysyłane do dzienników usługi Azure Monitor, usługa Event Hubs lub usługi Azure Storage do niestandardowego przetwarzania.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, jak utworzyć trasy wiadomości, zobacz [komunikaty urządzenia do chmury usługi IoT Hub procesu przy użyciu tras](tutorial-routing.md).

* [Jak wysyłać komunikaty urządzenie chmura](quickstart-send-telemetry-node.md)

* Aby uzyskać informacje dotyczące zestawów SDK, służy do wysyłania komunikatów urządzenie chmura, zobacz [Azure IoT SDKs](iot-hub-devguide-sdks.md).
