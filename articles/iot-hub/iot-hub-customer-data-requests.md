---
title: Żądania danych klientów dla urządzeń usługi Azure IoT Hub
description: Większość urządzeń zarządzanych w usłudze Azure IoT Hub nie są osobiste, ale niektóre są. W tym artykule o tym, że administratorzy mogą eksportować lub usuwać dane osobowe z urządzenia.
author: robinsh
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: a05fbf6e1908f88014cd8da99fafb875de033f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499318"
---
# <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klientów

Usługa Azure IoT Hub to usługa w chmurze oparta na interfejsie REST, przeznaczona dla klientów korporacyjnych, która umożliwia bezpieczną, dwukierunkową komunikację między milionami urządzeń a usługą platformy Azure na partycje.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Poszczególnym urządzeniom administratorowi dzierżawy jest przypisywany identyfikator urządzenia (identyfikator urządzenia). Dane urządzenia są oparte na przypisanym identyfikatorze urządzenia. Firma Microsoft nie przechowuje żadnych informacji i nie ma dostępu do danych, które pozwoliłyby identyfikatorowi urządzenia na korelację użytkownika.

Wiele urządzeń zarządzanych w usłudze Azure IoT Hub nie jest urządzeniami osobistymi, na przykład termostatem biurowym lub robotem fabrycznym. Klienci mogą jednak uznać niektóre urządzenia za możliwe do zidentyfikowania i według własnego uznania mogą utrzymywać własne metody śledzenia zasobów lub zapasów, które wiążą urządzenia z osobami fizycznymi. Usługa Azure IoT Hub zarządza i przechowuje wszystkie dane skojarzone z urządzeniami tak, jakby były danymi osobistymi.

Administratorzy dzierżawy mogą używać witryny Azure portal lub interfejsów API REST usługi do obsługi żądań informacji przez eksportowanie lub usuwanie danych skojarzonych z identyfikatorem urządzenia.

Jeśli używasz funkcji routingu usługi Azure IoT Hub do przesyłania dalej komunikatów urządzenia do innych usług, żądania danych muszą być wykonywane przez administratora dzierżawy dla każdego punktu końcowego routingu w celu wykonania pełnego żądania dla danego urządzenia. Aby uzyskać więcej informacji, zobacz dokumentację referencyjną dla każdego punktu końcowego. Aby uzyskać więcej informacji na temat obsługiwanych punktów końcowych, zobacz [Odwołanie — Punkty końcowe usługi IoT Hub](iot-hub-devguide-endpoints.md).

Jeśli używasz funkcji integracji usługi Azure Event Grid usługi Azure IoT Hub, żądania danych muszą być wykonywane przez administratora dzierżawy dla każdego subskrybenta tych zdarzeń. Aby uzyskać więcej informacji, zobacz [Reagowanie na zdarzenia usługi IoT Hub przy użyciu siatki zdarzeń](iot-hub-event-grid.md).

Jeśli używasz funkcji integracji usługi Azure IoT Hub do tworzenia dzienników diagnostycznych, żądania danych muszą być wykonywane przez administratora dzierżawy względem przechowywanych dzienników. Aby uzyskać więcej informacji, zobacz [Monitorowanie kondycji usługi Azure IoT Hub](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Administratorzy dzierżawy mogą użyć bloku urządzeń IoT rozszerzenia usługi Azure IoT Hub w witrynie Azure portal, aby usunąć urządzenie, które usuwa dane skojarzone z tym urządzeniem.

Możliwe jest również wykonywanie operacji usuwania dla urządzeń korzystających z interfejsów API REST. Aby uzyskać więcej informacji, zobacz [Usługa — usuwanie urządzenia](/rest/api/iothub/service/registrymanager/deletedevice).

## <a name="exporting-customer-data"></a>Eksportowanie danych klientów

Administratorzy dzierżawy mogą korzystać z kopiowania i wklejać w okienku urządzeń IoT rozszerzenia usługi Azure IoT Hub w witrynie Azure Portal do eksportowania danych skojarzonych z urządzeniem.

Możliwe jest również wykonywanie operacji eksportowania dla urządzeń korzystających z interfejsów API REST. Aby uzyskać więcej informacji, zobacz [Usługa — Pobierz urządzenie](/rest/api/iothub/service/registrymanager/getdevice).

> [!NOTE]
> Podczas korzystania z usług firmy Microsoft dla przedsiębiorstw firma Microsoft generuje pewne informacje, znane jako dzienniki generowane przez system. Niektóre dzienniki generowane przez system usługi Azure IoT Hub nie są dostępne ani eksportowane przez administratorów dzierżawy. Dzienniki te stanowią rzeczowe działania prowadzone w ramach usługi i dane diagnostyczne związane z poszczególnymi urządzeniami.

## <a name="links-to-additional-documentation"></a>Linki do dodatkowej dokumentacji

Pełna dokumentacja interfejsów API usługi Azure IoT Hub znajduje się w [interfejsach API usługi Usługi IoT Hub.](https://docs.microsoft.com/rest/api/iothub/service/configuration)
