---
title: Wysyłanie danych telemetrycznych do platformy Azure IoT Hub szybki start (Android) | Microsoft Docs
description: W tym przewodniku szybki start uruchamiasz przykładową aplikację dla systemu Android, która umożliwia wysyłanie symulowanych danych telemetrycznych do centrum IoT i odczytywanie danych telemetrycznych z Centrum IoT w celu przetworzenia w chmurze.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: e213a92397240f2646ceda30688ecef422cdf29c
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166358"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Szybki Start: wysyłanie danych telemetrycznych IoT z urządzenia z systemem Android

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT w chmurze w celu przechowania lub przetworzenia. W tym przewodniku szybki start wysyłasz dane telemetryczne do IoT Hub z aplikacji systemu Android działającego na urządzeniu fizycznym lub symulowanym.

Aby wysłać dane telemetryczne, szybki start używa wstępnie zapisaną aplikację dla systemu Android. Dane telemetryczne zostaną odczytane z IoT Hub przy użyciu Azure Cloud Shell. Przed uruchomieniem aplikacji należy utworzyć Centrum IoT Hub i zarejestrować urządzenie w centrum.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

* Z https://developer.android.com/studio/ programu Android Studio. Aby uzyskać więcej informacji na temat instalacji Android Studio, zobacz [Android-Installation](https://developer.android.com/studio/install).

* Android SDK 27 jest używany przez przykład w tym artykule.

* Uruchom następujące polecenie, aby dodać rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell. Rozszerzenie IOT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* [Przykładowa aplikacja dla systemu Android](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) uruchamiana w tym przewodniku Szybki Start jest częścią repozytorium Azure-IoT-Samples-Java w serwisie GitHub. Pobierz lub Sklonuj repozytorium [Azure-IoT-Samples-Java](https://github.com/Azure-Samples/azure-iot-samples-java) .

## <a name="create-an-iot-hub"></a>Tworzenie Centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Aby można było nawiązać połączenie, urządzenie musi zostać zarejestrowane w usłudze IoT Hub. W tym przewodniku szybki start użyjesz Azure Cloud Shell, aby zarejestrować symulowane urządzenie.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: Zastąp ten symbol zastępczy poniżej nazwą wybraną dla Centrum IoT Hub.

   **MyAndroidDevice**: jest to nazwa urządzenia, które jest rejestrowany. Zaleca się użycie **MyAndroidDevice** , jak pokazano. W przypadku wybrania innej nazwy dla urządzenia należy również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Uruchom następujące polecenie w Azure Cloud Shell, aby uzyskać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia:

    **YourIoTHubName**: Zastąp ten symbol zastępczy poniżej nazwą wybraną dla Centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki Start, aby wysłać dane telemetryczne.

## <a name="send-simulated-telemetry"></a>Wyślij symulowane dane telemetryczne

1. Otwórz projekt przykładowego systemu Android w serwisie GitHub w Android Studio. Projekt znajduje się w następującym katalogu sklonowanej lub pobranej kopii repozytorium [Azure-IoT-Sample-Java](https://github.com/Azure-Samples/azure-iot-samples-java) .

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. W Android Studio Otwórz *Gradle. Properties* dla przykładowego projektu i Zastąp symbol zastępczy **Device_Connection_String** parametrami połączenia urządzenia, które zostały wykonane wcześniej.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. W Android Studio kliknij **plik** > **zsynchronizuj projekt z plikami Gradle**. Sprawdź, czy kompilacja została ukończona.

   > [!NOTE]
   > Jeśli synchronizacja projektu nie powiedzie się, może to być spowodowane jedną z następujących przyczyn:
   >
   > * Wersje wtyczki Gradle systemu Android i Gradle, do których odwołuje się projekt, są nieaktualne dla używanej wersji programu Android Studio. Postępuj zgodnie z [tymi instrukcjami](https://developer.android.com/studio/releases/gradle-plugin) , aby odnieść się do i zainstalować odpowiednie wersje wtyczki i Gradle dla danej instalacji.
   > * Umowa licencyjna dla Android SDK nie została podpisana. Postępuj zgodnie z instrukcjami podanymi w danych wyjściowych kompilacji w celu podpisania umowy licencyjnej i pobrania zestawu SDK.

4. Po zakończeniu kompilacji kliknij pozycję **uruchom** > **Uruchom "App"** . Skonfiguruj aplikację do uruchamiania na fizycznym urządzeniu z systemem Android lub w emulatorze systemu Android. Aby uzyskać więcej informacji na temat uruchamiania aplikacji dla systemu Android na urządzeniu fizycznym lub emulatorze, zobacz [Uruchamianie aplikacji](https://developer.android.com/training/basics/firstapp/running-app).

5. Po załadowaniu aplikacji kliknij przycisk **Uruchom** , aby rozpocząć wysyłanie danych telemetrycznych do IoT Hub:

    ![Aplikacja](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

W tej sekcji użyjesz Azure Cloud Shell z [rozszerzeniem IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) do monitorowania komunikatów urządzeń wysyłanych przez urządzenie z systemem Android.

1. Korzystając z Azure Cloud Shell, uruchom następujące polecenie, aby nawiązać połączenie i odczytać wiadomości z Centrum IoT Hub:

   **YourIoTHubName**: Zastąp ten symbol zastępczy poniżej nazwą wybraną dla Centrum IoT Hub.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe przesyłane przez Centrum IoT Hub odbierane przez urządzenie z systemem Android:

      ![Odczytywanie komunikatów z urządzenia przy użyciu interfejsu wiersza polecenia platformy Azure](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz Centrum IoT, zarejestrowano urządzenie, wysłało symulowane dane telemetryczne do koncentratora przy użyciu aplikacji systemu Android, i odczytuje dane telemetryczne z centrum przy użyciu Azure Cloud Shell.

Aby dowiedzieć się, jak sterować urządzeniem symulowanym z poziomu aplikacji zaplecza, przejdź do następnego przewodnika Szybki Start.

> [!div class="nextstepaction"]
> [Szybki Start: sterowanie urządzeniem podłączonym do centrum IoT Hub](quickstart-control-device-android.md)