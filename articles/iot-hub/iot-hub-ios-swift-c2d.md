---
title: Komunikaty chmury do urządzenia z Centrum IoT Azure (iOS) | Dokumentacja firmy Microsoft
description: Jak wysyłać wiadomości chmury do urządzenia na urządzeniu z Centrum Azure IoT przy użyciu zestawów SDK IoT Azure dla systemu iOS.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: 032412c329e79ec671f59a049da7d8ddc0b9dd08
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Wysyłanie komunikatów chmury do urządzenia z Centrum IoT (iOS)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


Centrum IoT Azure jest w pełni zarządzaną usługę, która umożliwia włączanie i niezawodności komunikację dwukierunkową między milionów urządzeń i zaplecze rozwiązania. [wysyłania danych telemetrycznych z urządzenia do Centrum IoT] artykule przedstawiono sposób tworzenia Centrum IoT, udostępnić tożsamość urządzenia w nim i kodem aplikacji symulowane urządzenie, który wysyła wiadomości urządzenia do chmury.

W tym artykule opisano sposób do:

* Z sieci zaplecza rozwiązania wysyłać chmury do urządzenia do jednego urządzenia za pośrednictwem Centrum IoT.
* Komunikaty chmury do urządzenia na urządzeniu.
* Z sieci zaplecza rozwiązania, żądania potwierdzenia dostarczenia (*opinii*) dla wiadomości wysyłanych do urządzenia z Centrum IoT.

Można znaleźć więcej informacji na temat wiadomości chmury do urządzenia w [Centrum IoT — przewodnik dewelopera][IoT Hub developer guide - C2D].

Na końcu tego artykułu możesz uruchomić Swift dwa projekty dla systemu iOS:

* **Przykładowe urządzenia**, tej samej aplikacji utworzonych w [wysyłania danych telemetrycznych z urządzenia do Centrum IoT], która łączy się z Centrum IoT i odbiera komunikaty chmury do urządzenia.
* **Przykładowe usługi**, który wysyła komunikat chmury do urządzenia do aplikacji symulowane urządzenie za pomocą Centrum IoT i następnie odbiera jego potwierdzenie dostarczenia.

> [!NOTE]
> Centrum IoT obsługuje zestawu SDK dla wielu platform urządzeń i języków (w tym C, Java i Javascript) za pośrednictwem zestawy SDK urządzenia Azure IoT. Aby uzyskać instrukcje krok po kroku dotyczące sposobu Podłącz urządzenie do kodu w tym samouczku i zwykle z Centrum IoT Azure, zobacz [Azure IoT Developer Center].

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

- Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).
- Aktywnym Centrum IoT na platformie Azure. 
- Przykładowy kod z [przykładów dla platformy Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip) .
- Najnowszą wersję [XCode](https://developer.apple.com/xcode/), korzystania z najnowszej wersji zestawu SDK dla systemu iOS. Ta opcja szybkiego startu była testowana przy XCode 9.3 i iOS 11.3.
- Najnowszą wersję [programu CocoaPods](https://guides.cocoapods.org/using/getting-started.html).


## <a name="simulate-an-iot-device"></a>Symulacji urządzenia IoT
W tej sekcji można symulować Swift aplikacji na odbieranie komunikatów chmury do urządzenia z Centrum IoT urządzenia z systemem iOS. 

To urządzenie próbki próbki utworzonego w artykule [wysyłania danych telemetrycznych z urządzenia do Centrum IoT]. Jeśli masz już, że działa, możesz pominąć tę sekcję.

### <a name="install-cocoapods"></a>Instalowanie programu CocoaPods

Programu CocoaPods Zarządzanie zależności dla projektów dla systemu iOS, korzystających z bibliotek innych firm.

W oknie terminalu przejdź do folderu Azure IoT — przykłady-systemu iOS pobranego w wymaganiach wstępnych. Następnie przejdź do przykładowy projekt:

```sh
cd quickstart/sample-device
```

Upewnij się, że środowisko XCode jest zamknięty, a następnie uruchom następujące polecenie, aby zainstalować programu CocoaPods, które są zadeklarowane w **podfile** pliku:

```sh
pod install
```

Wraz z instalacją stanowiskami wymagane dla projektu polecenia instalacji utworzony plik obszaru roboczego XCode, który jest już skonfigurowana do używania stanowiskami zależności. 

### <a name="run-the-sample-device-application"></a>Uruchom przykładową aplikację urządzenia 

1. Pobrać parametry połączenia dla danego urządzenia. Skopiować te parametry z portalu Azure w bloku szczegóły urządzeń lub pobrać go za pomocą następującego polecenia interfejsu wiersza polecenia: 

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Otwórz obszar roboczy próbki w środowisku XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Rozwiń węzeł **MQTT klienta — przykład** projektu, a następnie folder o takiej samej nazwie.  
3. Otwórz **ViewController.swift** do edycji w środowisku XCode. 
4. Wyszukaj **connectionString** zmienną i zaktualizuj tę wartość z połączenie z urządzeniem ciągu skopiowane w pierwszym kroku.
5. Zapisz zmiany. 
6. Uruchom projekt w emulatorze urządzenia z **skompilować i uruchomić** przycisku lub pole kombi klucza **polecenia + r**. 

   ![Uruchom projekt](media/quickstart-send-telemetry-ios/run-sample.png)


## <a name="simulate-a-service-device"></a>Symulacji urządzenia usługi

W tej sekcji można symulować drugiego urządzenia z systemem iOS z aplikacją Swift, który wysyła wiadomości chmury do urządzenia za pośrednictwem Centrum IoT. Ta konfiguracja jest przydatne w scenariuszach IoT w przypadku, gdy istnieje jeden iPhone lub iPad działa jako kontroler dla innych urządzeń z systemem iOS są połączone z Centrum IoT. 

### <a name="install-cocoapods"></a>Instalowanie programu CocoaPods

Programu CocoaPods Zarządzanie zależności dla projektów dla systemu iOS, korzystających z bibliotek innych firm.

Przejdź do folderu Przykłady Azure IoT z systemem iOS, pobranego w wymaganiach wstępnych. Następnie przejdź do przykładowy projekt usługi:

```sh
cd quickstart/sample-service
```

Upewnij się, że środowisko XCode jest zamknięty, a następnie uruchom następujące polecenie, aby zainstalować programu CocoaPods, które są zadeklarowane w **podfile** pliku:

```sh
pod install
```

Wraz z instalacją stanowiskami wymagane dla projektu polecenia instalacji utworzony plik obszaru roboczego XCode, który jest już skonfigurowana do używania stanowiskami zależności.

### <a name="run-the-sample-service-application"></a>Uruchom przykładową aplikację usługi

1. Pobrać parametry połączenia usługi Centrum IoT. Możesz skopiować te parametry z portalu Azure z **iothubowner** zasad w **zasady dostępu współużytkowanego** bloku lub pobrać go za pomocą następującego polecenia interfejsu wiersza polecenia:  

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

2. Otwórz obszar roboczy próbki w środowisku XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

3. Rozwiń węzeł **AzureIoTServiceSample** projektu, a następnie rozwiń folder o takiej samej nazwie.  
4. Otwórz **ViewController.swift** do edycji w środowisku XCode. 
5. Wyszukaj **connectionString** zmienną i zaktualizuj tę wartość przy użyciu parametrów połączenia usługi, wcześniej skopiowany.
6. Zapisz zmiany. 
7. W programie Xcode Zmień ustawienia emulatora urządzenia iOS inną niż użyta do uruchomienia urządzenia IoT. Środowisko XCode nie można uruchomić wiele emulatory tego samego typu. 

   ![Zmienianie urządzenia emulatora](media/iot-hub-ios-swift-c2d/change-device.png)

8. Uruchom projekt w emulatorze urządzenia z **skompilować i uruchomić** przycisku lub pole kombi klucza **polecenia + r**. 

   ![Uruchom projekt](media/iot-hub-ios-swift-c2d/run-app.png)


## <a name="send-a-cloud-to-device-message"></a>Wyślij wiadomość chmury do urządzenia
Teraz można przystąpić do dwóch aplikacji umożliwia wysyłanie i odbieranie wiadomości chmury do urządzenia.

1. W **przykładowej aplikacji systemu iOS** aplikacji uruchomionej na symulowane urządzenie IoT kliknij **Start**. Aplikacja rozpoczyna wysyłanie wiadomości urządzenia do chmury, ale również rozpoczyna nasłuchiwanie wiadomości chmury do urządzenia. 

   ![Wyświetlanie przykładowej aplikacji urządzenia IoT](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. W **przykład klienta usługi Centrum IoTHub** aplikacji uruchomionej na urządzeniu symulowane usługi, wprowadź identyfikator urządzenia IoT, który ma do wysłania do. 
3. Zapisz komunikat w postaci zwykłego tekstu, a następnie kliknij przycisk **wysyłania**. 

Kilka akcji się tak zdarzyć, natychmiast po kliknięciu przycisku Wyślij. Przykład usługi wysyła wiadomość do użytkownika IoT koncentratora, w którym aplikacja ma dostęp do ze względu na połączenia z usługą string, które pod warunkiem. Centrum IoT sprawdza identyfikator urządzenia, wysyła wiadomość do urządzenia docelowego i wysyła potwierdzenie do urządzenia źródłowego. Aplikacja uruchomiona na urządzeniu IoT symulowane sprawdza, czy komunikaty z Centrum IoT i wyświetla tekst z najnowszego na ekranie.

Dane wyjściowe powinna wyglądać następująco:

   ![Wyświetlanie komunikatów chmury do urządzenia](media/iot-hub-ios-swift-c2d/view-c2d.png)


## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób wysyłania i odbierania wiadomości chmury do urządzenia. 

Aby zapoznać się przykładem kompletnych rozwiązań end-to-end korzystających z Centrum IoT, zobacz [pakiet IoT Azure].

Aby dowiedzieć się więcej na temat tworzenia rozwiązań z Centrum IoT, zobacz [Centrum IoT — przewodnik dewelopera].

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[wysyłania danych telemetrycznych z urządzenia do Centrum IoT]: quickstart-send-telemetry-ios.md

[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Centrum IoT — przewodnik dewelopera]: iot-hub-devguide.md
[Azure IoT Developer Center]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure portal]: https://portal.azure.com
[pakiet IoT Azure]: https://azure.microsoft.com/documentation/suites/iot-suite/
