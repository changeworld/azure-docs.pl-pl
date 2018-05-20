---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: f6986df19482800ff676c0e05ad680e14dfeca1f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
## <a name="view-device-telemetry"></a>Wyświetlanie danych telemetrycznych z urządzenia

Można wyświetlić dane telemetryczne, wysyłane z urządzenia **urządzeń** strony w rozwiązaniu.

1. Wybierz urządzenie, którego inicjowana na liście urządzeń na **urządzeń** strony. Panel wyświetla informacje o urządzeniu, włącznie z wykresu telemetrii urządzenia:

    ![Szczegóły urządzenia](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Wybierz **wykorzystania** Aby zmienić sposób wyświetlania danych telemetrii:

    ![Widok wykorzystania telemetrii](media/iot-suite-visualize-connecting/devicespressure.png)

1. Aby wyświetlić informacje diagnostyczne o urządzeniu, przewiń w dół do **diagnostyki**:

    ![Wyświetlanie urządzeń diagnostyki](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Działania na urządzeniu

Aby wywołać metody na urządzeniach, należy użyć **urządzeń** strony w zdalnym rozwiązanie monitorowania. Na przykład w przypadku zdalnego rozwiązanie monitorowania **Chłodnica** zaimplementować urządzeń **FirmwareUpdate** metody.

1. Wybierz **urządzeń** można przejść do **urządzeń** strony w rozwiązaniu.

1. Wybierz urządzenie, którego inicjowana na liście urządzeń na **urządzeń** strony:

    ![Wybierz urządzenia fizycznego](media/iot-suite-visualize-connecting/devicesselect.png)

1. Aby wyświetlić listę metod można wywołać na urządzeniu, wybierz **zadania**, następnie **uruchamiania metody**. Aby zaplanować zadania do uruchomienia na wielu urządzeniach, można wybrać wiele urządzeń na liście. **Zadania** panelu przedstawiono typy metody wspólne dla wszystkich wybranych urządzeń.

1. Wybierz **FirmwareUpdate**, ustaw nazwę zadania **UpdatePhysicalChiller**. Ustaw **wersja oprogramowania układowego** do **2.0.0**ustaw **URI oprogramowania układowego** do **http://contoso.com/updates/firmware.bin**, a następnie wybierz pozycję **Zastosuj**:

    ![Planowanie aktualizacji oprogramowania układowego](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Wyświetla sekwencję wiadomości w konsoli wykonywania kodu urządzenia, podczas gdy symulowane urządzenie obsługuje metody.

1. Po ukończeniu aktualizacji nowej wersji oprogramowania układowego wyświetla na **urządzeń** strony:

    ![Zakończono aktualizację](media/iot-suite-visualize-connecting/complete.png)

> [!NOTE]
> Aby śledzić stan zadania w rozwiązaniu, wybierz **widoku**.

## <a name="next-steps"></a>Kolejne kroki

Artykuł [dostosować zdalnego monitorowania akcelerator rozwiązań](../articles/iot-suite/iot-suite-remote-monitoring-customize.md) opisano niektóre sposoby dostosowania akcelerator rozwiązań.