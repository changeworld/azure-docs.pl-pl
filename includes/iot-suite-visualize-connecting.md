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
ms.openlocfilehash: 73ba80878615f04e1755a4d12014691c5ae2a077
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453124"
---
## <a name="view-device-telemetry"></a>Wyświetlanie danych telemetrycznych z urządzenia

Możesz wyświetlić dane telemetryczne wysyłane z urządzenia **urządzeń** strony w rozwiązaniu.

1. Wybierz urządzenie, którego obsługę zainicjowano na liście urządzeń na **urządzeń** strony. Panel wyświetla informacje o urządzeniu, w tym wykres danych telemetrycznych z urządzenia:

    ![Zobacz szczegóły urządzenia](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Wybierz **wykorzystanie** można zmienić wyświetlanie telemetrii:

    ![Wyświetlanie danych telemetrycznych z wykorzystania](media/iot-suite-visualize-connecting/devicespressure.png)

1. Aby wyświetlić informacje diagnostyczne o urządzeniu, przewiń w dół do **diagnostyki**:

    ![Wyświetl diagnostykę urządzenia](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Działa na twoim urządzeniu

Aby wywołać metod na urządzaniach, należy użyć **urządzeń** strony w rozwiązaniu do zdalnego monitorowania. Na przykład w przypadku rozwiązania do zdalnego monitorowania **Chłodnica** Implementowanie urządzeń **FirmwareUpdate** metody.

1. Wybierz **urządzeń** można przejść do **urządzeń** strony w rozwiązaniu.

1. Wybierz urządzenie, którego obsługę zainicjowano na liście urządzeń na **urządzeń** strony:

    ![Wybierz swoje rzeczywiste urządzenie](media/iot-suite-visualize-connecting/devicesselect.png)

1. Aby wyświetlić listę metod, można wywołać na urządzeniu przenośnym, wybierz **zadania**, następnie **uruchamiania metody**. Aby zaplanować zadanie do uruchamiania na wielu urządzeniach, można wybrać wiele urządzeń, na liście. **Zadań** panelu zawiera typy metody wspólne dla wszystkich urządzeń, które są wybrane.

1. Wybierz **FirmwareUpdate**, ustaw nazwę zadania na **UpdatePhysicalChiller**. Ustaw **wersja oprogramowania układowego** do **2.0.0**ustaw **URI oprogramowania układowego** do **http://contoso.com/updates/firmware.bin**, a następnie wybierz **Zastosuj**:

    ![Planowanie aktualizacji oprogramowania układowego](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Wyświetla sekwencję wiadomości w konsoli uruchamiania kodu urządzenia, gdy symulowane urządzenie obsłuży metodę.

1. Po zakończeniu aktualizacji nowej wersji oprogramowania układowego wyświetla na **urządzeń** strony:

    ![Aktualizacja została ukończona](media/iot-suite-visualize-connecting/complete.png)

> [!NOTE]
> Aby śledzić stan zadania w rozwiązaniu, wybierz **widoku**.

## <a name="next-steps"></a>Kolejne kroki

Artykuł [dostosować akcelerator rozwiązań zdalne monitorowanie](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) opisano niektóre sposoby, aby dostosować akcelerator rozwiązań.