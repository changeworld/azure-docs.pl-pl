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
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61449683"
---
## <a name="view-device-telemetry"></a>Wyświetlanie danych telemetrycznych z urządzenia

Możesz wyświetlić dane telemetryczne wysyłane z urządzenia **Device Explorer** strony w rozwiązaniu.

1. Wybierz urządzenie, którego obsługę zainicjowano na liście urządzeń na **Device Explorer** strony. Panel wyświetla informacje o urządzeniu, w tym wykres danych telemetrycznych z urządzenia:

    ![Zobacz szczegóły urządzenia](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Wybierz **wykorzystanie** można zmienić wyświetlanie telemetrii:

    ![Wyświetlanie danych telemetrycznych z wykorzystania](media/iot-suite-visualize-connecting/devicespressure.png)

1. Aby wyświetlić informacje diagnostyczne o urządzeniu, przewiń w dół do **diagnostyki**:

    ![Wyświetl diagnostykę urządzenia](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Działa na twoim urządzeniu

Aby wywołać metod na urządzaniach, należy użyć **Device Explorer** strony w rozwiązaniu do zdalnego monitorowania. Na przykład w przypadku rozwiązania do zdalnego monitorowania **Chłodnica** Implementowanie urządzeń **ponowny rozruch** metody.

1. Wybierz **urządzeń** można przejść do **Device Explorer** strony w rozwiązaniu.

1. Wybierz urządzenie, którego obsługę zainicjowano na liście urządzeń na **Device Explorer** strony:

    ![Wybierz swoje rzeczywiste urządzenie](media/iot-suite-visualize-connecting/devicesselect.png)

1. Aby wyświetlić listę metod, można wywołać na urządzeniu przenośnym, wybierz **zadania**, następnie **metody**. Aby zaplanować zadanie do uruchamiania na wielu urządzeniach, można wybrać wiele urządzeń, na liście. **Zadań** panelu zawiera typy metody wspólne dla wszystkich urządzeń, które są wybrane.

1. Wybierz **ponowny rozruch**, ustaw nazwę zadania na **RebootPhysicalChiller** , a następnie wybierz **Zastosuj**:

    ![Planowanie aktualizacji oprogramowania układowego](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Wyświetla sekwencję wiadomości w konsoli uruchamiania kodu urządzenia, gdy symulowane urządzenie obsłuży metodę.

> [!NOTE]
> Aby śledzić stan zadania w rozwiązaniu, wybierz **wyświetlanie stanu zadania**.

## <a name="next-steps"></a>Kolejne kroki

Artykuł [dostosować akcelerator rozwiązań zdalne monitorowanie](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) opisano niektóre sposoby, aby dostosować akcelerator rozwiązań.