---
title: Łączenie zestawu IoT DevKit z rozwiązaniem do zdalnego monitorowania — Platforma Azure | Dokumenty firmy Microsoft
description: W tym przewodniku instrukcja, dowiesz się, jak wysłać dane telemetryczne z czujników na urządzeniu IoT DevKit AZ3166 do zdalnego monitorowania akceleratora rozwiązania do monitorowania i wizualizacji.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 6e9f9c89cf2e5e40d37a1532e688490aae294181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888862"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>Podłączanie urządzenia IoT DevKit do akceleratora rozwiązań do zdalnego monitorowania

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

W tym przewodniku przedstawiono sposób uruchamiania przykładowej aplikacji na urządzeniu IoT DevKit. Przykładowy kod wysyła dane telemetryczne z czujników na urządzeniu DevKit do akceleratora rozwiązania.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) to płyta kompatybilna z all-in-one Arduino z bogatymi urządzeniami peryferyjnymi i czujnikami. Można go opracować przy użyciu [usługi Azure IoT Device Workbench](https://aka.ms/iot-workbench) lub pakietu rozszerzeń [narzędzi Azure IoT w](https://aka.ms/azure-iot-tools) programie Visual Studio Code. [Katalog projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) zawiera przykładowe aplikacje ułatwiające tworzenie prototypów rozwiązań IoT.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym samouczku, wykonaj następujące czynności:

* Przygotuj devkit, wykonując kroki opisane w [connect IoT DevKit AZ3166 do usługi Azure IoT Hub w chmurze.](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)

## <a name="open-sample-project"></a>Otwórz przykładowy projekt

Aby otworzyć przykład zdalnego monitorowania w programie VS Code:

1. Upewnij się, że zestaw IoT DevKit nie jest na komputerze. Najpierw uruchom program VS Code, a następnie podłącz devkit do komputera.

1. Kliknij, `F1` aby otworzyć paletę poleceń, wpisz i wybierz pozycję Azure **IoT Device Workbench: Open Examples...**. Następnie wybierz **IoT DevKit** jako tablicę.

1. Znajdź **zdalne monitorowanie** i kliknij przycisk **Otwórz próbkę**. Zostanie otwarte nowe okno programu VS Code z folderem projektu:

   ![IoT Workbench, wybierz przykład Zdalnego monitorowania](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Konfigurowanie urządzenia

Aby skonfigurować parametry połączenia urządzenia Usługi IoT Hub na urządzeniu DevKit:

1. Przełączanie zestawu IoT DevKit w **tryb konfiguracji:**

    * Przytrzymaj przycisk **A**.
    * Naciśnij i zwolnij przycisk **Reset.**

1. Na ekranie wyświetlany jest `Configuration`identyfikator DevKit i .

    ![Tryb konfiguracji zestawu IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Naciśnij **klawisz F1,** aby otworzyć paletę poleceń, wpisz i wybierz pozycję **Azure IoT Device Workbench: Configure Device Settings... > Config Device Connection String**.

1. Wklej wcześniej skopiowany ciąg połączenia i naciśnij klawisz **Enter,** aby skonfigurować urządzenie.

## <a name="build-the-code"></a>Kompilowanie kod

Aby utworzyć i przesłać kod urządzenia:

1. Naciśnij, `F1` aby otworzyć paletę poleceń, wpisz i wybierz pozycję **Azure IoT Device Workbench: Przekaż kod urządzenia:**

1. VS Code kompiluje i przekazuje kod do urządzenia DevKit:

    ![IoT Workbench: Urządzenie - > przesłane](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. Urządzenie DevKit uruchamia się ponownie i uruchamia przekazany kod.

## <a name="test-the-sample"></a>Badanie próbki

Aby sprawdzić, czy przykładowa aplikacja przekazana na urządzeniu DevKit działa, wykonaj następujące kroki:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Wyświetlanie danych telemetrycznych wysyłanych do rozwiązania do zdalnego monitorowania

Po uruchomieniu przykładowej aplikacji urządzenie DevKit wysyła dane telemetryczne z danych czujników za pomocą sieci Wi-Fi do akceleratora rozwiązań. Aby wyświetlić dane telemetryczne:

1. Przejdź do pulpitu nawigacyjnego rozwiązania i kliknij pozycję **Eksplorator urządzeń**.

1. Kliknij nazwę urządzenia DevKit. na karcie po prawej stronie, można zobaczyć dane telemetryczne z DevKit w czasie rzeczywistym:

    ![Dane czujników w pakiecie IoT Suite platformy Azure](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>Sterowanie urządzeniem DevKit

Akcelerator rozwiązań do zdalnego monitorowania umożliwia zdalne sterowanie urządzeniem. Przykładowy kod implementuje trzy metody, które można zobaczyć w sekcji **Metoda** po wybraniu urządzenia na stronie **Eksploratora urządzeń:**

![Metody IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Aby zmienić kolor jednej z diod LED DevKit, użyj metody **LedColor:**

1. Wybierz nazwę urządzenia z listy urządzeń i kliknij **zadania:**

    ![Tworzenie zadania](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Skonfiguruj zadania przy użyciu następujących wartości i kliknij przycisk **Zastosuj:**

   * Wybierz zadanie: **Uruchom metodę**
   * Nazwa metody: **LedColor**
   * Nazwa zadania: **ChangeLedColor**

     ![Ustawienia zadania](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Po kilku sekundach zmienia się kolor diody LED RGB (poniżej przycisku A) na DevKit:

    ![Czerwona dioda IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do samouczków, nie usuwaj wdrożenia akceleratora rozwiązania do monitorowania zdalnego.

Jeśli akcelerator rozwiązań nie jest już potrzebny, usuń go ze strony Rozwiązania aprowizacji, zaznaczając go, a następnie klikając pozycję Usuń rozwiązanie:

![Usuwanie rozwiązania](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz jakiekolwiek problemy, zapoznaj się [z często zadawanymi pytaniami dotyczącymi IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub skontaktuj się z nami za pomocą następujących kanałów:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak podłączyć urządzenie DevKit do akceleratora rozwiązań do zdalnego monitorowania, oto kilka sugerowanych następnych kroków:

* [Omówienie akceleratorów rozwiązań IoT platformy Azure](https://docs.microsoft.com/azure/iot-accelerators/)
* [Dostosowywanie interfejsu użytkownika](iot-accelerators-remote-monitoring-customize.md)
* [Łączenie zestawu IoT DevKit z aplikacją Azure IoT Central](../iot-central/core/howto-connect-devkit.md)