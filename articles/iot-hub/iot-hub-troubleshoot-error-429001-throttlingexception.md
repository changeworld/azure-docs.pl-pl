---
title: Rozwiązywanie problemów z błędem usługi Azure IoT Hub 429001 ThrottlingException
description: Dowiedz się, jak naprawić błąd 429001 ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960700"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

W tym artykule opisano przyczyny i rozwiązania **dla błędów throttlingexception 429001.**

## <a name="symptoms"></a>Objawy

Żądania do usługi IoT Hub nie powiodą się z powodu błędu **429001 ThrottlingException**.

## <a name="cause"></a>Przyczyna

[Limity ograniczania przepustowości](./iot-hub-devguide-quotas-throttling.md) usługi IoT Hub zostały przekroczone dla żądanej operacji.

## <a name="solution"></a>Rozwiązanie

Sprawdź, czy widzisz limit ograniczania przepustowości, porównując *metrykę prób wysyłania wiadomości telemetrycznych* z limitami określonymi powyżej. Można również sprawdzić *liczbę błędów ograniczania* przepustowości metryki. Aby uzyskać więcej informacji na temat tych i innych metryk dostępnych dla usługi IoT Hub, zobacz [metryki usługi IoT Hub i sposób ich używania.](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them)

Centrum IoT zwraca 429 ThrottlingException tylko po zbyt długim okresie naruszenia limitu. Odbywa się to tak, aby wiadomości nie są odrzucane, jeśli centrum IoT pobiera ruch seryjny. W międzyczasie usługa IoT Hub przetwarza komunikaty z ograniczoną przepływnością, co może być powolne, jeśli lista prac zawiera zbyt wiele danych. Aby dowiedzieć się więcej, zobacz [Kształtowanie ruchu w usłudze IoT Hub](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Następne kroki

Należy rozważyć [skalowanie w górę Usługi IoT Hub,](./iot-hub-scaling.md) jeśli pracujesz w limity przydziału lub ograniczania przepustowości.