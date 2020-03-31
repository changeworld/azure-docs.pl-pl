---
title: Rozwiązywanie problemów z błędem usługi Azure IoT Hub 403002 IoTHubQuotaExceeded
description: Dowiedz się, jak naprawić błąd 403002 IoTQuotaExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961116"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

W tym artykule opisano przyczyny i rozwiązania dla **błędów 403002 IoTHubQuotaExceededed.**

## <a name="symptoms"></a>Objawy

Wszystkie żądania do Centrum IoT nie powiodą się z błędem **403002 IoTQuotaExceeded**. W witrynie Azure portal lista urządzeń centrum IoT nie ładuje się.

## <a name="cause"></a>Przyczyna

Dzienny przydział wiadomości dla centrum IoT hub jest przekroczony. 

## <a name="solution"></a>Rozwiązanie

[Uaktualnij lub zwiększ liczbę jednostek w centrum IoT hub](iot-hub-upgrade.md) lub poczekaj na następny dzień UTC, aby odświeżyć dzienny przydział.

## <a name="next-steps"></a>Następne kroki

* Aby zrozumieć, jak operacje są wliczane do przydziału, takie jak zapytania bliźniacze i metody bezpośrednie, zobacz [Opis cen usługi IoT Hub](iot-hub-devguide-pricing.md#charges-per-operation)
* Aby skonfigurować monitorowanie dziennego użycia przydziału, skonfiguruj alert z metryką *Całkowita liczba używanych komunikatów*. Aby uzyskać instrukcje krok po kroku, zobacz [Konfigurowanie metryk i alertów za pomocą Usługi IoT Hub](tutorial-use-metrics-and-diags.md#set-up-metrics)