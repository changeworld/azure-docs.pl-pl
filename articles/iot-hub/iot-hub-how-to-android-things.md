---
title: Tworzenie platformy dla urządzeń Android Things przy użyciu zestawów SDK usługi Azure IoT | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — dowiedz się, jak tworzyć informacje o systemach Android Things przy użyciu zestawów SDK usługi Azure IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: a06583e9aab4b082517d47c1022f7bec5184b9bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673397"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Tworzenie platformy dla urządzeń Android Things przy użyciu zestawów SDK usługi Azure IoT

[ZestawY SDK usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) zapewniają obsługę pierwszej warstwy dla popularnych platform, takich jak Windows, Linux, OSX, MBED i platformy mobilne, takie jak Android i iOS.  W ramach naszego zobowiązania do umożliwienia większego wyboru i elastyczności we wdrożeniach IoT zestaw Java SDK obsługuje również platformę [Android Things.](https://developer.android.com/things/)  Deweloperzy mogą korzystać z zalet systemu operacyjnego Android Rzeczy po stronie urządzenia, podczas gdy przy użyciu [usługi Azure IoT Hub](about-iot-hub.md) jako centrum wiadomości centralnej, która skaluje się do milionów jednocześnie połączonych urządzeń.

W tym samouczku opisano kroki tworzenia aplikacji po stronie urządzenia w systemie Android Rzeczy przy użyciu zestawu Azure IoT Java SDK.

## <a name="prerequisites"></a>Wymagania wstępne

* Android Rzeczy obsługiwane sprzętu z systemem operacyjnym Android Rzeczy uruchomione.  Możesz śledzić [dokumentację Android Things,](https://developer.android.com/things/get-started/kits#flash-at) jak migać system operacyjny Android Things.  Upewnij się, że urządzenie z systemem Android Things jest połączone z Internetem z podstawowymi urządzeniami peryferyjnymi, takimi jak klawiatura, wyświetlacz i mysz.  Ten poradnik używa Raspberry Pi 3.

* Najnowsza wersja [Android Studio](https://developer.android.com/studio/)

* Najnowsza wersja [Gita](https://git-scm.com/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby dodać rozszerzenie interfejsu wiersza polecenia usługi IoT Hub i utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT.

   **MyAndroidThingsDevice** : Jest to nazwa nadana dla zarejestrowanego urządzenia. Użyj MyAndroidThingsDevice jak pokazano. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz również używać tej nazwy w tym artykule oraz zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az extension add --name azure-iot
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby uzyskać *parametry połączenia urządzenia* dla właśnie zarejestrowanego urządzenia. Zamień `YourIoTHubName` poniżej nazwę wybraną dla centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

## <a name="building-an-android-things-application"></a>Tworzenie aplikacji na Android Things

1. Pierwszym krokiem do tworzenia aplikacji Android Things jest połączenie z urządzeniami z systemem Android Things. Podłącz urządzenie z systemem Android Things do wyświetlacza i podłącz go do Internetu. Android Things dostarcza [dokumentację](https://developer.android.com/things/get-started/kits) dotyczącą łączenia się z siecią Wi-Fi. Po nawiązaniu połączenia z Internetem zanotuj adres IP wymieniony w obszarze Sieci.

2. Użyj narzędzia [adb,](https://developer.android.com/studio/command-line/adb) aby połączyć się z urządzeniem Android Things z adresem IP, o których wspomniano powyżej. Sprawdź dokładnie połączenie za pomocą tego polecenia z terminala. Urządzenia powinny być wyświetlane jako "podłączone".

   ```
   adb devices
   ```

3. Pobierz naszą próbkę dla Android / Android Rzeczy z tego [repozytorium](https://github.com/Azure-Samples/azure-iot-samples-java) lub użyj Git.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. W programie Android Studio otwórz projekt systemu Android w lokalizacji w "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample".

5. Otwórz plik gradle.properties i zastąp "Device_connection_string" parametry połączenia urządzenia odnotowane wcześniej.
 
6. Kliknij przycisk Uruchom — debugowanie i wybierz urządzenie, aby wdrożyć ten kod na urządzeniach z systemem Android Things.

7. Po pomyślnym uruchomieniu aplikacji można wyświetlić aplikację działającą na urządzeniu z systemem Android Things. Ta przykładowa aplikacja wysyła losowo generowane odczyty temperatury.

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Dane można wyświetlać za pośrednictwem centrum IoT hub, jak są odbierane. Rozszerzenie interfejsu wiersza polecenia usługi IoT Hub może połączyć się z punktem końcowym **Zdarzenia** po stronie usługi w usłudze IoT Hub. Rozszerzenie odbiera komunikaty z urządzenia do chmury wysyłane z urządzenia symulowanego. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze, aby odbierać i przetwarzać komunikaty urządzenie-chmura.

Uruchom następujące polecenia w usłudze Azure Cloud Shell, zastępując ciąg `YourIoTHubName` nazwą swojego centrum IoT:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

* Dowiedz [się, jak zarządzać łącznością i niezawodnymi wiadomościami](iot-hub-reliability-features-in-sdks.md) przy użyciu sdk usługi IoT Hub.
* Dowiedz się, jak [tworzyć platformy mobilne,](iot-hub-how-to-develop-for-mobile-devices.md) takie jak iOS i Android.
* [Obsługa platformy Azure IoT SDK](iot-hub-device-sdk-platform-support.md)
