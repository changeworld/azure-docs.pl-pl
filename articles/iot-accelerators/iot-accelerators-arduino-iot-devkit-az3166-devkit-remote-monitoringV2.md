---
title: IoT Mxchip z chmurą — nawiązać połączenie z IoT DevKit az3166 usługi akcelerator rozwiązań IoT monitorowania zdalnego | Dokumentacja firmy Microsoft
description: W tym samouczku Dowiedz się, jak wysyłać stan czujników na DevKit az3166 usługi IoT do zdalnego monitorowania IoT akcelerator rozwiązań do monitorowania i wizualizacji.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: isacabe
ms.openlocfilehash: d3175290c1a7fca5e35f4438392f29324868f1a3
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054920"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Łączenie zestawu deweloperskiego IoT DevKit az3166 usługi do akceleratora rozwiązań IoT zdalnego monitorowania


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

W tym samouczku dowiesz się, jak uruchomić przykładową aplikację w swojej Mxchip wysyłać danych z czujników Twój akcelerator rozwiązań.

[Zestawu deweloperskiego IoT Mxchip](https://aka.ms/iot-devkit) to w jednym Arduino zgodne z czujników i urządzeń peryferyjnych sformatowanego. Możesz tworzyć w ramach [rozszerzenie programu Visual Studio Code dla Arduino](https://aka.ms/arduino). I pochodzi ona z rosnącej [katalogu projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) przeprowadzenie Cię prototypu rozwiązania Internetu rzeczy (IoT), które korzystać z usług Microsoft Azure.

## <a name="what-you-need"></a>Co jest potrzebne

Zapoznaj się z artykułem [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) i **Zakończ poniższe sekcje**:

* Przygotowania sprzętu
* Konfigurowanie sieci Wi-Fi
* Rozpoczynanie korzystania z Mxchip
* Przygotowywanie środowiska deweloperskiego


## <a name="open-the-remotemonitoring-sample-in-vs-code"></a>Otwórz przykładową RemoteMonitoring w programie VS Code

1. Odłącz zestawu deweloperskiego Mxchip z tego komputera, jeśli jest połączony.

2. Uruchom program VS Code.

3. Łączenie zestawu deweloperskiego Mxchip do komputera.

4. Użyj `Ctrl+Shift+P` (z systemem macOS: `Cmd+Shift+P`) aby otworzyć paletę poleceń, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: przykłady**.

 5. Po lewej stronie rozwiń **przykłady ARDUINO** sekcji, przejdź do **przykłady zestawu DEWELOPERSKIEGO az3166 usługi > AzureIoT**i wybierz **RemoteMonitoringv2**. Nowe okno programu VS Code zostanie otwarty z folderu projektu w nim.

  > [!NOTE]
  > Jeśli użytkownik nie widzisz **przykłady zestawu DEWELOPERSKIEGO**, użyj `Ctrl+Shift+P` (z systemem macOS: `Cmd+Shift+P`) aby otworzyć paletę poleceń i typ **Menedżera tablicy Arduino**. Zaznacz go, a następnie wyszukaj **az3166 usługi** w Menedżerze tablicy. Należy następnie powtórz krok 5 powyżej i zobacz przykłady.

  ![Otwórz projekt do monitorowania zdalnego](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)

  > [!NOTE]
  > Jeśli występuje Zamknij okienko zostanie ponownie otwarty. Użyj `Ctrl+Shift+P` (z systemem macOS: `Cmd+Shift+P`) aby otworzyć paletę poleceń, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: przykłady**.

## <a name="build-and-upload-the-device-code-to-your-mxchip"></a>Tworzenie i przekazywanie kodu urządzenia do Twojego zestawu deweloperskiego

1. Użyj `Ctrl+P` (z systemem macOS: `Cmd + P`) i typ **zadań konfiguracji urządzenia połączeń**.

  ![Wybierz subskrypcję platformy Azure i usługą IoT Hub](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. Naciśnij w terminalu pyta, czy chcesz użyć parametrów połączenia urządzenia IoT, czy chcesz użyć. Wybierz *tworzenia nowych*i wklej go.

  ![Wklej parametry połączenia](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. Naciśnij w terminalu czasami wyświetli monit o wprowadzenie tryb konfiguracji. Aby to zrobić, przytrzymaj wciśnięty **przycisk A**, Wypchnij i zwolnij **przycisk Resetuj** a następnie zwolnij przycisk A. Na ekranie zostanie wyświetlony identyfikator Mxchip i "Konfiguracja".

  ![Ekran Mxchip urządzenia](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > Parametry połączenia powinny być zapisane w Schowka, gdy po ostatniej części tego samouczka. Jeśli nie, należy przejść do witryny Azure portal i wyszukaj Centrum IoT, grupy zasobów zdalnego monitorowania. Widać tam połączone urządzenia i skopiuj parametry połączenia urządzenia usługi IoT Hub.

  ![Zwróć uwagę na parametry połączenia](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)

  Teraz możesz pomyślnie nawiązano połączenie i zweryfikowane urządzenia zestawu deweloperskiego Centrum IoT. Aby zobaczyć nowe urządzenie fizyczne w sekcji "Azure IoT Hub Devices" programu VS Code, możesz pobrać [rozszerzenie Azure IoT Toolkit.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) 

  Teraz możesz zobaczyć nowe urządzenie fizyczne w sekcji "Azure IoT Hub Devices" programu VS Code:

  ![Zwróć uwagę, nowych urządzeń usługi IoT Hub](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

4. Na koniec możesz przekazać kod RemoteMonitoringV2.ino na Twojego zestawu deweloperskiego, aby rozpocząć wysyłanie danych do Twój akcelerator rozwiązań IoT. Użyj `Ctrl + Shift + P` (z systemem macOS: `Cmd + Shift + P`) i typ **przekazywanie Arduino**. Program VS Code zostanie następnie rozpocznij przekazywanie kodu do Twojego zestawu deweloperskiego i powiadomienie po zakończeniu. 

## <a name="test-the-project"></a>Projekt testowy

Po uruchomieniu aplikacji przykładowej Twojego zestawu deweloperskiego Mxchip wysyła dane czujników za pośrednictwem sieci Wi-Fi do Akceleratory rozwiązań IoT. Aby wyświetlić wynik, wykonaj następujące kroki:

1. Przejdź do Twój akcelerator rozwiązań IoT, a następnie kliknij przycisk **pulpit NAWIGACYJNY**.

2. W konsoli akcelerator rozwiązań IoT zobaczysz swój status czujnik zestawu deweloperskiego Mxchip. 

![Danych z czujników w akceleratorów rozwiązań IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

Po kliknięciu nazwy czujnika (az3166 usługi) zostanie otwarta karta, która po prawej stronie pulpitu nawigacyjnego, w którym można zobaczyć wykres zestawu deweloperskiego czujników w czasie rzeczywistym.


## <a name="send-a-c2d-message"></a>Wyślij wiadomość C2D
Zdalne monitorowanie v2 umożliwia wywołania zdalnej metody na urządzeniu.
Przykładowy kod mikroukładu MX publikuje trzy metody, które widać w sekcji metodę po wybraniu czujnika.

![Mikroukład MX metody](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

Można zmienić kolor jednego z diod LED mikroukładu MX, przy użyciu metody "LedColor". Dla to zrobić, zaznacz pole wyboru, urządzenia i kliknij przycisk harmonogramu. 

![Mikroukład MX metody](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

Wybierz metodę o nazwie ChangeColor w menu rozwijanym, gdzie wszystkie metody są wyświetlane, wpisana nazwa i zastosować.

![Lista rozwijana MX mikroukładu](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

W ciągu kilku sekund fizycznych modułem MX należy zmienić kolor RGB LED (poniżej a przycisk)

![Mikroukład MX LED](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz problemy, zapoznaj się [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub się z nami od następujące kanały:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Witryna Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak połączyć urządzenie Mxchip Akceleratory rozwiązań IoT i wizualizowanie danych z czujników, Oto zalecane kolejne kroki:

* [Omówienie akceleratorów rozwiązań IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Podłącz urządzenie z systemem zestawu deweloperskiego IoT Mxchip z aplikacją Microsoft IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
* [Zestaw dla deweloperów IoT](https://microsoft.github.io/azure-iot-developer-kit/)
