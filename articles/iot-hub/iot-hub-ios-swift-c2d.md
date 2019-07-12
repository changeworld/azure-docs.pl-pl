---
title: Komunikaty z chmury do urządzenia z usługą Azure IoT Hub (iOS) | Dokumentacja firmy Microsoft
description: Jak wysyłanie komunikatów z chmury do urządzeń do urządzenia z usługi Azure IoT hub przy użyciu zestawów SDK usługi Azure IoT dla systemu iOS.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: 6bb95bf887837fffc4196bca8d761239ac430a1a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620175"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Wysyłanie komunikatów z chmury do urządzeń z usługą IoT Hub (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Usługa Azure IoT Hub to w pełni zarządzana usługa, która ułatwia włączanie bezpieczną i niezawodną komunikację dwukierunkową między milionami urządzeń i zapleczem rozwiązania. [Wysyłanie danych telemetrycznych z urządzenia do usługi IoT hub](quickstart-send-telemetry-ios.md) Przewodnik Szybki Start przedstawia sposób tworzenia Centrum IoT hub, aprowizować w nim tożsamości urządzenia i kodu aplikacji symulowanego urządzenia, która wysyła komunikaty z urządzenia do chmury.

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Z zapleczem rozwiązania wysyłanie komunikatów z chmury do urządzeń do pojedynczego urządzenia za pomocą usługi IoT Hub.

* Odbieranie komunikatów z chmury do urządzeń na urządzeniu.

* Z zapleczem rozwiązania, żądania potwierdzenia dostarczania (*opinii*) dla wiadomości wysyłanych do urządzenia z usługi IoT Hub.

Można znaleźć więcej informacji na temat komunikatów z chmury do urządzeń w [komunikatów części przewodnika dla deweloperów usługi IoT Hub](iot-hub-devguide-messaging.md).

Na końcu tego artykułu możesz uruchomić dwie Swift projektów systemu iOS:

* **Przykładowe urządzenia**, ta sama aplikacja utworzona w [wysyłanie danych telemetrycznych z urządzenia do usługi IoT hub](quickstart-send-telemetry-ios.md), który nawiązuje połączenie z Centrum IoT i odbiera komunikaty z chmury do urządzenia.

* **przykład usługi**, która jest wysyłana wiadomość chmury do urządzenia w aplikacji symulowanego urządzenia za pomocą usługi IoT Hub i odbiera jego potwierdzenia dostarczenia.

> [!NOTE]
> Usługa IoT Hub obsługuje zestaw SDK na wielu platformach i językach (w tym C, Java i Javascript) za pomocą zestawów SDK urządzeń Azure IoT. Aby uzyskać instrukcje krok po kroku dotyczące łączenia urządzenia do kodu w tym samouczku i ogólnie do usługi Azure IoT Hub, zobacz [Centrum deweloperów Azure IoT](https://www.azure.com/develop/iot).

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.)

* Aktywnym Centrum IoT na platformie Azure.

* Przykładowy kod z [przykładów dla platformy Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip).

