---
title: Funkcje żądania danych klienta
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
ms.openlocfilehash: a9115ee9c49ea069999393a6430b010addf672de
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klienta

Centrum IoT Azure jest oparte na interfejsu API REST usługi w chmurze celem przedsiębiorstwa umożliwiający bezpieczny, dwukierunkową komunikację między milionów urządzeń i podzielonym na partycje usługi Azure.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Poszczególne urządzenia są przypisywane identyfikator urządzenia (identyfikator urządzenia) przez administratora dzierżawy. Dane urządzenia są oparte na identyfikator przypisanego urządzenia. Firma Microsoft zachowuje żadnych informacji i nie ma dostępu do danych, która umożliwiałaby identyfikator urządzenia do użytkownika korelacji.

Wiele urządzeń zarządzanych w usłudze Azure IoT Hub nie są urządzeń osobistych, na przykład office robota Termostat lub do ustawień fabrycznych. Klienci mogą jednak wziąć pod uwagę niektórych urządzeń osobistych i według własnego uznania może utrzymać własnych zasobów lub metody, które powiązanie urządzeń do osób śledzenia spisu. Centrum IoT Azure zarządza i przechowuje wszystkie dane skojarzone z urządzenia, tak jakby był on dane osobowe.

Administratorzy dzierżawy może używać portalu Azure lub interfejsów API REST usługi do spełnienia żądania informacji przez eksportowanie lub usuwanie danych skojarzonych z identyfikatorem urządzenia.

Jeśli używasz funkcji routingu usługi Centrum IoT Azure do przesyłania dalej komunikatów urządzenia do innych usług, następnie żądania danych musi zostać wykonana przez administratora dzierżawy dla każdego punktu końcowego, routingu w celu wykonania pełnej żądania dla danego urządzenia. Zobacz dokumentację referencyjną każdego punktu końcowego, aby uzyskać więcej szczegółowych informacji. Aby uzyskać więcej informacji o obsługiwanych punktów końcowych, zobacz [odwołanie — punkty końcowe Centrum IoT](iot-hub-devguide-endpoints.md).

Jeśli używasz funkcji integracji Azure siatki zdarzeń usługi Azure IoT Hub, następnie żądania danych musi zostać wykonana przez administratora dzierżawy dla subskrybenta każdego z tych zdarzeń. Aby uzyskać więcej informacji, zobacz [reagowania na zdarzenia Centrum IoT przy użyciu siatki zdarzeń](iot-hub-event-grid.md).

Jeśli używasz funkcji integracji Azure monitora usługi Centrum IoT Azure można utworzyć dzienników diagnostycznych żądania danych musi zostać wykonana przez administratora dzierżawy z dzienników przechowywanych. Aby uzyskać więcej informacji, zobacz [monitorowanie kondycji Azure IoT Hub](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Administratorzy dzierżawy służy blok urządzenia IoT rozszerzenia Centrum IoT Azure w portalu Azure do usunięcia urządzenia, które powoduje usunięcie danych powiązanych z tym urządzeniem.

Istnieje również możliwość wykonywania operacji usuwania dla urządzeń przy użyciu interfejsów API REST. Aby uzyskać więcej informacji, zobacz [interfejsu Api urządzenia — Usuń urządzenia](https://docs.microsoft.com/rest/api/iothub/deviceapi/deletedevice).

## <a name="exporting-customer-data"></a>Eksportowanie danych klienta

Administratorzy dzierżawy może wykorzystywać kopiowania i wklejania w bloku urządzenia IoT rozszerzenia Centrum IoT Azure w portalu Azure, aby wyeksportować dane skojarzone z urządzeniem.

Istnieje również możliwość wykonywania operacji eksportowania urządzeń przy użyciu interfejsów API REST. Aby uzyskać więcej informacji, zobacz [interfejsu Api urządzenia — Uzyskaj urządzenie](https://docs.microsoft.com/rest/api/iothub/deviceapi/getdevice).

> [!NOTE]
> Korzystając z usług dla przedsiębiorstw firmy Microsoft, Microsoft generuje niektórych informacji, znane jako dzienniki generowane przez system. Niektóre usługi inicjowania obsługi urządzeń Centrum IoT Azure dzienniki generowanych przez system nie są dostępne lub można eksportować przez administratorów dzierżawy. Te dzienniki stanowią faktyczne akcje dokonywanych w ramach usługi i danych diagnostycznych dotyczących poszczególnych urządzeń.

## <a name="links-to-additional-documentation"></a>Linki do dodatkowej dokumentacji

Pełną dokumentację dla interfejsów API usługi Azure IoT Hub urządzenie znajduje się pod adresem [ https://docs.microsoft.com/rest/api/iothub/deviceapi ](https://docs.microsoft.com/rest/api/iothub/deviceapi).