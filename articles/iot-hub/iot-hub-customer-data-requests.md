---
title: Funkcje żądania danych klienta
description: Podsumowanie funkcji żądania danych klienta
author: robinsh
manager: philmea
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 1519637eddf909040131a1efac5738fc7cc8e565
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791682"
---
# <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klienta

Usługi Azure IoT Hub jest oparte na interfejsie API REST usługi w chmurze przeznaczona dla klientów korporacyjnych bezpiecznej, dwukierunkowej komunikacji między milionami urządzeń i podzielonym na partycje usługi platformy Azure.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Poszczególne urządzenia są przypisane urządzenia identyfikator urządzenia przez administratora dzierżawy. Dane urządzenia opiera się na identyfikator przypisanych do urządzenia. Firma Microsoft zachowuje żadnych informacji i nie ma dostępu do danych, które pozwoliłoby identyfikator urządzenia do użytkownika korelacji.

Wiele urządzeń zarządzanych w usłudze Azure IoT Hub nie jest urządzeń osobistych, na przykład robota Termostat lub fabryki pakietu office. Klienci mogą jednak wziąć pod uwagę niektóre urządzenia jako osobiste i według własnego uznania mogą utrzymać ich własnych zasobów magazynu śledzenia metody, które powiązanie urządzeń fizycznych. Usługa Azure IoT Hub zarządza i przechowuje wszystkie dane skojarzone z urządzeń, jak w przypadku danych osobowych.

Administratorzy dzierżawy, można użyć witryny Azure portal lub interfejsów API REST usługi do spełnienia żądania informacji przez eksportowanie lub usuwanie danych skojarzonych z identyfikatorem urządzenia.

Jeśli używasz routingu funkcji usługi Azure IoT Hub do przesyłania komunikatów z urządzeń do innych usług, następnie żądania danych muszą być wykonywane przez administratora dzierżawy dla każdego punktu końcowego w routingu w celu wykonania pełnego żądania dla danego urządzenia. Aby uzyskać więcej informacji zobacz dokumentację odniesienia dla każdego punktu końcowego. Aby uzyskać więcej informacji na temat obsługiwanych punktów końcowych, zobacz [odwołanie - punktów końcowych usługi IoT Hub](iot-hub-devguide-endpoints.md).

Jeśli używasz funkcji integracji usługi Azure Event Grid usługi Azure IoT Hub, następnie żądania danych muszą być wykonywane przez administratora dzierżawy dla każdego subskrybenta tych zdarzeń. Aby uzyskać więcej informacji, zobacz [reagować na zdarzenia IoT Hub przy użyciu usługi Event Grid](iot-hub-event-grid.md).

Jeśli używasz funkcji integracji usługi Azure Monitor usługi Azure IoT Hub można tworzyć dzienniki diagnostyczne żądania danych muszą być wykonywane przez administratora dzierżawy przed przechowywane dzienniki. Aby uzyskać więcej informacji, zobacz [monitorowania kondycji usługi Azure IoT Hub](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Administratorzy dzierżawy, można użyć bloku urządzeń IoT rozszerzenia usługi Azure IoT Hub w witrynie Azure portal można usunąć urządzenia, co spowoduje usunięcie danych skojarzonych z tym urządzeniem.

Istnieje również możliwość wykonywania operacji usuwania w przypadku urządzeń korzystających z interfejsów API REST. Aby uzyskać więcej informacji, zobacz [Service — Usuń urządzenia](/rest/api/iothub/service/deletedevice).

## <a name="exporting-customer-data"></a>Eksportowanie danych klienta

Administratorzy dzierżawy mogą korzystanie z kopiowania i Wklej w bloku urządzeń IoT rozszerzenia usługi Azure IoT Hub w witrynie Azure portal, aby wyeksportować dane skojarzone z urządzenia.

Istnieje również możliwość wykonywania operacji eksportowania w przypadku urządzeń korzystających z interfejsów API REST. Aby uzyskać więcej informacji, zobacz [Service — Pobierz urządzenie](/rest/api/iothub/service/getdevice).

> [!NOTE]
> Korzystając z usług firmy Microsoft dla przedsiębiorstw firmy Microsoft generuje pewnych informacji, znane jako dzienniki generowane przez system. Niektóre usługi Azure IoT Hub w dziennikach generowanych przez system nie są dostępne lub który można eksportować przez administratorów dzierżawy. Te dzienniki stanowią odzwierciedlenie faktycznych działań przeprowadzanych w ramach usługi i dane diagnostyczne, które dotyczą poszczególnych urządzeń.

## <a name="links-to-additional-documentation"></a>Linki do dodatkowej dokumentacji

Pełną dokumentację dotyczącą interfejsów API usługi Microsoft Azure IoT Hub znajduje się w [interfejsów API usługi Microsoft IoT Hub](https://docs.microsoft.com/rest/api/iothub/service).
