---
title: Zrozumienie wbudowany punkt końcowy usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — w tym artykule opisano sposób używania wbudowanych, komunikaty z urządzenia do chmury toread punktu końcowego zgodnego z Centrum zdarzeń.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 767c91e4926e553b63b8331ac99edcd7823d2c13
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44055019"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Odczytywanie komunikatów przesyłanych z urządzeń do chmury z wbudowanego punktu końcowego

Domyślnie komunikaty są kierowane do wbudowany punkt końcowy przeznaczonych dla usługi (**komunikaty/zdarzenia**) jest zgodna z [usługi Event Hubs][lnk-event-hubs]. Ten punkt końcowy jest aktualnie korzystać tylko z uwidocznionymi [AMQP] [ lnk-amqp] protokół, port 5671. Usługa IoT hub udostępnia poniższe właściwości do umożliwiają kontrolowanie wbudowanych zgodnego z Centrum zdarzeń obsługi komunikatów punktu końcowego **komunikaty/zdarzenia**.

| Właściwość            | Opis |
| ------------------- | ----------- |
| **Liczba partycji** | Ustaw tę właściwość w momencie jego tworzenia, aby zdefiniować liczbę [partycje] [ lnk-event-hub-partitions] dla przyjmowanie zdarzeń urządzenia do chmury. |
| **Czas przechowywania**  | Ta właściwość określa, jak długo w dniach, komunikaty są zachowywane przez usługę IoT Hub. Wartość domyślna to jeden dzień, ale może wzrosnąć do siedmiu dni. |

Usługa IoT Hub umożliwia także zarządzanie grupy konsumentów na wbudowane urządzenia do chmury otrzymywać punktu końcowego.

Domyślnie wszystkie komunikaty, które nie są jawnie zgodne regułę routingu wiadomości są zapisywane w wbudowany punkt końcowy. Jeśli wyłączysz tę trasę rezerwowy, wiadomości, które nie są jawnie zgodne żadnych reguł rozsyłania komunikatów są porzucane.

Możesz zmodyfikować czas przechowywania albo programowo przy użyciu [dostawcy zasobów usługi IoT Hub interfejsów API REST][lnk-resource-provider-apis], lub za pomocą [witryny Azure portal] [ lnk-management-portal].

Usługa IoT Hub udostępnia **komunikaty/zdarzenia** wbudowany punkt końcowy dla usług zaplecza do odczytywania komunikatów przesyłanych z chmury do urządzeń odebrane przez Centrum. Ten punkt końcowy jest zdarzenie zgodnego z Centrum, co pozwala na użyć innych mechanizmów usługi Event Hubs obsługuje do odczytywania komunikatów.

## <a name="read-from-the-built-in-endpoint"></a>Odczyt z wbudowanego punktu końcowego

Kiedy używasz [usługi Azure Service Bus SDK dla platformy .NET] [ lnk-servicebus-sdk] lub [Event Hubs — hosta procesora zdarzeń][lnk-eventprocessorhost], może używać dowolnego połączenia usługi IoT Hub ciągi z odpowiednimi uprawnieniami. Następnie użyj **komunikaty/zdarzenia** jako nazwy Centrum zdarzeń.

Kiedy używasz zestawów SDK (lub integracji produktu) rozpoznaje usługi IoT Hub, musisz pobrać punktu końcowego zgodnego z Centrum zdarzeń i nazwę zgodną z Centrum zdarzeń:

1. Zaloguj się do [witryny Azure portal] [ lnk-management-portal] i przejdź do Centrum IoT hub.
1. Kliknij przycisk **Punkty końcowe**.
1. W **wbudowanych punktach końcowych** kliknij **zdarzenia**. 
1. Zostanie otwarta strona właściwości, która zawiera następujące wartości: **punktu końcowego zgodnego z Centrum zdarzeń**, **nazwę zgodną z Centrum zdarzeń**, **partycje**,  **Czas przechowywania**, i **grupy konsumentów**.

    ![Ustawienia komunikacji między urządzeniem i chmurą][img-eventhubcompatible]

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

Aby uzyskać więcej informacji na temat punktów końcowych usługi IoT Hub, zobacz [punktów końcowych usługi IoT Hub][lnk-endpoints].

[Przewodników Szybki Start] [ lnk-get-started] pokazują, jak wysyłać komunikaty urządzenie chmura z symulowanych urządzeń i odczytu komunikatów z wbudowanego punktu końcowego. Aby uzyskać więcej informacji, zobacz [komunikaty urządzenia do chmury usługi IoT Hub procesu przy użyciu tras] [ lnk-d2c-tutorial] samouczka.

Jeśli chcesz przekierowywać komunikatów przesyłanych z chmury do urządzenia do niestandardowych punktów końcowych, zobacz [trasy wiadomość i niestandardowe punkty końcowe na użytek komunikatów z urządzenia do chmury][lnk-custom].

[img-eventhubcompatible]: ./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png

[lnk-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-get-started]: quickstart-send-telemetry-node.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-management-portal]: https://portal.azure.com
[lnk-d2c-tutorial]: tutorial-routing.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-features.md#partitions
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph
[lnk-amqp]: https://www.amqp.org/
