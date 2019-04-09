---
title: Wysyłanie danych telemetrycznych do usługi Azure IoT Hub — Szybki start | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz przykładową aplikację systemu iOS wysyłającą symulowane dane telemetryczne do centrum IoT oraz odczytującą dane telemetryczne z centrum IoT na potrzeby przetwarzania w chmurze.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/03/2019
ms.openlocfilehash: a3f72c2a55acfc53d7d8a07bd10250c6ed6102ea
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006902"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-ios"></a>Szybki start: Wysyłanie danych telemetrycznych z urządzenia do centrum IoT (iOS)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym artykule opisano wysyłanie danych telemetrycznych z aplikacji urządzenia symulowanego do usługi IoT Hub, a następnie wyświetlanie tych danych z poziomu aplikacji zaplecza.

W tym artykule używana jest wstępnie napisana aplikacja języka Swift wysyłająca dane telemetryczne oraz narzędzie interfejsu wiersza polecenia odczytujące dane telemetryczne z usługi IoT Hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz przykładowy kod z [przykładów dla platformy Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)
- Najnowsza wersja środowiska [XCode](https://developer.apple.com/xcode/) korzystająca z najnowszej wersji zestawu SDK systemu iOS. Ten przewodnik Szybki Start została przetestowana XCode 10.2 i iOS 12.2.
- Najnowsza wersja menedżera [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).
- Uruchom następujące polecenie, aby dodać rozszerzenia usługi Microsoft Azure IoT dla interfejsu wiersza polecenia platformy Azure do swojego wystąpienia usługi Cloud Shell. Rozszerzenia IOT dodaje określone polecenia usługi IoT Hub, IoT Edge i usługi aprowizacji urządzeń IoT (DPS) do wiersza polecenia platformy Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w usłudze Azure Cloud Shell do tworzenia tożsamości urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **myiOSdevice**: jest to nazwa nadana dla zarejestrowanego urządzenia. Użyj myiOSdevice w pokazany sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz również używać tej nazwy w tym artykule oraz zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name YourIoTHubName --device-id myiOSdevice
   ```

1. Uruchom następujące polecenie, aby uzyskać _parametry połączenia urządzenia_ dla urządzenia, które właśnie zostało zarejestrowane:

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id myiOSdevice --output table
   ```

   Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

## <a name="send-simulated-telemetry"></a>Wysyłanie symulowanych danych telemetrycznych

Przykładowa aplikacja jest uruchamiana na urządzeniu z systemem iOS, które łączy się z punktem końcowym specyficznym dla urządzenia w centrum IoT i wysyła symulowane dane telemetryczne dotyczące temperatury oraz wilgotności. 

### <a name="install-cocoapods"></a>Instalowanie zasobników CocoaPods

Zasobniki CocoaPods zarządzają zależnościami dla projektów systemu iOS korzystających z bibliotek innych firm.

W lokalnym oknie terminalu przejdź do folderu Azure-IoT-Samples-iOS pobranego w ramach wymagań wstępnych. Następnie przejdź do przykładowego projektu:

```sh
cd quickstart/sample-device
```

Upewnij się, że środowisko XCode jest zamknięte, a następnie uruchom następujące polecenie, aby zainstalować zasobniki CocoaPods zadeklarowane w pliku **podfile**:

```sh
pod install
```

Oprócz instalacji zasobników wymaganych przez projekt polecenie instalacji tworzy także plik obszaru roboczego środowiska XCode, który jest już skonfigurowany do używania zasobników na potrzeby zależności. 

### <a name="run-the-sample-application"></a>Uruchamianie przykładowej aplikacji 

1. Otwórz przykładowy obszar roboczy w środowisku XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Rozwiń projekt **MQTT Client Sample**, a następnie rozwiń folder o takiej samej nazwie.  
3. Otwórz plik **ViewController.swift** do edycji w środowisku XCode. 
4. Wyszukaj zmienną **connectionString** i zaktualizuj wartość zanotowanymi wcześniej parametrami połączenia urządzenia.
5. Zapisz zmiany. 
6. Uruchom projekt w emulatorze urządzenia za pomocą przycisku **Skompiluj i uruchom** lub kombinacji klawiszy **Command + R**. 

   ![Uruchamianie projektu](media/quickstart-send-telemetry-ios/run-sample.png)

7. Po otwarciu emulatora wybierz pozycję **Start** w przykładowej aplikacji.

Poniższy zrzut ekranu przedstawia przykładowe dane wyjściowe w momencie wysyłania przez aplikację symulowanych danych telemetrycznych do centrum IoT:

   ![Uruchamianie urządzenia symulowanego](media/quickstart-send-telemetry-ios/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Przykładowa aplikacja uruchomiona w emulatorze środowiska XCode wyświetla dane dotyczące komunikatów wysłanych z urządzenia. Dane możesz wyświetlić także za pośrednictwem centrum IoT, gdy są odbierane. Rozszerzenie interfejsu wiersza polecenia usługi IoT Hub może połączyć się z punktem końcowym **Zdarzenia** po stronie usługi w usłudze IoT Hub. Rozszerzenie odbiera komunikaty z urządzenia do chmury wysyłane z urządzenia symulowanego. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze, aby odbierać i przetwarzać komunikaty urządzenie-chmura.

Uruchom następujące polecenia w usłudze Azure Cloud Shell, zastępując ciąg `YourIoTHubName` nazwą swojego centrum IoT:

```azurecli-interactive
az iot hub monitor-events --device-id myiOSdevice --hub-name YourIoTHubName
```

Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie odbierania przez rozszerzenie danych telemetrycznych wysyłanych przez urządzenie symulowane do centrum:

Poniższy zrzut ekranu przedstawia typ danych telemetrycznych wyświetlanych w lokalnym oknie terminalu:

![Wyświetlanie danych telemetrycznych](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano konfigurowanie centrum IoT, rejestrowanie urządzenia, wysyłanie symulowanych danych telemetrycznych do centrum z urządzenia z systemem iOS oraz odczytywanie danych telemetrycznych z centrum. 

Aby dowiedzieć się, jak kontrolować urządzenie symulowane z poziomu aplikacji zaplecza, przejdź do następnego przewodnika Szybki start.

> [!div class="nextstepaction"]
> [Szybki start: Kontrolowanie urządzenia podłączone do usługi IoT hub](quickstart-control-device-node.md)

<!-- Links -->
[lnk-process-d2c-tutorial]: tutorial-routing.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
