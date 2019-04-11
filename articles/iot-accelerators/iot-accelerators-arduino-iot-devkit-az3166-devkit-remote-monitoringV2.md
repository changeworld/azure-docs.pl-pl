---
title: Mxchip IoT DevKit nawiązać połączenie zdalne monitorowanie akcelerator rozwiązań — Azure | Dokumentacja firmy Microsoft
description: W tym przewodniku dowiesz się, jak wysyłać dane telemetryczne z czujników na urządzeniu IoT DevKit az3166 usługi do monitorowania zdalnego akcelerator rozwiązań do monitorowania i wizualizacji.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 3551d088c1d02715bf9ace09d7eb0048bc10111e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105254"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>Podłącz urządzenie z systemem mxchip IoT DevKit do akceleratora rozwiązania monitorowania zdalnego

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ten poradnik pokazuje, jak uruchomić przykładową aplikację na urządzeniu IoT Mxchip. Przykładowy kod wysyła dane telemetryczne z czujników na urządzeniu Mxchip, aby Twój akcelerator rozwiązań.

[Zestawu deweloperskiego IoT Mxchip](https://aka.ms/iot-devkit) to w jednym Arduino zgodne z czujników i urządzeń peryferyjnych sformatowanego. Możesz tworzyć w ramach [Workbench urządzenia IoT Azure](https://aka.ms/iot-workbench) lub [narzędzia IoT Azure](https://aka.ms/azure-iot-tools) pakiet rozszerzenia programu Visual Studio Code. [Katalogu projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) zawiera przykładowe aplikacje ułatwiające prototypów rozwiązań IoT.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wykonanie czynności opisanych w tym samouczku, należy najpierw wykonać następujące zadania:

* Przygotuj swoje Mxchip wykonując kroki opisane w [połączyć DevKit az3166 usługi IoT dla usługi Azure IoT Hub w chmurze](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="open-sample-project"></a>Otwórz przykładowy projekt

Aby otworzyć przykładowy zdalnego monitorowania w programie VS Code:

1. Upewnij się, że Twoje mxchip IoT DevKit nie jest do tego komputera. Najpierw uruchom program VS Code, a następnie podłącz Mxchip do komputera.

1. Kliknij przycisk `F1` , aby otworzyć paletę poleceń, wpisz i wybierz **Workbench urządzenia IoT platformy Azure: Otwórz przykłady...** . Następnie wybierz pozycję **mxchip IoT DevKit** jako tablica.

1. Znajdź **zdalne monitorowanie** i kliknij przycisk **Otwórz przykładowy**. Zostanie otwarte nowe okno programu VS Code, przedstawiający folder projektu:

   ![IoT aplikacji Workbench wybierz przykład zdalnego monitorowania](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Konfigurowanie urządzenia

Aby skonfigurować parametry połączenia urządzenia usługi IoT Hub na urządzeniu z systemem Mxchip:

1. Przełącz mxchip IoT DevKit do **tryb konfiguracji**:

    * Wciśnij przycisk **A**.
    * Naciśnij i zwolnij **resetowania** przycisku.

1. Na ekranie zostanie wyświetlony identyfikator Mxchip i `Configuration`.

    ![Tryb konfiguracji mxchip IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Naciśnij klawisz **F1** , aby otworzyć paletę poleceń, wpisz i wybierz **Workbench urządzenia IoT platformy Azure: Konfigurowanie ustawień urządzenia... > Parametry połączenia urządzenia Config**.

1. Wklej skopiowane wcześniej parametry połączenia, a następnie naciśnij klawisz **Enter** do skonfigurowania urządzenia.

## <a name="build-the-code"></a>Kompilowanie kod

Aby skompilować i przekazać kod urządzenia:

1. Naciśnij klawisz `F1` , aby otworzyć paletę poleceń, wpisz i wybierz **Workbench urządzenia IoT platformy Azure: Przekazywanie kodu urządzenia**:

1. Program VS Code kompiluje i przekazuje kod do Twojego urządzenia Mxchip:

    ![IoT Workbench: Urządzenia - > przekazany](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. Urządzenie Mxchip ponowne uruchomienie i uruchamia kod, który został przekazany.

## <a name="test-the-sample"></a>Test próbki

Aby sprawdzić, czy przykładowej aplikacji, który został przekazany do urządzenia Mxchip działa, wykonaj następujące czynności:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Widok danych telemetrycznych wysyłanych do rozwiązania do zdalnego monitorowania

Po uruchomieniu aplikacji przykładowej, urządzenie Mxchip wysyła dane telemetryczne z jego danych czujników za pośrednictwem sieci Wi-Fi na Twój akcelerator rozwiązań. Aby wyświetlić dane telemetryczne:

1. Przejdź do pulpitu nawigacyjnego rozwiązania, a następnie kliknij przycisk **Device Explorer**.

1. Kliknij nazwę urządzenia Mxchip urządzenia. na karcie po prawej stronie można wyświetlić dane telemetryczne z Mxchip w czasie rzeczywistym:

    ![Danych z czujników w usłudze Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>Kontrolę na urządzeniem Mxchip

Akcelerator rozwiązania monitorowania zdalnego umożliwia zdalne sterowanie urządzenia. Przykładowy kod implementuje trzy metody, które można zobaczyć w **metoda** sekcji po wybraniu urządzenia na **Device Explorer** strony:

![Metody mxchip IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Aby zmienić kolor jednego z diod LED Mxchip, użyj **LedColor** metody:

1. Wybierz nazwę urządzenia, z listy urządzeń i kliknij **zadań**:

    ![Utwórz zadanie](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Konfigurowanie zadań przy użyciu następujących wartości, a następnie kliknij przycisk **Zastosuj**:

   * Wybierz zadania: **Run — metoda**
   * Nazwa metody: **LedColor**
   * Nazwa zadania: **ChangeLedColor**

     ![Ustawienia zadania](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Po kilku sekundach zmienia kolor LED RGB (poniżej przycisku, A) w swojej Mxchip:

    ![Czerwony mxchip IoT DevKit prowadzone](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do samouczków, nie usuwaj wdrożenia akceleratora rozwiązania do monitorowania zdalnego.

Jeśli akcelerator rozwiązań nie są już potrzebne, usuń go ze strony rozwiązania Aprowizowana, wybierając ją, a następnie klikając polecenie Usuń rozwiązanie:

![Usuwanie rozwiązania](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz żadnych problemów, zapoznaj się [Mxchip IoT często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub skontaktowanie się z nami za pomocą następujących kanałów:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wyjaśniono sposób połączyć urządzenie Mxchip Twój akcelerator rozwiązań monitorowania zdalnego, poniżej przedstawiono niektóre Sugerowane następne kroki:

* [Omówienie akceleratorów rozwiązań w usłudze Azure IoT](https://docs.microsoft.com/azure/iot-accelerators/)
* [Dostosowywanie interfejsu użytkownika](iot-accelerators-remote-monitoring-customize.md)
* [Połącz IoT Mxchip z aplikacją usługi Azure IoT Central](../iot-central/howto-connect-devkit.md)