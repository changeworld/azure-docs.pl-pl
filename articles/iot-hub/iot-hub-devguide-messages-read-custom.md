---
title: Opis niestandardowych punktów końcowych usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — używanie zapytań routingu do kierowania wiadomości z urządzenia do chmury do niestandardowych punktów końcowych.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: e5e92c40cef15e99431dc9652820c71e87935f67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61244348"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Używanie tras komunikatów i niestandardowych punktów końcowych dla wiadomości z urządzenia do chmury

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

[Routing komunikatów](iot-hub-devguide-routing-query-syntax.md) usługi IoT Hub umożliwia użytkownikom kierowanie komunikatów z urządzenia do chmury do punktów końcowych skierowanych do usługi. Routing zapewnia również możliwość przeszukiwania danych przed przekierowaniem go do punktów końcowych. Każda skonfigurowana kwerenda routingu ma następujące właściwości:

| Właściwość      | Opis |
| ------------- | ----------- |
| **Nazwa**      | Unikatowa nazwa identyfikująca kwerendę. |
| **Źródła**    | Pochodzenie strumienia danych, który ma zostać zrealizowany. Na przykład dane telemetryczne urządzenia. |
| **Warunek** | Wyrażenie kwerendy dla kwerendy routingu, która jest uruchamiana względem właściwości aplikacji wiadomości, właściwości systemu, treść wiadomości, tagi bliźniaczej reprezentacji urządzenia i właściwości bliźniaczej reprezentacji urządzenia, aby określić, czy jest to zgodne dla punktu końcowego. Aby uzyskać więcej informacji na temat konstruowania kwerendy, zobacz [składnię kwerendy routingu wiadomości](iot-hub-devguide-routing-query-syntax.md) |
| **Punktu końcowego**  | Nazwa punktu końcowego, w którym Centrum IoT wysyła komunikaty pasujące do kwerendy. Zaleca się wybranie punktu końcowego w tym samym regionie co centrum IoT hub. |

Pojedyncza wiadomość może odpowiadać warunek w wielu kwerend routingu, w którym to przypadku Usługa IoT Hub dostarcza wiadomość do punktu końcowego skojarzonego z każdej dopasowanej kwerendy. Usługa IoT Hub również automatycznie deduplikuje dostarczanie wiadomości, więc jeśli wiadomość pasuje do wielu zapytań, które mają ten sam cel, jest zapisywana tylko raz do tego miejsca docelowego.

## <a name="endpoints-and-routing"></a>Punkty końcowe i routing

Centrum IoT ma domyślny [wbudowany punkt końcowy](iot-hub-devguide-messages-read-builtin.md). Można utworzyć niestandardowe punkty końcowe do kierowania wiadomości do łącząc inne usługi w subskrypcji do centrum. Usługa IoT Hub obsługuje obecnie kontenery usługi Azure Storage, centra zdarzeń, kolejki usługi Service Bus i tematy usługi Service Bus jako niestandardowe punkty końcowe.

Korzystając z routingu i niestandardowych punktów końcowych, wiadomości są dostarczane do wbudowanego punktu końcowego tylko wtedy, gdy nie pasują do żadnej kwerendy. Aby dostarczać wiadomości do wbudowanego punktu końcowego, a także do niestandardowego punktu końcowego, należy dodać trasę, która wysyła wiadomości do wbudowanego punktu końcowego **zdarzeń.**

> [!NOTE]
> * Usługa IoT Hub obsługuje tylko zapisywanie danych w kontenerach usługi Azure Storage jako obiekty blob.
> * Kolejki i tematy usługi Service Bus z **włączoną obsługą sesji** lub **wykrywania duplikatów** nie są obsługiwane jako niestandardowe punkty końcowe.

Aby uzyskać więcej informacji na temat tworzenia niestandardowych punktów końcowych w Centrum IoT, zobacz [Punkty końcowe usługi IoT Hub](iot-hub-devguide-endpoints.md).

Aby uzyskać więcej informacji na temat odczytu z niestandardowych punktów końcowych, zobacz:

* Czytanie z [kontenerów usługi Azure Storage](../storage/blobs/storage-blobs-introduction.md).

* Czytanie z [centrów zdarzeń](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* Odczyt z [kolejek usługi Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Czytanie z [tematów usługi Service Bus](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat punktów końcowych usługi IoT Hub, zobacz [Punkty końcowe usługi IoT Hub](iot-hub-devguide-endpoints.md).

* Aby uzyskać więcej informacji na temat języka kwerend używanych do definiowania kwerend routingu, zobacz [Składnia kwerendy routingu wiadomości](iot-hub-devguide-routing-query-syntax.md).

* Process [IoT Hub device-to-cloud tutorial przy użyciu tras](tutorial-routing.md) samouczek pokazuje, jak używać zapytań routingu i niestandardowych punktów końcowych.