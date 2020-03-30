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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183665"
---
## <a name="view-device-telemetry"></a>Wyświetlanie danych telemetrycznych z urządzenia

Dane telemetryczne wysyłane z urządzenia można wyświetlić na stronie **Eksploratora urządzeń** w rozwiązaniu.

1. Wybierz urządzenie aprowizowane na liście urządzeń na stronie **Eksplorator urządzeń.** Panel wyświetla informacje o urządzeniu, w tym wykres danych telemetrycznych urządzenia:

    ![Zobacz szczegóły urządzenia](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Wybierz **opcję Nacisk,** aby zmienić wyświetlanie danych telemetrycznych:

    ![Wyświetlanie danych telemetrycznych ciśnienia](media/iot-suite-visualize-connecting/devicespressure.png)

1. Aby wyświetlić informacje diagnostyczne o urządzeniu, przewiń w dół do **diagnostyki:**

    ![Wyświetlanie diagnostyki urządzenia](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Działaj na swoim urządzeniu

Aby wywołać metody na urządzeniach, użyj strony **Eksploratora urządzeń** w rozwiązaniu do zdalnego monitorowania. Na przykład w rozwiązaniu zdalnego monitorowania **urządzeń agregatu chłodniczego** implementuje **metodę ponownego uruchamiania.**

1. Wybierz **pozycję Urządzenia,** aby przejść do strony **Eksploratora urządzeń** w rozwiązaniu.

1. Wybierz urządzenie aprowizowane na liście urządzeń na stronie **Eksploratora urządzeń:**

    ![Wybierz swoje prawdziwe urządzenie](media/iot-suite-visualize-connecting/devicesselect.png)

1. Aby wyświetlić listę metod, które można wywołać na urządzeniu, wybierz pozycję **Zadania**, a następnie **metody**. Aby zaplanować uruchamianie zadania na wielu urządzeniach, można wybrać wiele urządzeń na liście. Panel **Zadania** pokazuje typy metod wspólne dla wszystkich wybranych urządzeń.

1. Wybierz **pozycję Uruchom ponownie**, ustaw nazwę zadania na **RebootPhysicalChiller,** a następnie wybierz pozycję **Zastosuj:**

    ![Planowanie aktualizacji oprogramowania układowego](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Sekwencja komunikatów jest wyświetlana w konsoli z kodem urządzenia, podczas gdy symulowane urządzenie obsługuje metodę.

> [!NOTE]
> Aby śledzić stan zadania w rozwiązaniu, wybierz pozycję **Wyświetl stan zadania**.

## <a name="next-steps"></a>Następne kroki

W [artykule Dostosowywanie akceleratora rozwiązań do zdalnego monitorowania](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) opisano niektóre sposoby dostosowywania akceleratora rozwiązań.