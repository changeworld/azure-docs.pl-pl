---
title: Łączenie zestawu MXChip IoT DevKit z zdalnym monitorowaniem usługi Azure IoT Hub
description: W tym samouczku dowiesz się, jak wysłać stan czujników w IoT DevKit AZ3166 do akceleratora rozwiązań zdalnego monitorowania usługi Azure IoT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 598e361949b000724645c841910b1682a7bbb1a3
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258460"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Podłączanie zestawu MXChip IoT DevKit do akceleratora rozwiązań zdalnego monitorowania usługi Azure IoT

W tym samouczku dowiesz się, jak uruchomić przykładową aplikację na devkit, aby wysłać dane czujnika do akceleratora rozwiązania zdalnego monitorowania usługi Azure IoT.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) to płyta kompatybilna z all-in-one Arduino z bogatymi urządzeniami peryferyjnymi i czujnikami. Można opracować dla niego za pomocą [rozszerzenia kodu Visual Studio dla Arduino](https://aka.ms/arduino). I jest wyposażony w rosnący [katalog projektów,](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) który poprowadzi Cię do prototypu rozwiązań Internetu rzeczy (IoT), które korzystają z usług platformy Microsoft Azure.

## <a name="what-you-need"></a>Co jest potrzebne

Ukończ [przewodnik Wprowadzenie](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) do:

* Podłącz devkit do sieci Wi-Fi
* Przygotowywanie środowiska deweloperskiego

Aktywna subskrypcja platformy Azure. Jeśli go nie masz, możesz zarejestrować się za pomocą jednej z tych dwóch metod:

* Aktywowanie [bezpłatnego 30-dniowego konta microsoft azure podczas próby](https://azure.microsoft.com/free/)

* Ubiegaj się o [kredyt na platformę Azure,](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) jeśli jesteś subskrybentem usługi MSDN lub Visual Studio

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Tworzenie akceleratora rozwiązań zdalnego monitorowania usługi Azure IoT

1. Przejdź do [witryny akceleratorów rozwiązań IoT platformy Azure](https://www.azureiotsolutions.com/) i kliknij pozycję **Utwórz nowe rozwiązanie**.

   ![Wybierz typ akceleratora rozwiązania Usługi Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Domyślnie ten przykład tworzy Centrum IoT S2 po tym, jak utworzy jeden akcelerator rozwiązań zdalnego monitorowania IoT. Jeśli ten koncentrator IoT nie jest używany z ogromną liczbą urządzeń, zdecydowanie zalecamy obniżenie jego poziomu z S2 do S1 i usunięcie akceleratora rozwiązań do zdalnego monitorowania IoT, dzięki czemu można również usunąć powiązane centrum IoT Hub, gdy nie jest już potrzebny. 

2. Wybierz **opcję Zdalne monitorowanie**.

3. Wprowadź nazwę rozwiązania, wybierz subskrypcję i region, a następnie kliknij przycisk **Utwórz rozwiązanie**. Rozwiązanie może zająć trochę czasu, aby być aprowizacji.
  
   ![Tworzenie rozwiązania](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Po zakończeniu inicjowania obsługi administracyjnej kliknij przycisk **Uruchom**. Niektóre symulowane urządzenia są tworzone dla rozwiązania podczas procesu inicjowania obsługi administracyjnej. Kliknij **pozycję URZĄDZENIA,** aby je sprawdzić.

   ![Pulpit nawigacyjny](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Konsola](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Kliknij **pozycję DODAJ URZĄDZENIE**.

6. Kliknij **pozycję Dodaj nowy** dla urządzenia **niestandardowego**.
  
   ![Dodawanie nowego urządzenia](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Kliknij **pozycję Pozwól mi zdefiniować własny identyfikator urządzenia**, wprowadź `AZ3166`, a następnie kliknij przycisk **Utwórz**.
  
   ![Tworzenie urządzenia z identyfikatorem](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Zanotuj nazwę **hosta usługi IoT Hub**i kliknij przycisk **Gotowe**.

## <a name="open-the-remotemonitoring-sample"></a>Otwórz przykład zdalnego monitorowania

1. Odłącz devkit od komputera, jeśli jest podłączony.

2. Uruchom program VS Code.

3. Podłącz devkit do komputera. Program VS Code automatycznie wykrywa devkit i otwiera następujące strony:

   * Strona wprowadzająca DevKit.
   * Przykłady Arduino: Praktyczne przykłady, aby rozpocząć pracę z DevKit.

4. Rozwiń sekcję **ARDUINO EXAMPLES** po lewej stronie, przejdź do **sekcji Przykłady dla programu MXCHIP AZ3166 > AzureIoT**i wybierz **opcję RemoteMonitoring**. Otwiera nowe okno kodu PROGRAMU VS z folderem projektu w nim.

   > [!NOTE]
   > Jeśli zdarzy ci się zamknąć okienko, możesz je ponownie otworzyć. Użyj `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`), aby otworzyć paletę poleceń, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: Przykłady**.

## <a name="provision-required-azure-services"></a>Aprowizuj wymagane usługi platformy Azure

W oknie rozwiązania uruchom zadanie `Ctrl+P` za pośrednictwem `Cmd+P`(macOS: ), wprowadzając `task cloud-provision` w podanym polu tekstowym.

W terminalu kodu VS interaktywny wiersz polecenia prowadzi użytkownika przez inicjowanie obsługi administracyjnej wymaganych usług platformy Azure.

![Aprowizuj zasoby platformy Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Tworzenie i przesyłanie kodu urządzenia

1. Użyj `Ctrl+P` (macOS: `Cmd + P`) i wpisz **polecenie konfigur-device-connection**.

2. Terminal pyta, czy chcesz użyć ciągu połączenia, który `task cloud-provision` pobiera z kroku. Możesz również wprowadzić własny ciąg połączenia urządzenia, klikając przycisk "Utwórz nowy...".

3. Terminal wyświetli monit o przełączenie do trybu konfiguracji. Aby to zrobić, przytrzymaj przycisk A, a następnie naciśnij i zwolnij przycisk resetowania. Na ekranie są wyświetlane identyfikatory DevKit i "Konfiguracja".

   ![Wejściowy ciąg połączenia](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Po `task config-device-connection` zakończeniu kliknij, `F1` aby załadować polecenia `Arduino: Upload`VS Code i wybierz opcję . VS Code rozpoczyna weryfikację i przesłanie szkicu Arduino.
  
   ![Weryfikacja i przesyłanie szkicu Arduino](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

DevKit uruchamia się ponownie i uruchamia kod.

## <a name="test-the-project"></a>Testowanie projektu

Po uruchomieniu przykładowej aplikacji DevKit wysyła dane czujnika za pomocą sieci Wi-Fi do akceleratora rozwiązań zdalnego monitorowania usługi Azure IoT. Aby wyświetlić wynik, wykonaj następujące kroki:

1. Przejdź do akceleratora rozwiązań zdalnego monitorowania usługi Azure IoT i kliknij przycisk **DASHBOARD**.

2. Na konsoli rozwiązania zdalnego monitorowania zostanie wyświetlony stan czujnika DevKit.

   ![Dane czujników w akceleratorze rozwiązań zdalnego monitorowania IoT platformy Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Zmienianie identyfikatora urządzenia

Jeśli chcesz zmienić kodowany na stałe **AZ3166** na niestandardowy identyfikator urządzenia w kodzie, zmodyfikuj wiersz kodu wyświetlany w [przykładzie zdalnego monitorowania](../iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoring-v2.md).

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz problemy, zapoznaj się [z często zadawanymi pytaniami dotyczącymi zestawu programistów IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub skontaktuj się z nami za pomocą następujących kanałów:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiesz się, jak podłączyć urządzenie DevKit do akceleratora rozwiązania zdalnego monitorowania usługi Azure IoT i wizualizować dane z czujników, oto sugerowane następne kroki:

* [Omówienie akceleratorów rozwiązań IoT platformy Azure](https://docs.microsoft.com/azure/iot-suite/)

* [Łączenie urządzenia MXChip IoT DevKit z aplikacją Azure IoT Central](/azure/iot-central/core/howto-connect-devkit)

* [Zestaw deweloperski IoT](https://microsoft.github.io/azure-iot-developer-kit/) 
