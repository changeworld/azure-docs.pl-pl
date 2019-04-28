---
title: IoT Mxchip z chmurą — nawiązać połączenie z IoT Mxchip zestawu deweloperskiego usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: W tym samouczku Dowiedz się, jak wysyłać na DevKit az3166 usługi IoT akcelerator rozwiązań Azure IoT zdalne monitorowanie stanu czujników.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: ae8dc263e08528c6e3b3bae8c779162c96d51f43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61324627"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Łączenie zestawu deweloperskiego IoT Mxchip do zdalnego monitorowania usługi Azure IoT akceleratora rozwiązań

W tym samouczku dowiesz się, jak uruchomić przykładową aplikację w Twojej Mxchip wysyłać danych z czujników Twój akcelerator rozwiązań Azure IoT zdalnego monitorowania.

[Zestawu deweloperskiego IoT Mxchip](https://aka.ms/iot-devkit) to w jednym Arduino zgodne z czujników i urządzeń peryferyjnych sformatowanego. Możesz tworzyć w ramach [rozszerzenie programu Visual Studio Code dla Arduino](https://aka.ms/arduino). I pochodzi ona z rosnącej [katalogu projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) przeprowadzenie Cię prototypu rozwiązania Internetu rzeczy (IoT), które korzystać z usług Microsoft Azure.

## <a name="what-you-need"></a>Co jest potrzebne

Zakończ [przewodnik wprowadzenie](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) do:

* Twoje Mxchip nawiązano połączenie z sieci Wi-Fi
* Przygotowywanie środowiska deweloperskiego

Aktywna subskrypcja platformy Azure. Jeśli nie masz, możesz zarejestrować się przy użyciu jednej z tych dwóch metod:

* Aktywuj [bezpłatnej 30-dniowej wersji próbnej konta systemu Microsoft Azure](https://azure.microsoft.com/free/)

* Oświadczenie użytkownika [platformy Azure w wysokości](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) Jeśli jesteś subskrybentem MSDN lub Visual Studio

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Utwórz akcelerator rozwiązań Azure IoT zdalnego monitorowania

1. Przejdź do [lokacji akceleratorów rozwiązań Azure IoT](https://www.azureiotsolutions.com/) i kliknij przycisk **Utwórz nowe rozwiązanie**.

   ![Wybierz typ akceleratora rozwiązań Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Domyślnie ten przykład umożliwia utworzenie Centrum IoT Hub S2 po utworzeniu jednego akcelerator rozwiązań IoT zdalnego monitorowania. Jeśli tego Centrum IoT hub nie jest używany z ogromną liczbą urządzeń, zdecydowanie zalecamy obniżyć jej z S2 do S1 i Usuń akcelerator rozwiązań IoT zdalne monitorowanie, więc pokrewne usługi IoT Hub można również zostaną usunięte, gdy nie są już potrzebne. 

2. Wybierz **zdalne monitorowanie**.

3. Wprowadź nazwę rozwiązania, wybierz region i subskrypcję, a następnie kliknij **tworzenie rozwiązania**. Rozwiązanie może potrwać do zainicjowania obsługi administracyjnej.
  
   ![Utwórz rozwiązanie](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Po zainicjowaniu obsługi administracyjnej zostanie zakończone, kliknij przycisk **Uruchom**. Niektóre symulowanych urządzeń są tworzone dla rozwiązania podczas procesu inicjowania obsługi administracyjnej. Kliknij przycisk **urządzeń** je wyewidencjonować.

   ![Pulpit nawigacyjny](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Konsola](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Kliknij przycisk **Dodaj urządzenie**.

6. Kliknij przycisk **Dodaj nowy** dla **urządzeń niestandardowych**.
  
   ![Dodawanie nowego urządzenia](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Kliknij przycisk **Pozwól mi zdefiniować własny identyfikator urządzenia**, wprowadź `AZ3166`, a następnie kliknij przycisk **Utwórz**.
  
   ![Tworzenie urządzenia o identyfikatorze](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Zwróć uwagę na **hosta Centrum IoT**i kliknij przycisk **gotowe**.

## <a name="open-the-remotemonitoring-sample"></a>Otwórz przykładową RemoteMonitoring

1. Rozłącz Mxchip z tego komputera, jeśli jest ona połączona.

2. Uruchom program VS Code.

3. Podłącz Mxchip do komputera. Program VS Code automatycznie wykrywa swoje Mxchip i otwiera następujące strony:

   * Strona wprowadzenia Mxchip.
   * Przykłady Arduino: Praktyczne przykłady wprowadzenie Mxchip.

4. Po lewej stronie rozwiń **przykłady ARDUINO** sekcji, przejdź do **przykłady zestawu DEWELOPERSKIEGO az3166 usługi > AzureIoT**i wybierz **RemoteMonitoring**. Nowe okno programu VS Code zostanie otwarty z folderu projektu w nim.

   > [!NOTE]
   > Jeśli występuje Zamknij okienko zostanie ponownie otwarty. Użyj `Ctrl+Shift+P` (z systemem macOS: `Cmd+Shift+P`) aby otworzyć paletę poleceń, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: Przykłady**.

## <a name="provision-required-azure-services"></a>Provision wymagane usługi platformy Azure

W oknie rozwiązania należy uruchomić zadanie przy użyciu `Ctrl+P` (z systemem macOS: `Cmd+P`), wprowadzając `task cloud-provision` w polu tekstowym podana.

W terminalu programu VS Code interaktywne wiersza polecenia przeprowadzi Cię przez Inicjowanie obsługi administracyjnej wymaganych usług platformy Azure.

![Aprowizowanie zasobów platformy Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Tworzenie i przekazywanie kodu urządzenia

1. Użyj `Ctrl+P` (z systemem macOS: `Cmd + P`) i typ **zadań konfiguracji urządzenia połączeń**.

2. Naciśnij w terminalu pyta, czy chcesz użyć parametrów połączenia, które pobiera z `task cloud-provision` kroku. Można również wprowadź własne parametry połączenia urządzenia, klikając przycisk "Utwórz nową..."

3. Naciśnij w terminalu wyświetli monit o wprowadzenie tryb konfiguracji. Aby to zrobić, przytrzymaj naciśnięty przycisk A, a następnie wypychania, a następnie zwolnij przycisk resetowania. Na ekranie zostanie wyświetlony identyfikator Mxchip i "Konfiguracja".

   ![Parametry połączenia danych wejściowych](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Po `task config-device-connection` zostanie zakończone, kliknij przycisk `F1` ładować poleceń programu VS Code i wybierz `Arduino: Upload`. Weryfikowanie i przekazywanie szkic Arduino, rozpoczyna się programu VS Code.
  
   ![Weryfikacja i przekazywania szkic Arduino](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

Mxchip rozruchu i uruchamiania kodu.

## <a name="test-the-project"></a>Projekt testowy

Po uruchomieniu aplikacji przykładowej, Mxchip wysyła dane czujników za pośrednictwem sieci Wi-Fi na Twój akcelerator rozwiązań Azure IoT zdalnego monitorowania. Aby wyświetlić wynik, wykonaj następujące kroki:

1. Przejdź do Twój akcelerator rozwiązań zdalnego monitorowania usługi Azure IoT, a następnie kliknij przycisk **pulpit NAWIGACYJNY**.

2. W konsoli rozwiązania monitorowania zdalnego zobaczysz swój status czujnika Mxchip.

   ![Danych z czujników w akcelerator rozwiązań Azure IoT zdalnego monitorowania](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Zmiana Identyfikatora urządzenia

Jeśli chcesz zmienić zapisane na stałe **az3166 usługi** ID urządzenia niestandardowe w kodzie, należy zmodyfikować wiersza kod wyświetlany w [zdalnego monitorowania przykład](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23).

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz problemy, zapoznaj się [kit dla deweloperów IoT — często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub skontaktowanie się z nami za pomocą następujących kanałów:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak połączyć urządzenie Mxchip Twój akcelerator rozwiązań Azure IoT zdalne monitorowanie i wizualizowanie danych z czujników, Oto zalecane kolejne kroki:

* [Omówienie akceleratorów rozwiązań w usłudze Azure IoT](https://docs.microsoft.com/azure/iot-suite/)

* [Podłącz urządzenie z systemem zestawu deweloperskiego IoT Mxchip z aplikacją usługi Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)

* [Zestaw dla deweloperów IoT](https://microsoft.github.io/azure-iot-developer-kit/) 
