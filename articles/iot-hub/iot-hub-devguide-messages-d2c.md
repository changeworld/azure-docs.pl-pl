---
title: Opis routingu wiadomości usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — jak używać routingu wiadomości do wysyłania wiadomości z urządzenia do chmury. Zawiera informacje dotyczące wysyłania danych telemetrycznych i nie telemetrycznych.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: d10744f2536cdf89115cdccd0bea6f1e5155774c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370461"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Wysyłanie wiadomości z urządzenia do chmury za pomocą usługi IoT Hub do wysyłania wiadomości z urządzenia do chmury do różnych punktów końcowych

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Routing wiadomości umożliwia wysyłanie wiadomości z urządzeń do usług w chmurze w sposób zautomatyzowany, skalowalny i niezawodny. Routingu wiadomości może być używany do: 

* **Wysyłanie komunikatów telemetrycznych urządzenia, a także zdarzeń,** a mianowicie zdarzeń cyklu życia urządzenia i bliźniaczej reprezentacji urządzenia zmieniają zdarzenia do wbudowanego punktu końcowego i niestandardowych punktów końcowych. Dowiedz się więcej o [wyznaczaniu punktów końcowych](#routing-endpoints).

* **Filtrowanie danych przed przekierowaniem do różnych punktów końcowych** przez zastosowanie zapytań rozszerzonych. Routing wiadomości umożliwia wykonywanie zapytań dotyczących właściwości wiadomości i treści wiadomości, a także tagów bliźniaczej reprezentacji urządzenia i właściwości bliźniaczej reprezentacji urządzenia. Dowiedz się więcej o używaniu [zapytań w routingu wiadomości](iot-hub-devguide-routing-query-syntax.md).

Usługa IoT Hub potrzebuje dostępu do zapisu do tych punktów końcowych usługi do routingu wiadomości do pracy. Jeśli skonfigurujesz swoje punkty końcowe za pośrednictwem witryny Azure portal, niezbędne uprawnienia są dodawane dla Ciebie. Upewnij się, że skonfigurować usługi do obsługi oczekiwanej przepływności. Na przykład jeśli używasz Usługi event hubs jako niestandardowego punktu końcowego, należy skonfigurować **jednostki przepływności** dla tego centrum zdarzeń, aby mógł obsłużyć przychód zdarzeń, które mają być wysyłane za pośrednictwem routingu komunikatów usługi IoT Hub. Podobnie podczas korzystania z kolejki usługi Service Bus jako punktu końcowego, należy skonfigurować **maksymalny rozmiar,** aby upewnić się, że kolejka może pomieścić wszystkie dane przychodzące, dopóki nie jest wychodzący przez konsumentów. Podczas pierwszej konfiguracji rozwiązania IoT może być konieczne monitorowanie dodatkowych punktów końcowych i wprowadzanie wszelkich niezbędnych korekt dla rzeczywistego obciążenia.

Centrum IoT hub definiuje [wspólny format](iot-hub-devguide-messages-construct.md) dla wszystkich wiadomości z urządzenia do chmury dla współdziałania między protokołami. Jeśli wiadomość pasuje do wielu tras, które wskazują ten sam punkt końcowy, Usługa IoT Hub dostarcza komunikat do tego punktu końcowego tylko raz. W związku z tym nie trzeba konfigurować deduplikacji w kolejce lub temacie usługi Service Bus. W kolejkach podzielonych na partycje koligacja partycji gwarantuje kolejność komunikatów. Użyj tego samouczka, aby dowiedzieć się, jak [skonfigurować routing wiadomości](tutorial-routing.md).

## <a name="routing-endpoints"></a>Punkty końcowe routingu

Centrum IoT hub ma domyślny wbudowany punkt końcowy **(wiadomości/zdarzenia),** który jest zgodny z centrum zdarzeń. Można utworzyć [niestandardowe punkty końcowe](iot-hub-devguide-endpoints.md#custom-endpoints) do kierowania wiadomości do łącząc inne usługi w subskrypcji do Usługi IoT Hub. 

Każda wiadomość jest kierowana do wszystkich punktów końcowych, których zapytania routingu są zgodne. Innymi słowy wiadomość może być kierowane do wielu punktów końcowych.

Usługa IoT Hub obsługuje obecnie następujące usługi jako niestandardowe punkty końcowe:

### <a name="built-in-endpoint"></a>Wbudowany punkt końcowy

Za pomocą standardowej [integracji z centrami zdarzeń i SDK](iot-hub-devguide-messages-read-builtin.md) można odbierać wiadomości z urządzenia do chmury z wbudowanego punktu końcowego **(wiadomości/zdarzenia).** Po utworzeniu Route dane przestaje przepływać do wbudowanego punktu końcowego, chyba że trasa jest tworzony do tego punktu końcowego.

### <a name="azure-storage"></a>Azure Storage

Istnieją dwie usługi magazynu Usługi IoT Hub można kierować wiadomości do — [Usługi Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) i usługi Azure Data Lake Storage [Gen2](../storage/blobs/data-lake-storage-introduction.md) (ADLS Gen2) kont. Konta usługi Azure Data Lake Storage to hierarchiczne konta magazynu z włączoną [przestrzenią nazw,](../storage/blobs/data-lake-storage-namespace.md)zbudowane na podstawie magazynu obiektów blob. Oba te obiekty używają obiektów blob do ich przechowywania.

Usługa IoT Hub obsługuje zapisywanie danych w usłudze Azure Storage w formacie [Apache Avro,](https://avro.apache.org/) a także w formacie JSON. Wartość domyślna to AVRO. Format kodowania można ustawić tylko wtedy, gdy punkt końcowy magazynu obiektów blob jest skonfigurowany. Formatu nie można edytować dla istniejącego punktu końcowego. Podczas korzystania z kodowania JSON, należy ustawić contentType do **application/json** i contentEncoding do **UTF-8** we [właściwościach systemu](iot-hub-devguide-routing-query-syntax.md#system-properties)wiadomości . Obie te wartości są niewrażliwe na argumenty. Jeśli kodowanie zawartości nie jest ustawione, centrum IoT hub zapisze wiadomości w formacie zakodowanym w podstawowej 64. Format kodowania można wybrać za pomocą interfejsu API Tworzenie lub aktualizowanie interfejsu REST w centrum IoT, w szczególności [właściwości Usługi RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), witryny Azure portal, [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)lub programu Azure [PowerShell.](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0) Na poniższym diagramie pokazano, jak wybrać format kodowania w witrynie Azure portal.

![Kodowanie punktu końcowego magazynu obiektów blob](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

Usługa IoT Hub partii wiadomości i zapisuje dane do magazynu, gdy partia osiągnie określony rozmiar lub upłynął określony czas. Centrum IoT hub domyślnie ma następującą konwencję nazewnictwa plików: 

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Możesz użyć dowolnej konwencji nazewnictwa plików, jednak należy użyć wszystkich wymienionych tokenów. Usługa IoT Hub będzie zapisywać do pustego obiektu blob, jeśli nie ma żadnych danych do zapisu.

Zalecamy listę obiektów blobs lub plików, a następnie iteracji nad nimi, aby upewnić się, że wszystkie obiekty blob lub pliki są odczytywane bez żadnych założeń partycji. Zakres partycji może potencjalnie ulec zmianie podczas [pracy awaryjnej zainicjowanej przez](iot-hub-ha-dr.md#microsoft-initiated-failover) firmę Microsoft lub [ręcznej pracy awaryjnej](iot-hub-ha-dr.md#manual-failover)usługi IoT Hub. Za pomocą [interfejsu API obiektów blob listy](https://docs.microsoft.com/rest/api/storageservices/list-blobs) można wyliczyć listę obiektów blob lub [listę interfejsu API ADLS Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) dla listy plików. Zobacz poniższą próbkę jako wskazówki.

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

> [!NOTE]
> Jeśli twoje konto magazynu ma konfiguracje zapory, które ograniczają łączność usługi IoT Hub, należy rozważyć użycie [zaufanego wyjątku firmy Microsoft (dostępnego](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) w wybranych regionach dla centrów IoT z tożsamością usługi zarządzanej).

Aby utworzyć konto magazynu zgodne z usługą Azure Data Lake Gen2, utwórz nowe konto magazynu w wersji 2 i wybierz *włączone* pole *hierarchicznego obszaru nazw* na karcie **Zaawansowane,** jak pokazano na poniższej ilustracji:

![Wybierz magazyn usługi Azure Date Lake Gen2](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)


### <a name="service-bus-queues-and-service-bus-topics"></a>Kolejki magistrali usług i tematy magistrali usług

Kolejki i tematy usługi Service Bus używane jako punkty końcowe usługi IoT Hub nie mogą mieć **włączonych sesji** ani **wykrywania duplikatów.** Jeśli którakolwiek z tych opcji jest włączona, punkt końcowy jest wyświetlany jako **nieosiągalny** w witrynie Azure portal.

> [!NOTE]
> Jeśli zasób magistrali usług ma konfiguracje zapory, które ograniczają łączność usługi IoT Hub, należy rozważyć użycie [zaufanego wyjątku firmy Microsoft (dostępnego](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing) w wybranych regionach dla centrów IoT z tożsamością usługi zarządzanej).


### <a name="event-hubs"></a>Usługa Event Hubs

Oprócz wbudowanego punktu końcowego zgodnego z koncentratorami zdarzeń można również kierować dane do niestandardowych punktów końcowych typu Usługi zdarzeń. 

> [!NOTE]
> Jeśli zasób centrów zdarzeń ma konfiguracje zapory, które ograniczają łączność usługi IoT Hub, należy rozważyć użycie [zaufanego wyjątku firmy Microsoft (dostępnego](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) w wybranych regionach dla centrów IoT z tożsamością usługi zarządzanej).


## <a name="reading-data-that-has-been-routed"></a>Odczyt danych, które zostały przekierowane

Trasę można skonfigurować, wykonując ten [samouczek](tutorial-routing.md).

Skorzystaj z poniższych samouczków, aby dowiedzieć się, jak odczytać wiadomość z punktu końcowego.

* Odczyt z [wbudowanego punktu końcowego](quickstart-send-telemetry-node.md)

* Odczyt z [magazynu obiektów Blob](../storage/blobs/storage-blob-event-quickstart.md)

* Czytanie z [centrów zdarzeń](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Odczyt z [kolejek magistrali usług](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Przeczytaj z [tematów usługi Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)


## <a name="fallback-route"></a>Trasa rezerwowa

Trasa rezerwowa wysyła wszystkie komunikaty, które nie spełniają warunków kwerendy na żadnej z istniejących tras do wbudowanych centrów zdarzeń **(wiadomości/zdarzeń),** które są zgodne z [Centrum zdarzeń.](/azure/event-hubs/) Jeśli routing wiadomości jest włączony, można włączyć funkcję trasy rezerwowej. Po utworzeniu trasy dane przestają przepływać do wbudowanego punktu końcowego, chyba że trasa zostanie utworzona do tego punktu końcowego. Jeśli nie ma żadnych tras do wbudowanego punktu końcowego i trasa rezerwowa jest włączona, tylko komunikaty, które nie pasują do żadnych warunków kwerendy na trasach będą wysyłane do wbudowanego punktu końcowego. Ponadto jeśli wszystkie istniejące trasy zostaną usunięte, trasa rezerwowa musi być włączona, aby odbierać wszystkie dane w punkcie końcowym wbudowanym.

Można włączyć/wyłączyć trasę rezerwową w bloku routingu wiadomości >portalu Azure. Można również użyć usługi Azure Resource Manager dla [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) używać niestandardowego punktu końcowego dla trasy rezerwowej.

## <a name="non-telemetry-events"></a>Zdarzenia inne niż telemetryczne

Oprócz telemetrii urządzenia routing wiadomości umożliwia również wysyłanie zdarzeń podwójnej zmiany urządzenia, zdarzeń cyklu życia urządzenia i zdarzeń zmiany podwójnej reprezentacji cyfrowej (w publicznej wersji zapoznawczej). Na przykład jeśli trasa jest tworzona ze źródłem danych ustawionym na **zdarzenia zmiany bliźniaczej reprezentacji urządzenia,** usługa IoT Hub wysyła wiadomości do punktu końcowego, które zawierają zmianę w bliźniaczej reprezentacji urządzenia. Podobnie jeśli trasa jest tworzona ze źródłem danych ustawionym na **zdarzenia cyklu życia urządzenia,** Usługa IoT Hub wysyła komunikat informujący, czy urządzenie zostało usunięte, czy utworzone. Na koniec w ramach [publicznej wersji zapoznawczej IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md)deweloper może tworzyć trasy ze źródłem danych ustawionym na cyfrowe zdarzenia zmiany **bliźniaczej reprezentacji,** a usługa IoT Hub wysyła wiadomości za każdym razem, gdy [cyfrowa właściwość](../iot-pnp/iot-plug-and-play-glossary.md) bliźniaczej reprezentacji jest ustawiona lub zmieniana, zostanie zastąpiona [cyfrowa bliźniaczka bliźniaczej](../iot-pnp/iot-plug-and-play-glossary.md) reprezentacji lub gdy zdarzenie zmiany nastąpi dla bliźniaczej reprezentacji urządzenia źródłowego.

[Usługa IoT Hub integruje się również z usługą Azure Event Grid](iot-hub-event-grid.md) w celu publikowania zdarzeń urządzenia w celu obsługi integracji w czasie rzeczywistym i automatyzacji przepływów pracy na podstawie tych zdarzeń. Zobacz kluczowe [różnice między routingu wiadomości i usługi Event Grid,](iot-hub-event-grid-routing-comparison.md) aby dowiedzieć się, który działa najlepiej w twoim scenariuszu.

## <a name="testing-routes"></a>Trasy testowania

Podczas tworzenia nowej trasy lub edytowania istniejącej trasy należy przetestować kwerendę marszruty za pomocą przykładowego komunikatu. Można przetestować poszczególne trasy lub przetestować wszystkie trasy naraz i żadne komunikaty nie są kierowane do punktów końcowych podczas testu. Usługa Azure portal, usługa Azure Resource Manager, Azure PowerShell i azure cli mogą być używane do testowania. Wyniki pomagają określić, czy przykładowy komunikat pasował do kwerendy, komunikat nie był zgodny z kwerendą, czy nie można uruchomić testu, ponieważ przykładowa składnia wiadomości lub kwerendy są niepoprawne. Aby dowiedzieć się więcej, zobacz [Trasa testowa](/rest/api/iothub/iothubresource/testroute) i [Testowanie wszystkich tras](/rest/api/iothub/iothubresource/testallroutes).

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>Zamawianie gwarancji z co najmniej jednorazową dostawą

Routing komunikatów usługi IoT Hub gwarantuje uporządkowane i co najmniej raz dostarczanie wiadomości do punktów końcowych. Oznacza to, że mogą istnieć zduplikowane wiadomości i serii wiadomości mogą być retransmitowane honorowanie oryginalnej kolejności wiadomości. Na przykład, jeśli oryginalna kolejność wiadomości to [1,2,3,4], możesz otrzymać sekwencję wiadomości, taką jak [1,2,1,2,3,1,2,3,4]. Gwarancja zamówienia jest taka, że jeśli kiedykolwiek otrzymasz wiadomość [1], zawsze będzie za nią podążać [2,3,4].

W przypadku obsługi duplikatów komunikatów zaleca się stemplowanie unikatowego identyfikatora we właściwościach aplikacji wiadomości w miejscu pochodzenia, który jest zwykle urządzeniem lub modułem. Usługa korzystająca z wiadomości może obsługiwać zduplikowane wiadomości przy użyciu tego identyfikatora.

## <a name="latency"></a>Opóźnienie

Podczas rozsyłania komunikatów telemetrycznych urządzenia do chmury przy użyciu wbudowanych punktów końcowych, występuje niewielki wzrost opóźnienia end-to-end po utworzeniu pierwszej trasy.

W większości przypadków średni wzrost opóźnienia jest mniejszy niż 500 ms. Opóźnienie można monitorować przy użyciu **routingu: opóźnienie komunikatów/zdarzeń** lub **d2c.endpoints.latency.builtIn.events** metryka IoT Hub. Tworzenie lub usuwanie dowolnej trasy po pierwszej nie ma wpływu na opóźnienie end-to-end.

## <a name="monitoring-and-troubleshooting"></a>Monitorowanie i rozwiązywanie problemów

Usługa IoT Hub udostępnia kilka metryk związanych z routingiem i punktami końcowymi, aby zapewnić przegląd kondycji centrum i wysłanych wiadomości. Można połączyć informacje z wielu metryk, aby zidentyfikować główną przyczynę problemów. Na przykład użyj **routingu metryki: komunikaty telemetryczne porzucone** lub **d2c.telemetry.egress.dropped,** aby zidentyfikować liczbę wiadomości, które zostały usunięte, gdy nie pasują do zapytań na żadnej z tras i trasy rezerwowej została wyłączona. [Metryki usługi IoT Hub](iot-hub-metrics.md) zawierają listę wszystkich metryk, które są domyślnie włączone dla centrum IoT Hub.

Można użyć interfejsu API REST [Get Endpoint Health,](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) aby uzyskać [stan kondycji](iot-hub-devguide-endpoints.md#custom-endpoints) punktów końcowych. Zaleca się użycie [metryk Usługi IoT Hub związanych](iot-hub-metrics.md) z opóźnieniem komunikatów routingu do identyfikowania i debugowania błędów, gdy kondycja punktu końcowego jest martwa lub w złej kondycji. Na przykład dla centrum zdarzeń typu punktu końcowego można monitorować **d2c.endpoints.latency.eventHubs**. Stan punktu końcowego w złej kondycji zostaną zaktualizowane do dobrej kondycji, gdy Centrum IoT ustanowił ostatecznie spójny stan kondycji.

Za pomocą **dzienników** diagnostycznych tras w [ustawieniach diagnostycznych](../iot-hub/iot-hub-monitor-resource-health.md)usługi Azure Monitor można śledzić błędy występujące podczas oceny zapytania routingu i kondycji punktu końcowego, jak postrzegane przez centrum IoT Hub, na przykład, gdy punkt końcowy jest martwy. Te dzienniki diagnostyczne mogą być wysyłane do dzienników usługi Azure Monitor, usługi Event Hubs lub usługi Azure Storage w celu przetwarzania niestandardowego.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak tworzyć trasy komunikatów, zobacz [Przetwarzanie komunikatów między urządzeniami w usłudze IoT Hub przy użyciu tras](tutorial-routing.md).

* [Jak wysyłać wiadomości z urządzenia do chmury](quickstart-send-telemetry-node.md)

* Aby uzyskać informacje na temat zestawów SDK, których można używać do wysyłania komunikatów z urządzenia do chmury, zobacz [Zestawy SDK IoT platformy Azure](iot-hub-devguide-sdks.md).
