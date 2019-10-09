---
title: Wysyłanie danych telemetrycznych do usługi Azure IoT Hub Szybki Start | Microsoft Docs
description: W tym przewodniku szybki start uruchomisz przykładową aplikację dla systemu iOS, która będzie wysyłać symulowane dane telemetryczne do centrum IoT i odczytywanie danych telemetrycznych z usługi IoT Hub na potrzeby przetwarzania w chmurze.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/03/2019
ms.openlocfilehash: a23518cd016a1711e47734df0f7179770aa92a87
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166988"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-ios"></a>Szybki Start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub (iOS)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT w chmurze w celu przechowania lub przetworzenia. W tym artykule opisano wysyłanie danych telemetrycznych z aplikacji symulowanego urządzenia do IoT Hub. Następnie można wyświetlić dane z aplikacji zaplecza.

W tym artykule jest stosowana wstępnie zapisywana aplikacja SWIFT do wysyłania danych telemetrycznych i narzędzia interfejsu wiersza polecenia w celu odczytu danych telemetrycznych z IoT Hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz przykład kodu z [przykładów platformy Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)
- Najnowsza wersja [Xcode](https://developer.apple.com/xcode/), z uruchomioną najnowszą wersją zestawu iOS SDK. Ten przewodnik Szybki Start został przetestowany z systemami XCode 10,2 i iOS 12,2.
- Najnowsza wersja programu [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).
- Uruchom następujące polecenie, aby dodać rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell. Rozszerzenie IOT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="create-an-iot-hub"></a>Tworzenie Centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Aby można było nawiązać połączenie, urządzenie musi zostać zarejestrowane w usłudze IoT Hub. W tym przewodniku szybki start użyjesz Azure Cloud Shell, aby zarejestrować symulowane urządzenie.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: Zastąp ten symbol zastępczy poniżej nazwą wybraną dla Centrum IoT Hub.

   **myiOSdevice**: jest to nazwa urządzenia, które jest rejestrowany. Zaleca się użycie **myiOSdevice** , jak pokazano. W przypadku wybrania innej nazwy dla urządzenia należy również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myiOSdevice
   ```

1. Uruchom następujące polecenie w Azure Cloud Shell, aby uzyskać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia:

   **YourIoTHubName**: Zastąp ten symbol zastępczy poniżej nazwą wybraną dla Centrum IoT Hub.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id myiOSdevice --output table
   ```

   Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=myiOSdevice;SharedAccessKey={YourSharedAccessKey}`

    Ta wartość zostanie użyta w dalszej części przewodnika Szybki Start.

## <a name="send-simulated-telemetry"></a>Wyślij symulowane dane telemetryczne

Przykładowa aplikacja jest uruchamiana na urządzeniu z systemem iOS, które nawiązuje połączenie z punktem końcowym specyficznym dla urządzenia w centrum IoT Hub i wysyła symulowaną telemetrię temperatury i wilgotności. 

### <a name="install-cocoapods"></a>Zainstaluj CocoaPods

CocoaPods Zarządzaj zależnościami dla projektów systemu iOS, które korzystają z bibliotek innych firm.

W oknie terminalu lokalnego przejdź do folderu Azure-IoT-Samples-iOS, który został pobrany w wymaganiach wstępnych. Następnie przejdź do przykładowego projektu:

```sh
cd quickstart/sample-device
```

Upewnij się, że XCode jest zamknięte, a następnie uruchom następujące polecenie, aby zainstalować CocoaPods, które są zadeklarowane w pliku **plik podfile** :

```sh
pod install
```

Wraz z instalacją zasobników wymaganych dla projektu, polecenie instalacji także utworzył plik obszaru roboczego XCode, który jest już skonfigurowany do używania zasobników dla zależności. 

### <a name="run-the-sample-application"></a>Uruchamianie przykładowej aplikacji 

1. Otwórz przykładowy obszar roboczy w XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Rozwiń **przykładowy projekt MQTT klienta** , a następnie rozwiń folder o tej samej nazwie.  
3. Otwórz **plik viewcontroller. Swift** do edycji w Xcode. 
4. Wyszukaj zmienną **ConnectionString** i zaktualizuj wartość przy użyciu parametrów połączenia urządzenia, które zostały wcześniej zanotowane.
5. Zapisz zmiany. 
6. Uruchom projekt w emulatorze urządzenia za pomocą przycisku **Kompiluj i Uruchom, a** następnie polecenie Key kombi **+ r**. 

   ![Uruchom projekt](media/quickstart-send-telemetry-ios/run-sample.png)

7. Gdy emulator zostanie otwarty, wybierz pozycję **Rozpocznij** w przykładowej aplikacji.

Poniższy zrzut ekranu przedstawia przykład danych wyjściowych, gdy aplikacja wysyła symulowane dane telemetryczne do centrum IoT:

   ![Uruchamianie symulowanego urządzenia](media/quickstart-send-telemetry-ios/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Przykładowa aplikacja uruchomiona na emulatorze XCode pokazuje dane dotyczące komunikatów wysyłanych z urządzenia. Możesz również wyświetlać dane za pomocą Centrum IoT w miarę ich odbierania. Rozszerzenie interfejsu wiersza polecenia IoT Hub może nawiązać połączenie z punktem końcowym **zdarzeń** po stronie usługi na IoT Hub. Rozszerzenie odbiera komunikaty z urządzenia do chmury wysyłane z symulowanego urządzenia. Aplikacja zaplecza IoT Hub zwykle działa w chmurze, aby odbierać i przetwarzać komunikaty z urządzenia do chmury.

Uruchom następujące polecenia w Azure Cloud Shell, zastępując `YourIoTHubName` nazwą Centrum IoT:

```azurecli-interactive
az iot hub monitor-events --device-id myiOSdevice --hub-name {YourIoTHubName}
```

Poniższy zrzut ekranu przedstawia dane wyjściowe, ponieważ rozszerzenie odbiera telemetrię wysłaną przez symulowane urządzenie do centrum:

Poniższy zrzut ekranu przedstawia typ telemetrii widocznej w oknie terminalu lokalnego:

![Wyświetl dane telemetryczne](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz Centrum IoT, zarejestrowano urządzenie, wysłało symulowane dane telemetryczne do centrum z urządzenia z systemem iOS i odczytuje dane telemetryczne z centrum. 

Aby dowiedzieć się, jak sterować urządzeniem symulowanym z poziomu aplikacji zaplecza, przejdź do następnego przewodnika Szybki Start.

> [!div class="nextstepaction"]
> [Szybki Start: sterowanie urządzeniem podłączonym do centrum IoT Hub](quickstart-control-device-node.md)
