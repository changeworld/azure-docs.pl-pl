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
ms.openlocfilehash: 8eada6b88c320396b30ecdeb96c95e4ec61679ce
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
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

1. Aby wyświetlić listę metod można wywołać na urządzeniu, wybierz **harmonogram**. Aby zaplanować metody do uruchamiania na wielu urządzeniach, można wybrać wiele urządzeń na liście. **Harmonogram** panelu przedstawiono typy metody wspólne dla wszystkich wybranych urządzeń.

1. Wybierz **FirmwareUpdate**, ustaw nazwę zadania **UpdatePhysicalChiller**. Ustaw **wersja oprogramowania układowego** do **2.0.0**ustaw **URI oprogramowania układowego** do **http://contoso.com/updates/firmware.bin**, a następnie wybierz pozycję **Zastosuj**:

    ![Planowanie aktualizacji oprogramowania układowego](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Wyświetla sekwencję wiadomości w konsoli wykonywania kodu urządzenia, podczas gdy symulowane urządzenie obsługuje metody.

1. Po ukończeniu aktualizacji nowej wersji oprogramowania układowego wyświetla na **urządzeń** strony:

    ![Zakończono aktualizację](media/iot-suite-visualize-connecting/complete.png)

> [!NOTE]
> Aby śledzić stan zadania w rozwiązaniu, wybierz **widoku**.

## <a name="next-steps"></a>Kolejne kroki

Artykuł [dostosować zdalnego monitorowania akcelerator rozwiązań](../articles/iot-suite/iot-suite-remote-monitoring-customize.md) opisano niektóre sposoby dostosowania akcelerator rozwiązań.