---
title: Usługa Azure IoT Hub przeprowadza migrację do ustawień diagnostycznych | Dokumenty firmy Microsoft
description: Jak zaktualizować usługę Azure IoT Hub, aby używać ustawień diagnostyki platformy Azure zamiast monitorowania operacji w celu monitorowania stanu operacji w centrum IoT hub w czasie rzeczywistym.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: ab07da38c01b052a4220274fb059683a22950a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750700"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrowanie centrum IoT Hub z monitorowania operacji do ustawień diagnostycznych

Klienci korzystający z [monitorowania operacji](iot-hub-operations-monitoring.md) do śledzenia stanu operacji w centrum IoT Hub mogą migrować ten przepływ pracy do [ustawień diagnostyki platformy Azure](../azure-monitor/platform/platform-logs-overview.md), funkcji usługi Azure Monitor. Ustawienia diagnostyki dostarczają informacje diagnostyczne na poziomie zasobów dla wielu usług platformy Azure.

**Funkcje monitorowania operacji usługi IoT Hub są przestarzałe**i zostały usunięte z portalu. Ten artykuł zawiera kroki, aby przenieść obciążenia z monitorowania operacji do ustawień diagnostyki. Aby uzyskać więcej informacji na temat osi czasu wycofania, zobacz [Monitorowanie rozwiązań IoT platformy Azure za pomocą usługi Azure Monitor i usługi Azure Resource Health.](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/)

## <a name="update-iot-hub"></a>Aktualizacja Usługi IoT Hub

Aby zaktualizować centrum IoT w witrynie Azure portal, najpierw włącz ustawienia diagnostyki, a następnie wyłącz monitorowanie operacji.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Wyłącz monitorowanie operacji

> [!NOTE]
> Od 11 marca 2019 r. funkcja monitorowania operacji jest usuwana z interfejsu portalu Azure usługi IoT Hub. Poniższe kroki nie mają już zastosowania. Aby przeprowadzić migrację, upewnij się, że poprawne kategorie są włączone w ustawieniach diagnostycznych usługi Azure Monitor powyżej.

Po przetestowaniu nowych ustawień diagnostycznych w przepływie pracy można wyłączyć funkcję monitorowania operacji. 

1. W menu Centrum IoT wybierz pozycję **Monitorowanie operacji**.

2. W każdej kategorii monitorowania wybierz **opcję Brak**.

3. Zapisz zmiany monitorowania operacji.

## <a name="update-applications-that-use-operations-monitoring"></a>Aktualizowanie aplikacji korzystających z monitorowania operacji

Schematy monitorowania operacji i ustawień diagnostycznych różnią się nieznacznie. Ważne jest, aby zaktualizować aplikacje, które używają operacji monitorowania dzisiaj do mapowania schematu używanego przez ustawienia diagnostyczne. 

Ponadto ustawienia diagnostyczne oferują pięć nowych kategorii śledzenia. Po zaktualizowaniu aplikacji dla istniejącego schematu dodaj również nowe kategorie:

* Operacje bliźniaczeczki chmury na urządzenia
* Operacje bliźniaczych między urządzeniami do chmury
* Zapytania bliźniacze
* Operacje zadań
* Metody bezpośrednie

Zobacz [też: Opis schematu ustawień diagnostyki.](iot-hub-monitor-resource-health.md#understand-the-logs)

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Monitorowanie zdarzeń urządzenia i rozłączania z małym opóźnieniem

Aby monitorować zdarzenia łączenia i rozłączania urządzenia w produkcji, zalecamy subskrybowanie zdarzenia [ **odłączone urządzenie** ](iot-hub-event-grid.md#event-types) w usłudze Event Grid, aby otrzymywać alerty i monitorować stan połączenia urządzenia. Ten [samouczek](iot-hub-how-to-order-connection-state-events.md) służy do integrowania zdarzeń połączonych z urządzeniem i urządzeń z centrum IoT Hub w rozwiązaniu IoT.

## <a name="next-steps"></a>Następne kroki

[Monitorowanie kondycji usługi Azure IoT Hub i szybkie diagnozowanie problemów](iot-hub-monitor-resource-health.md)
