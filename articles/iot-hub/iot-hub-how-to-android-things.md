---
title: Twórz aplikacje na platformę Android rzeczy za pomocą zestawów SDK usługi Azure IoT | Dokumentacja firmy Microsoft
description: Dewelopera przewodnik dotyczący — więcej informacji na temat sposobu projektowania dotyczące systemu Android przy użyciu zestawów SDK usługi Azure IoT Hub.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: yizhon
ms.openlocfilehash: 8e36cee9857c00fcb618a8491595432fb0fd60fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60400137"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Twórz aplikacje na platformę Android rzeczy za pomocą zestawów SDK usługi Azure IoT

[Usługa Azure IoT Hub SDKs](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) obsługi pierwszą warstwę dla popularnych platform, takich jak Windows, Linux, OSX, MBED i platform urządzeń przenośnych, takich jak systemy Android i iOS.  W ramach naszego zobowiązania do włączenia większy wybór i elastyczność w przypadku wdrożeń IoT, obsługuje również zestawu Java SDK [Android rzeczy](https://developer.android.com/things/) platformy.  Deweloperzy mogą korzystać z zalet systemu operacyjnego Android rzeczy po stronie urządzenia podczas korzystania z [usługi Azure IoT Hub](about-iot-hub.md) jako centralna komunikat koncentratora, który umożliwia skalowanie do milionów równocześnie połączonych urządzeń.

W tym samouczku przedstawiono kroki umożliwiające tworzenie aplikacji po stronie urządzenia dotyczące systemu Android przy użyciu zestawu SDK Java usługi Azure IoT.

## <a name="prerequisites"></a>Wymagania wstępne

* Android rzeczy obsługiwane sprzętu za pomocą elementów systemu operacyjnego Android uruchomione.  Możesz wykonać [dokumentację dla systemu Android rzeczy](https://developer.android.com/things/get-started/kits#flash-at) o tym, jak zaktualizować system operacyjny Android rzeczy.  Upewnij się, że urządzenie Android rzeczy jest połączony z Internetem przy użyciu podstawowych urządzeń peryferyjnych, takich jak klawiatury, wyświetlania i myszy podłączone.  Ten samouczek używa Raspberry Pi 3.

* Najnowszą wersję [Android Studio](https://developer.android.com/studio/)

* Najnowszą wersję [Git](https://git-scm.com/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby dodać rozszerzenie interfejsu wiersza polecenia usługi IoT Hub i utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyAndroidThingsDevice** : jest to nazwa nadana dla zarejestrowanego urządzenia. Użyj MyAndroidThingsDevice, jak pokazano. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz również używać tej nazwy w tym artykule oraz zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby uzyskać *parametry połączenia urządzenia* dla urządzenia, które właśnie zostało zarejestrowane. Zastąp `YourIoTHubName` pod nazwą wybranego Centrum IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

## <a name="building-an-android-things-application"></a>Tworzenie aplikacji dla systemu Android rzeczy

1. Pierwszym krokiem do tworzenia aplikacji dla systemu Android rzeczy łączy na urządzeniach z systemem Android rzeczy. Łączenie urządzenia Android rzeczy do wyświetlania i połączyć ją z Internetem. Podaj android rzeczy [dokumentacji](https://developer.android.com/things/get-started/kits) o tym, jak połączyć się z sieci Wi-Fi. Po połączeniu się z Internetem, zanotuj adres IP na liście sieci.

2. Użyj [adb](https://developer.android.com/studio/command-line/adb) narzędzie, aby połączyć się z urządzeniem czynności dla systemu Android przy użyciu adresu IP przedstawionych powyżej. Sprawdź połączenie za pomocą tego polecenia z terminala. Powinien zostać wyświetlony urządzenia wyświetlane jako "połączone".

   ```
   adb devices
   ```

3. Pobierz nasze przykładowe dla systemu Android w systemie Android rzeczy z tego [repozytorium](https://github.com/Azure-Samples/azure-iot-samples-java) lub za pomocą narzędzia Git.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. W programie Android Studio Otwórz projekt systemu Android w umieszczone w sekcji "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample".

5. Otwórz plik gradle.properties i zastąp "Device_connection_string" przy użyciu parametrów połączenia urządzenia zanotowanej wcześniej.
 
6. Kliknij przebieg - debugowanie i wybierz urządzenie, aby wdrożyć ten kod na urządzeniach z systemem Android rzeczy.

7. Po pomyślnym uruchomieniu aplikacji, można zobaczyć aplikację działającą na urządzeniu z systemem Android rzeczy. Ta przykładowa aplikacja wysyła losowo generowany odczytów.

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Po otrzymaniu, mogą wyświetlać dane za pośrednictwem usługi IoT hub. Rozszerzenie interfejsu wiersza polecenia usługi IoT Hub może połączyć się z punktem końcowym **Zdarzenia** po stronie usługi w usłudze IoT Hub. Rozszerzenie odbiera komunikaty z urządzenia do chmury wysyłane z urządzenia symulowanego. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze, aby odbierać i przetwarzać komunikaty urządzenie-chmura.

Uruchom następujące polecenia w usłudze Azure Cloud Shell, zastępując ciąg `YourIoTHubName` nazwą swojego centrum IoT:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [sposobu zarządzania łącznością i niezawodną obsługę komunikatów](iot-hub-reliability-features-in-sdks.md) za pomocą zestawów SDK Centrum IoT.
* Dowiedz się więcej o sposobie [programowanie dla platform urządzeń przenośnych](iot-hub-how-to-develop-for-mobile-devices.md) takich jak systemy iOS i Android.
* [Pomoc techniczna platformy zestawu SDK usługi IoT platformy Azure](iot-hub-device-sdk-platform-support.md)
