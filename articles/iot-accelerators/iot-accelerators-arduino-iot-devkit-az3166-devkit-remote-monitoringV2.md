---
title: 'Zestaw deweloperski IoT do chmury: Połącz AZ3166 zestaw deweloperski IoT do zdalnego monitorowania IoT akcelerator rozwiązań | Dokumentacja firmy Microsoft'
description: W tym samouczku Dowiedz się, jak wysyłać stan czujników na AZ3166 zestaw deweloperski IoT do zdalnego monitorowania IoT akcelerator rozwiązań monitorowania i wizualizacji.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: isacabe
ms.openlocfilehash: 8aa4d660fbf785a4caf99bcdeddc86aa9929c50d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627743"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Nawiązać MXChip IoT zestaw deweloperski AZ3166 akcelerator rozwiązań monitorowania zdalnego IoT


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

W tym samouczku Dowiedz się jak uruchomić przykładową aplikację na Twoje zestaw deweloperski, aby wysłać dane czujników do Twojej akcelerator rozwiązań.

[Zestaw deweloperski IoT MXChip](https://aka.ms/iot-devkit) jest w jednym Arduino tablicy zgodny z czujników i sformatowanego urządzenia peryferyjne. Można utworzyć dla niego przy użyciu [rozszerzenia Visual Studio Code Arduino](https://aka.ms/arduino). I pochodzi z rosnącym [katalogu projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) prowadzące prototypu Internetu rzeczy (IoT) rozwiązania, które korzystać z usług Microsoft Azure.

## <a name="what-you-need"></a>Co jest potrzebne

Zakończ [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) do:

* Twoje zestaw deweloperski ma podłączony do sieci Wi-Fi
* Przygotowywanie środowiska deweloperskiego


## <a name="open-the-remotemonitoring-sample"></a>Otwórz przykładowe RemoteMonitoring

1. Odłącz zestaw deweloperski z komputera, jeśli jest połączony.

2. Uruchom kod programu VS.

3. Zestaw deweloperski można połączyć się z komputerem. Kod VS automatycznie wykryje Twoje zestaw deweloperski i otwiera następujące strony:
  * Strona wprowadzenia zestaw deweloperski.
  * Przykłady Arduino: Przykłady praktyczne wprowadzenie zestaw deweloperski.

4. Po lewej stronie rozwiń **przykłady ARDUINO** przejdź do **przykłady MXCHIP AZ3166 > AzureIoT**i wybierz **RemoteMonitoringv2**. Otwiera nowe okno programu VS kodu z folderu projektu w nim.

  ![Otwórz projekt monitorowania zdalnego](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)


  > [!NOTE]
  > Jeśli wystąpi aby zamknąć okienko, zostanie ponownie otwarty. Użyj `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) do otwarcia palety polecenia, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: przykłady**.

## <a name="add-a-new-physical-device"></a>Dodaj nowe urządzenie fizyczne

W portalu, przejdź do **urządzeń** sekcji, a następnie kliknij w **+ nowe urządzenie** przycisku. 

![Dodawanie nowego urządzenia](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device.png)

*Nowy formularz urządzenia* powinno być wypełnione.
1. Kliknij przycisk **fizycznych** w *typu urządzenia* sekcji.
2. Zdefiniuj identyfikator urządzenia (na przykład *MXChip* lub *AZ3166*).
3. Wybierz **automatycznego generowania kluczy** w *klucz uwierzytelniania* sekcji.
4. Kliknij przycisk *Zastosuj* przycisku.

![Dodawanie nowego formularza urządzenia](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-new-device-form.png)

Poczekaj, aż zakończy portalu inicjowania obsługi nowego urządzenia.

![Inicjowanie obsługi administracyjnej nowych urządzeń ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device-provisioning.png)


Następnie będzie można wyświetlić konfiguracji nowego urządzenia.
Kopiuj **ciąg połączenia** wygenerowany.

![Ciąg połączenia urządzenia](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-new-device-connstring.png)


Ten ciąg połączenia będą używane w następnej sekcji.





## <a name="build-and-upload-the-device-code"></a>Tworzenie i przekazywanie kodu urządzenia

Wróć do programu Visual Studio Code: 

1. Użyj `Ctrl+P` (macOS: `Cmd + P`) i typ **zadań konfiguracji urządzenia połączenia**.

  ![Wybierz subskrypcję platformy Azure i Centrum IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. Terminal zapyta, czy chcesz użyć ciągu połączenia urządzenia IoT, który chcesz użyć. Wybierz *tworzenia nowych*i wklej go.

  ![Wklej parametry połączenia](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. Czasami terminal monituje o wprowadzenie trybu konfiguracji. Aby to zrobić, przytrzymaj naciśnięty przycisk A, a następnie wypychania i zwolnij przycisk reset a następnie zwolnij przycisk A. Na ekranie zostanie wyświetlony identyfikator zestaw deweloperski i "Konfiguracja".

  ![Ekran zestaw deweloperski urządzenia](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > Parametry połączenia powinny być zapisywane w Schowka, gdy po ostatniej sekcji tego samouczka. Jeśli nie, należy przejść do portalu Azure i wyszukaj Centrum IoT monitorowania zdalnego grupy zasobów. Widać, Centrum IoT urządzeń podłączonych i skopiuj parametry połączenia urządzenia.

  ![Wyszukaj ciąg połączenia](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)


Teraz możesz zobaczyć nowe urządzenie fizyczne w kodzie VS sekcji "Azure IoT Hub urządzenia":

![Zwróć uwagę, nowego urządzenia Centrum IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

## <a name="test-the-project"></a>Projekt testowy

Po uruchomieniu aplikacji przykładowej, zestaw deweloperski wysyła dane czujników za pośrednictwem sieci Wi-Fi do akceleratorami rozwiązania IoT. Aby zobaczyć wynik, wykonaj następujące kroki:

1. Przejdź do Twojej akceleratora rozwiązania IoT, a następnie kliknij przycisk **pulpitu NAWIGACYJNEGO**.

2. W konsoli akceleratora rozwiązania IoT pojawi się stan czujnik zestaw deweloperski. 

![Dane czujników w akceleratorów rozwiązania IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

Po kliknięciu nazwy czujnika (AZ3166) po prawej stronie pulpitu nawigacyjnego, w którym można zobaczyć MX układ wykresu czujników w czasie rzeczywistym powoduje otwarcie karty.


## <a name="send-a-c2d-message"></a>Wyślij wiadomość C2D
Zdalne monitorowanie v2 umożliwia wywołanie metody zdalnego na urządzeniu.
Przykładowy kod mikroukładu MX publikuje trzy metody, które można sprawdzić w sekcji metodę po wybraniu czujnika.

![Metody MX mikroukładu](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

Można zmienić kolor LED mikroukładu MX przy użyciu metody "LedColor". Dla to zrobić, zaznacz pole wyboru obok urządzenia i kliknij przycisk harmonogram. 

![Metody MX mikroukładu](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

Wybierz metodę o nazwie ChangeColor na liście rozwijanej, gdzie wszystkie metody są wyświetlane, zapisać nazwę i zastosowanie.

![Lista rozwijana MX mikroukładu](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

W ciągu kilku sekund, sieci fizycznej mikroukładu MX należy zmienić kolor RGB doprowadziły (poniżej a przycisk)

![Spowodował mikroukładu MX](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)


## <a name="change-device-id"></a>Zmień identyfikator urządzenia

Identyfikator urządzenia w Centrum IoT można zmienić, wykonując [w tym przewodniku](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).


## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli wystąpią problemy, zapoznaj się [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub dotrzeć do nas z następujących kanałów:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Witryna Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz jak zestaw deweloperski urządzenie podłączone do akceleratorami rozwiązania IoT i wizualizować dane czujników, w tym miejscu są Sugerowane następne kroki:

* [Omówienie akceleratorów rozwiązania IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Podłącz urządzenie z systemem zestaw deweloperski IoT MXChip do aplikacji Microsoft IoT centralnego](https://docs.microsoft.com/en-us/microsoft-iot-central/howto-connect-devkit)
