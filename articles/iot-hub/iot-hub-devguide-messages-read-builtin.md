---
title: Zrozumienie wbudowany punkt końcowy usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — w tym artykule opisano jak odczytywanie komunikatów z urządzenia do chmury za pomocą wbudowanego, punktu końcowego zgodnego z Centrum zdarzeń.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 02ea4b94f8d1442360bebb36fdbba13d973f8555
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242419"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Odczytywanie komunikatów przesyłanych z urządzeń do chmury z wbudowanego punktu końcowego

Domyślnie komunikaty są kierowane do wbudowany punkt końcowy przeznaczonych dla usługi (**komunikaty/zdarzenia**) jest zgodna z [usługi Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/
). Ten punkt końcowy jest aktualnie korzystać tylko z uwidocznionymi [AMQP](https://www.amqp.org/) protokół, port 5671. Usługa IoT hub udostępnia poniższe właściwości do umożliwiają kontrolowanie wbudowanych zgodnego z Centrum zdarzeń obsługi komunikatów punktu końcowego **komunikaty/zdarzenia**.

| Właściwość            | Opis |
| ------------------- | ----------- |
| **Liczba partycji** | Ustaw tę właściwość w momencie jego tworzenia, aby zdefiniować liczbę [partycje](../event-hubs/event-hubs-features.md#partitions) dla przyjmowanie zdarzeń urządzenia do chmury. |
| **Czas przechowywania**  | Ta właściwość określa, jak długo w dniach, komunikaty są zachowywane przez usługę IoT Hub. Wartość domyślna to jeden dzień, ale może wzrosnąć do siedmiu dni. |

Usługa IoT Hub umożliwia także zarządzanie grupy konsumentów na wbudowane urządzenia do chmury otrzymywać punktu końcowego.

Jeśli używasz [routing komunikatów](iot-hub-devguide-messages-d2c.md) i [rezerwowego trasy](iot-hub-devguide-messages-d2c.md#fallback-route) jest włączone, wszystkie komunikaty, które nie są zgodne z zapytaniem na wszystkie trasy są zapisywane wbudowany punkt końcowy. Jeśli wyłączysz tę trasę rezerwowy, wiadomości, które nie są zgodne z dowolnego zapytania są porzucane.

Możesz zmodyfikować czas przechowywania albo programowo przy użyciu [dostawcy zasobów usługi IoT Hub interfejsów API REST](/rest/api/iothub/iothubresource), lub za pomocą [witryny Azure portal](https://portal.azure.com).

Usługa IoT Hub udostępnia **komunikaty/zdarzenia** wbudowany punkt końcowy dla usług zaplecza do odczytywania komunikatów przesyłanych z chmury do urządzeń odebrane przez Centrum. Ten punkt końcowy jest zdarzenie zgodnego z Centrum, co pozwala na użyć innych mechanizmów usługi Event Hubs obsługuje do odczytywania komunikatów.

## <a name="read-from-the-built-in-endpoint"></a>Odczyt z wbudowanego punktu końcowego

Kiedy używasz [usługi Azure Service Bus SDK dla platformy .NET](https://www.nuget.org/packages/WindowsAzure.ServiceBus) lub [Event Hubs — hosta procesora zdarzeń](..//event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md), wszelkie parametry połączenia Centrum IoT Hub można użyć z odpowiednimi uprawnieniami. Następnie użyj **komunikaty/zdarzenia** jako nazwy Centrum zdarzeń.

Kiedy używasz zestawów SDK (lub integracji produktu) rozpoznaje usługi IoT Hub, musisz pobrać punktu końcowego zgodnego z Centrum zdarzeń i nazwę zgodną z Centrum zdarzeń:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do Centrum IoT hub.

2. Kliknij przycisk **wbudowanych punktach końcowych**.

3. **Zdarzenia** sekcja zawiera następujące wartości: **punktu końcowego zgodnego z Centrum zdarzeń**, **nazwę zgodną z Centrum zdarzeń**, **partycje**, **Czas przechowywania**, i **grupy konsumentów**.

    ![Ustawienia komunikacji między urządzeniem i chmurą](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

Wymaga zestawu SDK usługi IoT Hub, nazwę punktu końcowego usługi IoT Hub, która jest **komunikaty/zdarzenia** według **punktów końcowych**.

Jeśli zestaw SDK, którego używasz wymaga **Hostname** lub **Namespace** wartość, Usuń schemat z **punktu końcowego zgodnego z Centrum zdarzeń**. Na przykład, jeśli punktu końcowego zgodnego z Centrum zdarzeń jest **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, **Hostname** będzie  **narzędzia iothub-ns-myiothub-1234.servicebus.windows.net**. **Namespace** będzie **iothub-ns-myiothub-1234**.

Następnie można użyć dowolnego zasady dostępu współdzielonego, który ma **ServiceConnect** uprawnień, aby nawiązać połączenie z określonym Centrum zdarzeń.

Jeśli potrzebujesz utworzyć parametry połączenia Centrum zdarzeń przy użyciu poprzednich informacji, użyj następującego wzorca:

`Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}`

Zestawy SDK i integracji, korzystające z punktów końcowych zgodnych z Centrum zdarzeń, które usługa IoT Hub udostępnia zawiera elementy na poniższej liście:

* [Klient usługi Event Hubs w języku Java](https://github.com/Azure/azure-event-hubs-java).
* [Apache Storm spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Możesz wyświetlić [elementu spout źródła](https://github.com/apache/storm/tree/master/external/storm-eventhubs) w witrynie GitHub.
* [Integracja platformy Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat punktów końcowych usługi IoT Hub, zobacz [punktów końcowych usługi IoT Hub](iot-hub-devguide-endpoints.md).

* [Przewodników Szybki Start](quickstart-send-telemetry-node.md) pokazują, jak wysyłać komunikaty urządzenie chmura z symulowanych urządzeń i odczytu komunikatów z wbudowanego punktu końcowego. 

Aby uzyskać więcej informacji, zobacz [komunikaty urządzenia do chmury usługi IoT Hub procesu przy użyciu tras](tutorial-routing.md) samouczka.

* Jeśli chcesz przekierowywać komunikatów przesyłanych z chmury do urządzenia do niestandardowych punktów końcowych, zobacz [trasy wiadomość i niestandardowe punkty końcowe na użytek komunikatów z urządzenia do chmury](iot-hub-devguide-messages-read-custom.md).