* Najnowsza wersja środowiska [XCode](https://developer.apple.com/xcode/) korzystająca z najnowszej wersji zestawu SDK systemu iOS. Ten przewodnik Szybki start przetestowano przy użyciu środowiska XCode 9.3 i systemu iOS 11.3.

* Najnowsza wersja menedżera [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

## <a name="simulate-an-iot-device"></a>Symulowanie urządzenia usługi IoT

W tej sekcji można symulować urządzenie z systemem iOS Swift aplikacji na odbieranie komunikatów z chmury do urządzeń z usługi IoT hub. 

Jest to urządzenie próbki, utworzonego w artykule [wysyłanie danych telemetrycznych z urządzenia do usługi IoT hub](quickstart-send-telemetry-ios.md). Jeśli masz już, że działa, możesz pominąć tę sekcję.

### <a name="install-cocoapods"></a>Instalowanie zasobników CocoaPods

Zasobniki CocoaPods zarządzają zależnościami dla projektów systemu iOS korzystających z bibliotek innych firm.

W oknie terminalu przejdź do folderu Azure-IoT-Samples-iOS pobranego w ramach wymagań wstępnych. Następnie przejdź do przykładowego projektu:

```sh
cd quickstart/sample-device
```

Upewnij się, że środowisko XCode jest zamknięte, a następnie uruchom następujące polecenie, aby zainstalować zasobniki CocoaPods zadeklarowane w pliku **podfile**:

```sh
pod install
```

Oprócz instalacji zasobników wymaganych przez projekt polecenie instalacji tworzy także plik obszaru roboczego środowiska XCode, który jest już skonfigurowany do używania zasobników na potrzeby zależności.

### <a name="run-the-sample-device-application"></a>Uruchamianie przykładowej aplikacji urządzenia

1. Pobierz parametry połączenia dla Twojego urządzenia. Możesz skopiować te parametry z [witryny Azure portal](https://portal.azure.com) w bloku szczegółów urządzenia lub pobrać go za pomocą następującego polecenia interfejsu wiersza polecenia:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Otwórz przykładowy obszar roboczy w środowisku XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Rozwiń **MQTT Client Sample** projekt, a następnie folder o takiej samej nazwie.  

3. Otwórz plik **ViewController.swift** do edycji w środowisku XCode. 

4. Wyszukaj **connectionString** zmiennej i zaktualizuj wartość połączenie z urządzeniem ciąg skopiowane w pierwszym kroku.

5. Zapisz zmiany. 

6. Uruchom projekt w emulatorze urządzenia za pomocą przycisku **Skompiluj i uruchom** lub kombinacji klawiszy **Command + R**.

   ![Uruchamianie projektu](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="simulate-a-service-device"></a>Symulowanie urządzenia usługi

W tej sekcji można symulować drugiego urządzenia z systemem iOS przy użyciu Swift aplikacji, która wysyła komunikaty z chmury do urządzenia za pośrednictwem usługi IoT hub. Ta konfiguracja jest przydatne w scenariuszach IoT w przypadku, gdy znajduje się jeden dla telefonu iPhone lub iPad działa jako kontroler dla innych urządzeń z systemem iOS podłączone do usługi IoT hub.

### <a name="install-cocoapods"></a>Instalowanie zasobników CocoaPods

Zasobniki CocoaPods zarządzają zależnościami dla projektów systemu iOS korzystających z bibliotek innych firm.

Przejdź do folderu przykładów usługi Azure IoT z systemem iOS, pobrany w wymaganiach wstępnych. Następnie przejdź do przykładowego projektu usługi:

```sh
cd quickstart/sample-service
```

Upewnij się, że środowisko XCode jest zamknięte, a następnie uruchom następujące polecenie, aby zainstalować zasobniki CocoaPods zadeklarowane w pliku **podfile**:

```sh
pod install
```

Oprócz instalacji zasobników wymaganych przez projekt polecenie instalacji tworzy także plik obszaru roboczego środowiska XCode, który jest już skonfigurowany do używania zasobników na potrzeby zależności.

### <a name="run-the-sample-service-application"></a>Uruchamianie przykładowej aplikacji usługi

1. Pobierz parametry połączenia usługi dla Centrum IoT. Możesz skopiować te parametry z [witryny Azure portal](https://portal.azure.com) z **iothubowner** zasad w **zasady dostępu współdzielonego** bloku lub pobrać go za pomocą następującego polecenia interfejsu wiersza polecenia:  

    ```azurecli-interactive
    az iot hub show-connection-string --name {YourIoTHubName} --output table
    ```

2. Otwórz przykładowy obszar roboczy w środowisku XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

3. Rozwiń **AzureIoTServiceSample** projektu, a następnie rozwiń folder o takiej samej nazwie.  

4. Otwórz plik **ViewController.swift** do edycji w środowisku XCode. 

5. Wyszukaj **connectionString** zmiennej i zaktualizuj wartość za pomocą parametrów połączenia usługi, który został wcześniej skopiowany.

6. Zapisz zmiany.

7. W środowisku Xcode należy zmienić ustawienia emulatora na urządzeniu iOS innego niż użyte do uruchomienia urządzenia IoT. Środowisko XCode nie można uruchomić wiele emulatorów tego samego typu.

   ![Zmienianie urządzenia emulatora](media/iot-hub-ios-swift-c2d/change-device.png)

8. Uruchom projekt w emulatorze urządzenia za pomocą **kompilowanie i uruchamianie** przycisk lub kombinacji klawiszy **Command + r**.

   ![Uruchamianie projektu](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie komunikatów chmura urządzenie

Teraz można przystąpić do dwóch aplikacji umożliwia wysyłanie i odbieranie komunikatów z chmury do urządzeń.

1. W **przykładowych aplikacji systemu iOS** kliknij aplikację działającą w symulowanych urządzeń IoT **Start**. Aplikacja rozpoczyna wysyłanie komunikatów z urządzenia do chmury, ale również zaczyna nasłuchiwanie komunikatów z chmury do urządzeń.

   ![Wyświetl przykładowej aplikacji urządzenia IoT](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. W **przykład klienta usługi IoTHub** aplikacji uruchomionej na urządzenie symulowane usługi, wprowadź identyfikator dla urządzenia IoT, które chcesz wysłać wiadomość. 

3. Zapisz komunikat w postaci zwykłego tekstu, a następnie kliknij przycisk **wysyłania**.

    Kilka akcji się tak zdarzyć, zaraz po jego kliknięciu wysyłania. Przykład usługi wysyła komunikat do usługi IoT hub, która aplikacja ma dostęp do ze względu na połączenia z usługą ciągu, pod warunkiem. Centrum IoT hub sprawdza, czy identyfikator urządzenia, wysyła komunikat do urządzenia docelowego i wysyła potwierdzenie do urządzenia źródłowego. Jest uruchomiona na symulowanym urządzeniu IoT Aplikacja sprawdza, czy komunikaty z usługi IoT Hub i drukuje tekst od najnowszego na ekranie.

    Dane wyjściowe powinny wyglądać następująco:

   ![Wyświetlanie komunikatów z chmury do urządzeń](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wysyłania i odbierania komunikatów z chmury do urządzeń.

Aby wyświetlić przykłady kompletne rozwiązania end-to-end, które używają usługi IoT Hub, zobacz [akceleratorów rozwiązań IoT Azure](https://azure.microsoft.com/documentation/suites/iot-suite/) dokumentacji.

Aby dowiedzieć się więcej na temat opracowywania rozwiązań usługi IoT Hub, zobacz [usługi IoT Hub — przewodnik dewelopera](iot-hub-devguide.md).
