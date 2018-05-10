---
title: 'Zestaw deweloperski IoT do chmury: Połącz IoT MXChip zestaw deweloperski z Centrum IoT Azure | Dokumentacja firmy Microsoft'
description: W tym samouczku Dowiedz się, jak wysyłać stan czujników na AZ3166 zestaw deweloperski IoT do akcelerator rozwiązań monitorowania zdalnego IoT Azure.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: d3de8241b42aff63faaf04477a58c5564407ac8b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Zestaw deweloperski IoT MXChip nawiązania połączenia zdalnego monitorowania Azure IoT akcelerator rozwiązań

W tym samouczku Dowiedz się jak uruchomić przykładową aplikację na Twoje zestaw deweloperski, aby wysłać dane czujników do akcelerator rozwiązań sieci Azure IoT zdalnego monitorowania.

[Zestaw deweloperski IoT MXChip](https://aka.ms/iot-devkit) jest w jednym Arduino tablicy zgodny z czujników i sformatowanego urządzenia peryferyjne. Można utworzyć dla niego przy użyciu [rozszerzenia Visual Studio Code Arduino](https://aka.ms/arduino). I pochodzi z rosnącym [katalogu projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) prowadzące prototypu Internetu rzeczy (IoT) rozwiązania, które korzystać z usług Microsoft Azure.

## <a name="what-you-need"></a>Co jest potrzebne

Zakończ [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) do:

* Twoje zestaw deweloperski ma podłączony do sieci Wi-Fi
* Przygotowywanie środowiska deweloperskiego

Aktywna subskrypcja platformy Azure. Jeśli nie masz, możesz zarejestrować za pomocą jednej z tych dwóch metod:

* Aktywuj [bezpłatne 30-dniowej wersji próbnej konto Microsoft Azure](https://azure.microsoft.com/free/)
* Oświadczenie użytkownika [Azure środki](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) w przypadku subskrybentów MSDN lub Visual Studio

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Utwórz akcelerator rozwiązań monitorowania zdalnego IoT Azure

1. Przejdź do [lokacji acclerators rozwiązania Azure IoT](https://www.azureiotsuite.com/) i kliknij przycisk **Utwórz nowe rozwiązanie**.
  ![Wybierz typ akceleratora rozwiązania Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)
  > [!WARNING]
  > Domyślnie ten przykład tworzy Centrum IoT S2 po utworzeniu jednego akcelerator rozwiązań monitorowania zdalnego IoT. Jeśli w tym Centrum IoT nie jest używana z bardzo dużej liczby urządzeń, zdecydowanie zaleca się obniżyć go z S2 S1 i usunąć akcelerator rozwiązań monitorowania zdalnego IoT, aby powiązane Centrum IoT mogą być również usuwane, gdy nie są już potrzebne. 

2. Wybierz **monitorowania zdalnego**.

3. Wprowadź nazwę rozwiązanie, wybierz subskrypcję i region, a następnie kliknij **tworzenie rozwiązania**. Rozwiązanie może potrwać do obsługi administracyjnej.
  ![Tworzenie rozwiązania](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Po zakończeniu świadczenia kliknij **uruchamianie**. Niektóre symulowanego urządzenia są tworzone dla rozwiązania podczas procesu udostępniania. Kliknij przycisk **urządzeń** je wyewidencjonować. ![Pulpit nawigacyjny](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  ![konsoli](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Kliknij przycisk **dodać urządzenie**.

6. Kliknij przycisk **Dodawanie nowych** dla **urządzeń niestandardowych**.
  ![Dodaj nowe urządzenie](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Kliknij przycisk **Pozwól mi zdefiniować własny identyfikator urządzenia**, wprowadź `AZ3166`, a następnie kliknij przycisk **Utwórz**.
  ![Utwórz urządzenie o identyfikatorze](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Zanotuj **nazwy hosta Centrum IoT**i kliknij przycisk **gotowe**.

## <a name="open-the-remotemonitoring-sample"></a>Otwórz przykładowe RemoteMonitoring

1. Odłącz zestaw deweloperski z komputera, jeśli jest połączony.

2. Uruchom kod programu VS.

3. Zestaw deweloperski można połączyć się z komputerem. Kod VS automatycznie wykryje Twoje zestaw deweloperski i otwiera następujące strony:
  * Strona wprowadzenia zestaw deweloperski.
  * Przykłady Arduino: Przykłady praktyczne wprowadzenie zestaw deweloperski.

4. Po lewej stronie rozwiń **przykłady ARDUINO** przejdź do **przykłady MXCHIP AZ3166 > AzureIoT**i wybierz **RemoteMonitoring**. Otwiera nowe okno programu VS kodu z folderu projektu w nim.
  > [!NOTE]
  > Jeśli wystąpi aby zamknąć okienko, zostanie ponownie otwarty. Użyj `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) do otwarcia palety polecenia, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: przykłady**.

## <a name="provision-required-azure-services"></a>Zainicjuj obsługę wymaganych usług platformy Azure

W oknie rozwiązania, uruchom zadanie za pośrednictwem `Ctrl+P` (macOS: `Cmd+P`), wprowadzając `task cloud-provision` w polu tekstowym podana:

W terminalu VS kodu interaktywnego wiersza polecenia prowadzi użytkownika przez Inicjowanie obsługi administracyjnej wymaganych usług Azure:

![Udostępnianie zasobów platformy Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Tworzenie i przekazywanie kodu urządzenia

1. Użyj `Ctrl+P` (macOS: `Cmd + P`) i typ **zadań konfiguracji urządzenia połączenia**.

2. Terminal zapyta, czy chcesz użyć parametrów połączenia, które są pobierane z `task cloud-provision` kroku. Można również wejściowy ciąg połączenia własne urządzenia, klikając pozycję "Utwórz nowy..."

3. Terminal wyświetli monit o wprowadzenie trybu konfiguracji. Aby to zrobić, przytrzymaj naciśnięty przycisk A, a następnie wypychania, a następnie zwolnij przycisk resetowania. Na ekranie zostanie wyświetlony identyfikator zestaw deweloperski i "Konfiguracja".
  ![Ciąg wejściowy połączenia](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Po `task config-device-connection` gotowe, kliknij przycisk `F1` załadować polecenia kodzie VS i wybierz `Arduino: Upload`, następnie kod VS rozpoczyna się weryfikowanie i przekazywanie Arduino szkic: ![weryfikacji i przekazywanie szkicu Arduino](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

Zestaw deweloperski wykonuje ponowny rozruch i uruchamiania kodu.

## <a name="test-the-project"></a>Projekt testowy

Po uruchomieniu aplikacji przykładowej, zestaw deweloperski wysyła dane czujników za pośrednictwem sieci Wi-Fi do akcelerator rozwiązań sieci Azure IoT zdalnego monitorowania. Aby zobaczyć wynik, wykonaj następujące kroki:

1. Przejdź do akcelerator rozwiązań sieci Azure IoT zdalnego monitorowania, a następnie kliknij przycisk **pulpitu NAWIGACYJNEGO**.

2. W konsoli rozwiązanie monitorowania zdalnego pojawi się stan czujnik zestaw deweloperski.

![Dane czujników w akcelerator rozwiązań monitorowania zdalnego IoT Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Zmień identyfikator urządzenia

Identyfikator urządzenia w Centrum IoT można zmienić, wykonując [w tym przewodniku](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/). A jeśli chcesz zmienić zapisane na stałe **AZ3166** do dostosowania identyfikator urządzenia w kodzie. [W tym miejscu](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23) wiersza kodu, który można modyfikować.

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli wystąpią problemy, zapoznaj się [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub dotrzeć do nas z następujących kanałów:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Witryna Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz jak zestaw deweloperski urządzenie podłączone do sieci Azure IoT zdalne monitorowanie akcelerator rozwiązań i wizualizować dane czujników, w tym miejscu są Sugerowane następne kroki:

* [Omówienie akceleratorów rozwiązania IoT Azure](https://docs.microsoft.com/azure/iot-suite/)
* [Podłącz urządzenie z systemem zestaw deweloperski IoT MXChip do aplikacji Azure IoT centralnej](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
