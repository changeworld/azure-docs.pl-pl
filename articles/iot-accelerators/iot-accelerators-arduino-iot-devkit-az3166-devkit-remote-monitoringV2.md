---
title: IoT Mxchip z chmurą — nawiązać połączenie z IoT DevKit az3166 usługi akcelerator rozwiązań IoT monitorowania zdalnego | Dokumentacja firmy Microsoft
description: W tym samouczku Dowiedz się, jak wysyłać stan czujników na DevKit az3166 usługi IoT do zdalnego monitorowania IoT akcelerator rozwiązań do monitorowania i wizualizacji.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: isacabe
ms.openlocfilehash: 35ef6ef02e5ae8a4b9231121615f44e0dc00ad15
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378741"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Łączenie zestawu deweloperskiego IoT DevKit az3166 usługi do akceleratora rozwiązań IoT zdalnego monitorowania

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

W tym samouczku dowiesz się, jak uruchomić przykładową aplikację w swojej Mxchip wysyłać danych z czujników Twój akcelerator rozwiązań.

[Zestawu deweloperskiego IoT Mxchip](https://aka.ms/iot-devkit) to w jednym Arduino zgodne z czujników i urządzeń peryferyjnych sformatowanego. Możesz tworzyć w ramach [usługi Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) w programie Visual Studio Code. I pochodzi ona z rosnącej [katalogu projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) przeprowadzenie Cię prototypu rozwiązania Internetu rzeczy (IoT), które korzystać z usług Microsoft Azure.

## <a name="what-you-need"></a>Co jest potrzebne

Zapoznaj się z artykułem [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) i **Zakończ poniższe sekcje**:

* Przygotowania sprzętu
* Konfigurowanie sieci Wi-Fi
* Rozpoczynanie korzystania z Mxchip
* Przygotowywanie środowiska deweloperskiego


## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Utwórz akcelerator rozwiązań Azure IoT zdalnego monitorowania

Urządzenie az3166 usługi, które możesz utworzyć w tym samouczku wysyła dane do wystąpienia akceleratora rozwiązania monitorowania zdalnego. Jeśli wystąpienie nie zostało jeszcze aprowizowane na koncie platformy Azure, postępuj zgodnie z [Szybki Start](https://docs.microsoft.com/azure/iot-accelerators/quickstart-remote-monitoring-deploy) przewodnika, aby to zrobić.

Po zakończeniu wdrażania rozwiązania do monitorowania zdalnego, Otwórz pulpit nawigacyjny rozwiązania.

![Pulpit nawigacyjny rozwiązania](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard-info.png)

## <a name="add-a-device-to-the-remote-monitoring-solution"></a>Dodawanie urządzenia do rozwiązania do zdalnego monitorowania

1. Na pulpicie nawigacyjnym, przejdź do **urządzeń** sekcji, a następnie kliknij przycisk **nowe urządzenie**.
   ![Dodawanie nowego urządzenia](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-add-device.png)

2. Konfigurowanie urządzenia, korzystając z poniższych informacji, a następnie kliknij przycisk **Zastosuj**.
  * Typ urządzenia: **fizycznych**
  * Identyfikator urządzenia: **az3166 usługi**
  * Typ uwierzytelniania: **klucz symetryczny**
  * Klucz uwierzytelniania: **automatycznie Generuj klucze**
  
  ![Dodawanie nowego formularza urządzenia](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-add-new-device-form.png)

3. Po utworzeniu nowego urządzenia, skopiować **parametry połączenia klucza podstawowego**. Jest to urządzenie, które jest utworzony w usłudze Azure IoT Hub poniżej.
  
  ![Parametry połączenia urządzenia](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-new-device-connstring.png)

## <a name="open-the-remote-monitoring-sample-in-vs-code"></a>Otwórz przykładową zdalnego monitorowania w programie VS Code

1. Upewnij się, jest Twoja mxchip IoT DevKit **niepołączony** do komputera. Najpierw uruchom program VS Code, a następnie podłącz Mxchip do komputera.

1. Kliknij przycisk `F1` aby otworzyć paletę poleceń, typ i wybierz **IoT Workbench: przykłady**. Następnie wybierz pozycję **mxchip IoT DevKit** jako tablica.

1. Znajdź **zdalne monitorowanie** i kliknij przycisk **Otwórz przykładowy**. Nowe okno programu VS Code zostanie otwarty z folderu projektu w nim.
  ![IoT aplikacji Workbench wybierz przykład zdalnego monitorowania](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-iot-hub-device-connection-string"></a>Konfigurowanie parametrów połączenia urządzenia usługi IoT Hub

1. Przełącz mxchip IoT DevKit do **tryb konfiguracji**. W tym celu:
   * Wciśnij przycisk **A**.
   * Naciśnij i zwolnij **resetowania** przycisku.

2. Na ekranie zostanie wyświetlony identyfikator Mxchip i "Konfiguracja".
   
  ![Tryb konfiguracji mxchip IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

3. Kliknij przycisk `F1` aby otworzyć paletę poleceń, typ i wybierz **IoT Workbench: urządzenia > Ustawienia urządzenia Config**.

4. Wklej parametry połączenia, po prostu skopiować kliknij `Enter` ją skonfigurować.

## <a name="build-and-upload-the-device-code"></a>Tworzenie i przekazywanie kodu urządzenia

1. Kliknij przycisk `F1` aby otworzyć paletę poleceń, typ i wybierz **IoT Workbench: urządzenia > Przekaż urządzenia**.
  ![IoT Workbench: Urządzenia - > Przekaż](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-upload.png)

1. Program VS Code, a następnie uruchamia kompilowania i przekazywanie kodu do Twojej Mxchip.
  ![IoT Workbench: Urządzenia - > przekazany](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

Mxchip rozruchu i uruchamiania kodu.

## <a name="test-the-project"></a>Projekt testowy

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Widok danych telemetrycznych wysyłanych do rozwiązania do zdalnego monitorowania

Po uruchomieniu aplikacji przykładowej, Mxchip wysyła dane czujników za pośrednictwem sieci Wi-Fi do rozwiązania do zdalnego monitorowania. Aby wyświetlić wynik, wykonaj następujące kroki:

1. Przejdź do pulpitu nawigacyjnego rozwiązania, a następnie kliknij przycisk **urządzeń**.

1. Kliknij nazwę urządzenia (az3166 usługi), zostanie otwarta karta po prawej stronie pulpitu nawigacyjnego i tam, gdzie zobaczysz stan czujnika na Mxchip w czasie rzeczywistym.
  ![Danych z czujników w usłudze Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="send-a-c2d-message"></a>Wyślij wiadomość C2D

Rozwiązania do monitorowania zdalnego umożliwia wywoływanie metody zdalnej na urządzeniu. Kod sxample publikuje trzy metody, które można zobaczyć w **metoda** sekcji po wybraniu czujnika.

![Metody mxchip IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Daj nam spróbuj zmienić kolor Mxchip diod LED, przy użyciu metody "LedColor".

1. Wybierz **az3166 usługi** listy urządzeń, a następnie kliknij na **zadań**.

  ![Utwórz zadanie](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

2. Konfigurowanie zadań, tak jak pokazano poniżej, a następnie kliknij przycisk **Zastosuj**.
  * Wybierz zadanie: **Run — metoda**
  * Nazwa metody: **LedColor**
  * Nazwa zadania: **ChangeLedColor**
  
  ![Ustawienia zadania](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

W kilka sekund Twoje Mxchip należy zmieniać kolor LED RGB (poniżej przycisku, A).

![Czerwony mxchip IoT DevKit prowadzone](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do samouczków, nie usuwaj wdrożenia akceleratora rozwiązań do zdalnego monitorowania.

Jeśli akcelerator rozwiązań nie są już potrzebne, usuń go ze strony rozwiązania Aprowizowana, wybierając ją, a następnie klikając polecenie Usuń rozwiązanie:

![Usuwanie rozwiązania](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz problemy, zapoznaj się [Mxchip IoT często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub skontaktowanie się z nami za pomocą następujących kanałów:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Witryna Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak połączyć urządzenie Mxchip Twój akcelerator rozwiązań Azure IoT zdalne monitorowanie i wizualizowanie danych z czujników, Oto zalecane kolejne kroki:

* [Omówienie akceleratorów rozwiązań w usłudze Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Dostosowywanie interfejsu użytkownika](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)
* [Połącz IoT Mxchip z aplikacją usługi Azure IoT Central](../iot-central/howto-connect-devkit.md)