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
ms.openlocfilehash: 92ce85a3cc94702468a13348d3a41667498c68f5
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43187395"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Łączenie zestawu deweloperskiego IoT DevKit az3166 usługi do akceleratora rozwiązań IoT zdalnego monitorowania


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

W tym samouczku dowiesz się, jak uruchomić przykładową aplikację w swojej Mxchip wysyłać danych z czujników Twój akcelerator rozwiązań.

[Zestawu deweloperskiego IoT Mxchip](https://aka.ms/iot-devkit) to w jednym Arduino zgodne z czujników i urządzeń peryferyjnych sformatowanego. Możesz tworzyć w ramach [rozszerzenie programu Visual Studio Code dla Arduino](https://aka.ms/arduino). I pochodzi ona z rosnącej [katalogu projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) przeprowadzenie Cię prototypu rozwiązania Internetu rzeczy (IoT), które korzystać z usług Microsoft Azure.

## <a name="what-you-need"></a>Co jest potrzebne

Zakończ [przewodnik wprowadzenie](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) do:

* Twoje Mxchip nawiązano połączenie z sieci Wi-Fi
* Przygotowywanie środowiska deweloperskiego


## <a name="open-the-remotemonitoring-sample"></a>Otwórz przykładową RemoteMonitoring

1. Rozłącz Mxchip z tego komputera, jeśli jest ona połączona.

2. Uruchom program VS Code.

3. Podłącz Mxchip do komputera. Program VS Code automatycznie wykrywa swoje Mxchip i otwiera następujące strony:
  * Strona wprowadzenia Mxchip.
  * Przykłady Arduino: Przykłady praktyczne wprowadzenie Mxchip.

4. Po lewej stronie rozwiń **przykłady ARDUINO** sekcji, przejdź do **przykłady zestawu DEWELOPERSKIEGO az3166 usługi > AzureIoT**i wybierz **RemoteMonitoringv2**. Nowe okno programu VS Code zostanie otwarty z folderu projektu w nim.

  ![Otwórz projekt do monitorowania zdalnego](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)


  > [!NOTE]
  > Jeśli występuje Zamknij okienko zostanie ponownie otwarty. Użyj `Ctrl+Shift+P` (z systemem macOS: `Cmd+Shift+P`) aby otworzyć paletę poleceń, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: przykłady**.

## <a name="add-a-new-physical-device"></a>Dodaj nowe urządzenie fizyczne

W portalu, przejdź do **urządzeń** sekcji, a następnie w wyświetlonym obszarze kliknij **+ nowe urządzenie** przycisku. 

![Dodawanie nowego urządzenia](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device.png)

*Nowy formularz urządzenia* powinno być wypełnione.
1. Kliknij przycisk **fizycznych** w *typu urządzenia* sekcji.
2. Zdefiniować własny identyfikator urządzenia (na przykład *zestawu deweloperskiego* lub *az3166 usługi*).
3. Wybierz **automatycznie Generuj klucze** w *klucz uwierzytelniania* sekcji.
4. Kliknij przycisk *Zastosuj* przycisku.

![Dodawanie nowego formularza urządzenia](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-new-device-form.png)

Poczekaj, aż portalu zakończy się aprowizacja nowego urządzenia.

![Aprowizacja nowego urządzenia ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device-provisioning.png)


Następnie zostanie wyświetlony konfiguracji nowego urządzenia.
Kopiuj **parametry połączenia** wygenerowany.

![Parametry połączenia urządzenia](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-new-device-connstring.png)


Te parametry połączenia będą używane w następnej sekcji.





## <a name="build-and-upload-the-device-code"></a>Tworzenie i przekazywanie kodu urządzenia

Wróć do programu Visual Studio Code: 

1. Użyj `Ctrl+P` (z systemem macOS: `Cmd + P`) i typ **zadań konfiguracji urządzenia połączeń**.

  ![Wybierz subskrypcję platformy Azure i usługą IoT Hub](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. Naciśnij w terminalu pyta, czy chcesz użyć parametrów połączenia urządzenia IoT, czy chcesz użyć. Wybierz *tworzenia nowych*i wklej go.

  ![Wklej parametry połączenia](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. Naciśnij w terminalu czasami wyświetli monit o wprowadzenie tryb konfiguracji. Aby to zrobić, przytrzymaj naciśnięty przycisk A, a następnie wypychanie i zwolnij przycisk reset a następnie zwolnij przycisk A. Na ekranie zostanie wyświetlony identyfikator Mxchip i "Konfiguracja".

  ![Ekran Mxchip urządzenia](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > Parametry połączenia powinny być zapisane w Schowka, gdy po ostatniej części tego samouczka. Jeśli nie, należy przejść do witryny Azure portal i wyszukaj Centrum IoT, grupy zasobów zdalnego monitorowania. Widać tam połączone urządzenia i skopiuj parametry połączenia urządzenia usługi IoT Hub.

  ![Zwróć uwagę na parametry połączenia](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)


Teraz możesz zobaczyć nowe urządzenie fizyczne w sekcji "Azure IoT Hub Devices" programu VS Code:

![Zwróć uwagę, nowych urządzeń usługi IoT Hub](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

## <a name="test-the-project"></a>Projekt testowy

Po uruchomieniu aplikacji przykładowej, Mxchip wysyła dane czujników za pośrednictwem sieci Wi-Fi do Akceleratory rozwiązań IoT. Aby wyświetlić wynik, wykonaj następujące kroki:

1. Przejdź do Twój akcelerator rozwiązań IoT, a następnie kliknij przycisk **pulpit NAWIGACYJNY**.

2. W konsoli akcelerator rozwiązań IoT zobaczysz swój status czujnika Mxchip. 

![Danych z czujników w akceleratorów rozwiązań IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

Po kliknięciu nazwy czujnika (az3166 usługi) zostanie otwarta karta, która po prawej stronie pulpitu nawigacyjnego, w którym można zobaczyć wykres mikroukładu MX czujników w czasie rzeczywistym.


## <a name="send-a-c2d-message"></a>Wyślij wiadomość C2D
Zdalne monitorowanie v2 umożliwia wywołania zdalnej metody na urządzeniu.
Przykładowy kod mikroukładu MX publikuje trzy metody, które widać w sekcji metodę po wybraniu czujnika.

![Mikroukład MX metody](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

Można zmienić kolor jednego z mikroukładu MX diod LED, przy użyciu metody "LedColor". Dla to zrobić, zaznacz pole wyboru, urządzenia i kliknij przycisk harmonogramu. 

![Mikroukład MX metody](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

Wybierz metodę o nazwie ChangeColor w menu rozwijanym, gdzie wszystkie metody są wyświetlane, wpisana nazwa i zastosować.

![Lista rozwijana MX mikroukładu](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

W ciągu kilku sekund fizycznych modułem MX należy zmienić kolor RGB prowadzone (poniżej a przycisk)

![Prowadziły mikroukładu MX](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz problemy, zapoznaj się [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub się z nami od następujące kanały:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Witryna Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak połączyć urządzenie Mxchip Akceleratory rozwiązań IoT i wizualizowanie danych z czujników, Oto zalecane kolejne kroki:

* [Omówienie akceleratorów rozwiązań IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Podłącz urządzenie z systemem zestawu deweloperskiego IoT Mxchip z aplikacją Microsoft IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
* [Zestaw dla deweloperów IoT](https://microsoft.github.io/azure-iot-developer-kit/)
