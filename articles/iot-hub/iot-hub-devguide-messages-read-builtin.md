---
title: Zrozumienie wbudowany punkt końcowy usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — w tym artykule opisano jak odczytywanie komunikatów z urządzenia do chmury za pomocą wbudowanego, punktu końcowego zgodnego z Centrum zdarzeń.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 827d7d9a3d584342703a84dd2a42e5cda9b3a656
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579414"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Odczytywanie komunikatów przesyłanych z urządzeń do chmury z wbudowanego punktu końcowego

Domyślnie komunikaty są kierowane do wbudowany punkt końcowy przeznaczonych dla usługi (**komunikaty/zdarzenia**) jest zgodna z [usługi Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Ten punkt końcowy jest aktualnie korzystać tylko z uwidocznionymi [AMQP](https://www.amqp.org/) protokół, port 5671. Usługa IoT hub udostępnia poniższe właściwości do umożliwiają kontrolowanie wbudowanych zgodnego z Centrum zdarzeń obsługi komunikatów punktu końcowego **komunikaty/zdarzenia**.

| Właściwość            | Opis |
| ------------------- | ----------- |
| **Liczba partycji** | Ustaw tę właściwość w momencie jego tworzenia, aby zdefiniować liczbę [partycje](../event-hubs/event-hubs-features.md#partitions) dla przyjmowanie zdarzeń urządzenia do chmury. |
| **Czas przechowywania**  | Ta właściwość określa, jak długo w dniach, komunikaty są zachowywane przez usługę IoT Hub. Wartość domyślna to jeden dzień, ale może wzrosnąć do siedmiu dni. |

Usługa IoT Hub umożliwia przechowywanie danych wbudowane usługi Event hubs przez maksymalnie 7 dni. Możesz ustawić czas przechowywania, podczas tworzenia Centrum IoT Hub. Czas przechowywania danych w usłudze IoT Hub, zależy od warstwę Centrum IoT i typ jednostki. Pod względem rozmiaru wbudowane usługi Event Hubs można przechować wiadomości maksymalny rozmiar komunikatu do co najmniej 24 godziny limitu przydziału. Na przykład dla 1 jednostki S1 usługi IoT Hub zapewnia wystarczająco dużego magazynu, aby zachować co najmniej 400 K wiadomości 4 k rozmiar każdego. Urządzenia są wysyłane wiadomości mniejszych, mogą zostać zachowane przez dłużej (do 7 dni) w zależności od tego, jaka jest pojemność magazynu jest używane. Firma Microsoft gwarantuje, zachowując dane czas przechowywania określona co najmniej.

Usługa IoT Hub umożliwia także zarządzanie grupy konsumentów na wbudowane urządzenia do chmury otrzymywać punktu końcowego.

Jeśli używasz [routing komunikatów](iot-hub-devguide-messages-d2c.md) i [rezerwowego trasy](iot-hub-devguide-messages-d2c.md#fallback-route) jest włączone, wszystkie komunikaty, które nie są zgodne z zapytaniem na wszystkie trasy, przejdź do wbudowany punkt końcowy. Jeśli wyłączysz tę trasę rezerwowy, wiadomości, które nie są zgodne z dowolnego zapytania są porzucane.

Możesz zmodyfikować czas przechowywania albo programowo przy użyciu [dostawcy zasobów usługi IoT Hub interfejsów API REST](/rest/api/iothub/iothubresource), lub za pomocą [witryny Azure portal](https://portal.azure.com).

Usługa IoT Hub udostępnia **komunikaty/zdarzenia** wbudowany punkt końcowy dla usług zaplecza do odczytywania komunikatów przesyłanych z chmury do urządzeń odebrane przez Centrum. Ten punkt końcowy jest zdarzenie zgodnego z Centrum, co pozwala na użyć innych mechanizmów usługi Event Hubs obsługuje do odczytywania komunikatów.

## <a name="read-from-the-built-in-endpoint"></a>Odczyt z wbudowanego punktu końcowego

Niektóre integracji produktu i zestawy SDK z centrów zdarzeń zapoznali się z Centrum IoT Hub i umożliwiają nawiązywanie połączenia z wbudowanego punktu końcowego za pomocą parametrów połączenia usługi IoT hub.

Gdy używasz Event Hubs z zestawów SDK lub integracji produktu, które znają usługę IoT Hub, musisz punktu końcowego zgodnego z Centrum zdarzeń oraz nazwę zgodną z Centrum zdarzeń. Te wartości można pobrać z portalu w następujący sposób:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do Centrum IoT hub.

2. Kliknij przycisk **wbudowanych punktach końcowych**.

3. **Zdarzenia** sekcja zawiera następujące wartości: **Partycje**, **nazwę zgodną z Centrum zdarzeń**, **punktu końcowego zgodnego z Centrum zdarzeń**, **czas przechowywania**, i **grupy konsumentów**.

    ![Ustawienia komunikacji między urządzeniem i chmurą](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

W portalu pole punktu końcowego zgodnego z Centrum zdarzeń zawiera pełne parametry połączenia usługi Event Hubs, wygląda następująco: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456**. Jeśli używasz zestawu SDK wymaga inne wartości, ich będzie:

| Name (Nazwa) | Wartość |
| ---- | ----- |
| Endpoint | sb://abcd1234namespace.servicebus.windows.net/ |
| Nazwa hosta | abcd1234namespace.servicebus.windows.net |
| Przestrzeń nazw | abcd1234namespace |

Następnie można użyć dowolnego zasady dostępu współdzielonego, który ma **ServiceConnect** uprawnień, aby nawiązać połączenie z określonym Centrum zdarzeń.

Zestawy SDK, w którym można nawiązać połączenia z wbudowanego punktu końcowego zgodnego z Centrum zdarzeń, który usługa IoT Hub udostępnia obejmują:

| Język | SDK | Przykład | Uwagi |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Szybki start](quickstart-send-telemetry-dotnet.md) | Używa informacji o zgodnych z usługą Event Hubs |
 Java | https://github.com/Azure/azure-event-hubs-java | [Szybki start](quickstart-send-telemetry-java.md) | Używa informacji o zgodnych z usługą Event Hubs |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Szybki start](quickstart-send-telemetry-node.md) | Używa parametrów połączenia usługi IoT Hub |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | Używa parametrów połączenia usługi IoT Hub |

Integracje produktów, których można używać z wbudowanego punktu końcowego zgodnego z Centrum zdarzeń, który usługa IoT Hub udostępnia obejmują:

* [Usługa Azure Functions](https://docs.microsoft.com/azure/azure-functions/). Zobacz [przetwarzanie danych z usługi IoT Hub przy użyciu usługi Azure Functions](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/). Zobacz [Stream dane jako dane wejściowe do usługi Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/). Zobacz [dodawania źródła zdarzeń Centrum IoT do środowiska usługi Time Series Insights](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Apache Storm spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Możesz wyświetlić [elementu spout źródła](https://github.com/apache/storm/tree/master/external/storm-eventhubs) w witrynie GitHub.
* [Integracja platformy Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Usługa Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat punktów końcowych usługi IoT Hub, zobacz [punktów końcowych usługi IoT Hub](iot-hub-devguide-endpoints.md).

* [Przewodników Szybki Start](quickstart-send-telemetry-node.md) pokazują, jak wysyłać komunikaty urządzenie chmura z symulowanych urządzeń i odczytu komunikatów z wbudowanego punktu końcowego. 

Aby uzyskać więcej informacji, zobacz [komunikaty urządzenia do chmury usługi IoT Hub procesu przy użyciu tras](tutorial-routing.md) samouczka.

* Jeśli chcesz przekierowywać komunikatów przesyłanych z chmury do urządzenia do niestandardowych punktów końcowych, zobacz [trasy wiadomość i niestandardowe punkty końcowe na użytek komunikatów z urządzenia do chmury](iot-hub-devguide-messages-read-custom.md).
