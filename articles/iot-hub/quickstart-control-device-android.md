---
title: Kontrolowanie urządzenia z poziomu usługi Azure IoT Hub — Szybki start (Android) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje Java. Jedna z aplikacji to aplikacja usługi, która może zdalnie kontrolować urządzenia podłączone do centrum. Druga aplikacja jest uruchamiana na urządzeniu fizycznym lub symulowanym podłączonym do centrum, które można kontrolować zdalnie.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: d125328d903b419aa81c54ffecf1f549d4cb4fe2
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330782"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Szybki start: Sterowanie urządzeniem połączonym z centrum IoT Hub (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury i zarządzanie urządzeniami z poziomu chmury. W tym przewodniku Szybki start użyjesz *metody bezpośredniej* do kontrolowania urządzenia symulowanego podłączonego do centrum IoT. Możesz użyć metod bezpośrednich, aby zdalnie zmieniać zachowanie urządzenia podłączonego do centrum IoT.

Przewodnik Szybki start używa dwóch wstępnie napisanych aplikacji Java:

* Aplikacja urządzenia symulowanego, która reaguje na metody bezpośrednie wywoływane z aplikacji usługi zaplecza. Aby odbierać wywołania metod bezpośrednich, ta aplikacja łączy się z punktem końcowym właściwym dla urządzenia w centrum IoT.

* Aplikacja usługi, która wywołuje metodę bezpośrednią na urządzeniu z systemem Android. Aby wywoływać metody bezpośrednie na urządzeniu, ta aplikacja łączy się z punktem końcowym po stronie usługi w centrum IoT.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Program Android Studio ze strony https://developer.android.com/studio/. Aby uzyskać więcej informacji na temat instalacji programu Android Studio, zobacz artykuł dotyczący [instalowania w systemie Android](https://developer.android.com/studio/install).

* W przykładzie w tym artykule jest używany zestaw Android SDK 27.

* Uruchom następujące polecenie, aby dodać rozszerzenia usługi Microsoft Azure IoT dla interfejsu wiersza polecenia platformy Azure do swojego wystąpienia usługi Cloud Shell. Rozszerzenia IOT dodaje określone polecenia usługi IoT Hub, IoT Edge i usługi aprowizacji urządzeń IoT (DPS) do wiersza polecenia platformy Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* Ten przewodnik Szybki start wymaga użycia dwóch przykładowych aplikacji: [przykładowej aplikacji systemu Android dla zestawu SDK urządzenia](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) i [przykładowej aplikacji systemu Android dla zestawu SDK](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample). Oba te przykłady są częścią repozytorium azure-iot-samples-java w usłudze GitHub. Pobierz lub sklonuj repozytorium [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Jeśli ukończono poprzedni przewodnik [Szybki start: Wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-android.md), można pominąć ten krok i użyć utworzonego już centrum IoT Hub.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Jeśli ukończono poprzedni przewodnik [Szybki start: Wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-android.md), można pominąć ten krok i użyć tego samego urządzenia, które zarejestrowano w poprzednim przewodniku Szybki start.

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w usłudze Azure Cloud Shell do tworzenia tożsamości urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyAndroidDevice**: ta wartość to nazwa nadana zarejestrowanemu urządzeniu. Użyj nazwy MyAndroidDevice w przedstawiony sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, być może trzeba będzie również używać tej nazwy w tym artykule oraz zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyAndroidDevice
    ```

2. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby uzyskać _parametry połączenia urządzenia_ dla urządzenia, które właśnie zostało zarejestrowane:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyAndroidDevice \
      --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

## <a name="retrieve-the-service-connection-string"></a>Pobieranie parametrów połączenia usługi

Potrzebne będą także _parametry połączenia usługi_, aby umożliwić aplikacji usługi zaplecza nawiązywanie połączenia z centrum IoT Hub w celu wykonywania metod i pobierania komunikatów. Następujące polecenie pobiera parametry połączenia usługi dla centrum IoT:

**YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

```azurecli-interactive
az iot hub show-connection-string --name YourIoTHubName --policy-name service --output table
```

Zanotuj parametry połączenia usługi, które wyglądają następująco:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Użyjesz tej wartości w dalszej części tego przewodnika Szybki start. Parametry połączenia usługi różnią się od parametrów połączenia urządzenia.

## <a name="listen-for-direct-method-calls"></a>Nasłuchiwanie wywołań metod bezpośrednich

Przykładową aplikację dla zestawu SDK urządzenia można uruchomić na urządzeniu fizycznym z systemem Android lub w emulatorze systemu Android. Przykład łączy się z punktem końcowym właściwym dla urządzenia w centrum IoT, wysyła symulowane dane telemetryczne i nasłuchuje wywołań metod bezpośrednich z centrum. W tym przewodniku Szybki start wywołanie metody bezpośredniej z centrum nakazuje urządzeniu zmienić interwał wysyłania danych telemetrycznych. Urządzenie symulowane wysyła potwierdzenie z powrotem do centrum po wykonaniu metody bezpośredniej.

1. Otwórz przykładowy projekt GitHub dla systemu Android w programie Android Studio. Projekt znajduje się w następującym katalogu sklonowanej lub pobranej kopii repozytorium [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. W programie Android Studio otwórz plik *gradle.properties* dla przykładowego projektu i zastąp symbol zastępczy **Device_Connection_String** zanotowanymi wcześniej parametrami połączenia.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. W programie Android Studio kliknij kolejno pozycje **File** > **Sync Project with Gradle Files** (Plik > Synchronizuj projekt z plikami Gradle). Sprawdź, czy kompilacja została zakończona.

   > [!NOTE]
   > Jeśli synchronizacja projektu zakończy się niepowodzeniem, może być jednego z następujących powodów:
   >
   > * Wersje Wtyczka programu Gradle dla systemu Android i narzędzia Gradle, do którego odwołuje się projekt jest nieaktualny dla używanej wersji programu Android Studio. Postępuj zgodnie z [w instrukcjach](https://developer.android.com/studio/releases/gradle-plugin) odwołać się i zainstaluj poprawne wersje wtyczki i narzędzie Gradle dla tej instalacji.
   > * Umowa licencyjna dotycząca zestawu Android SDK nie jest podpisany. Postępuj zgodnie z instrukcjami w danych wyjściowych kompilacji, podpisać umowę licencyjną, a następnie pobierz zestaw SDK.

4. Po zakończeniu kompilacji kliknij kolejno pozycje **Run** > **Run 'app'** (Uruchom > Uruchom „aplikację”). Skonfiguruj aplikację do uruchamiania na fizycznym urządzeniu z systemem Android lub w emulatorze systemu Android. Aby uzyskać więcej informacji na temat uruchamiania aplikacji systemu Android na urządzeniu fizycznym lub w emulatorze, zobacz [Run your app (Uruchamianie aplikacji)](https://developer.android.com/training/basics/firstapp/running-app).

5. Po załadowaniu aplikacji kliknij przycisk **Start**, aby rozpocząć wysyłanie danych telemetrycznych do centrum IoT Hub:

    ![Zrzut ekranu przykładowej aplikacji dla systemu android na urządzeniu klienta](media/quickstart-control-device-android/sample-screenshot.png)

Ta aplikacja musi nadal działać w urządzeniu fizycznym lub emulatorze, gdy będziesz wykonywać przykład zestawu SDK w celu zaktualizowania interwału telemetrii w czasie wykonywania.

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

W tej sekcji, używając usługi Azure Cloud Shell z [rozszerzeniem IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest), będziesz monitorować komunikaty urządzenia wysyłane przez urządzenie z systemem Android.

1. Używając usługi Azure Cloud Shell, uruchom następujące polecenie, aby nawiązać połączenie i odczytać komunikaty z centrum IoT:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie odbierania przez centrum IoT Hub danych telemetrycznych wysyłanych przez urządzenie z systemem Android:

      ![Czytanie komunikatów urządzenia za pomocą interfejsu wiersza polecenia platformy Azure](media/quickstart-control-device-android/read-data.png)

Domyślnie aplikacja telemetrii wysyła dane telemetryczne z urządzenia z systemem Android co 5 sekund. W następnej sekcji użyjesz wywołania metody bezpośredniej do zaktualizowania interwału danych telemetrycznych urządzenia IoT dla systemu Android.

## <a name="call-the-direct-method"></a>Wywoływanie metody bezpośredniej

Aplikacja usługi łączy się z punktem końcowym po stronie usługi w centrum IoT Hub. Aplikacja wykonuje wywołania metod bezpośrednich do urządzenia za pośrednictwem centrum IoT oraz nasłuchuje potwierdzeń.

Uruchom tę aplikację na oddzielnym urządzeniu fizycznym z systemem Android lub w emulatorze systemu Android.

Aplikacja usługi zaplecza centrum IoT Hub zwykle działa w chmurze, gdzie można łatwiej ograniczyć ryzyko związane z poufnymi parametrami połączenia, które kontrolują wszystkie urządzenia w centrum IoT Hub. W tym przykładzie uruchamiamy ją jako aplikację systemu Android tylko na potrzeby pokazu. Inne wersje językowe tego przewodnika Szybki start zawierają inne przykłady dokładniej dostosowane do aplikacji usługi zaplecza.

1. Otwórz przykładowy projekt dla systemu Android z usługi GitHub w programie Android Studio. Projekt znajduje się w następującym katalogu sklonowanej lub pobranej kopii repozytorium [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. W programie Android Studio otwórz plik *gradle.properties* przykładowego projektu i zaktualizuj wartości właściwości **ConnectionString** i **DeviceId** przy użyciu zanotowanych wcześniej parametrów połączenia oraz zarejestrowanego identyfikatora urządzenia z systemem Android.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. W programie Android Studio kliknij kolejno pozycje **File** > **Sync Project with Gradle Files** (Plik > Synchronizuj projekt z plikami Gradle). Sprawdź, czy kompilacja została zakończona.

   > [!NOTE]
   > Jeśli synchronizacja projektu zakończy się niepowodzeniem, może być jednego z następujących powodów:
   >
   > * Wersje Wtyczka programu Gradle dla systemu Android i narzędzia Gradle, do którego odwołuje się projekt jest nieaktualny dla używanej wersji programu Android Studio. Postępuj zgodnie z [w instrukcjach](https://developer.android.com/studio/releases/gradle-plugin) odwołać się i zainstaluj poprawne wersje wtyczki i narzędzie Gradle dla tej instalacji.
   > * Umowa licencyjna dotycząca zestawu Android SDK nie jest podpisany. Postępuj zgodnie z instrukcjami w danych wyjściowych kompilacji, podpisać umowę licencyjną, a następnie pobierz zestaw SDK.

4. Po zakończeniu kompilacji kliknij kolejno pozycje **Run** > **Run 'app'** (Uruchom > Uruchom „aplikację”). Skonfiguruj aplikację do uruchamiania na osobnym fizycznym urządzeniu z systemem Android lub w emulatorze systemu Android. Aby uzyskać więcej informacji na temat uruchamiania aplikacji systemu Android na urządzeniu fizycznym lub w emulatorze, zobacz [Run your app (Uruchamianie aplikacji)](https://developer.android.com/training/basics/firstapp/running-app).

5. Po załadowaniu aplikacji zaktualizuj ustawienie **Set Messaging Interval** (Ustaw interwał komunikatów) na wartość **1000** i kliknij pozycję **Invoke** (Wywołaj).

    Interwał telemetrii komunikatów jest podawany w milisekundach. Domyślny interwał telemetrii przykładowego urządzenia został ustawiony na 5 sekund. Ta zmiana spowoduje zaktualizowanie urządzenia IoT z systemem Android tak, aby dane telemetryczne były wysyłane co sekundę.

    ![Wprowadzanie interwału telemetrii](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. Aplikacja otrzyma potwierdzenie wskazujące, czy wykonywanie metody zakończyło się pomyślnie.

    ![Potwierdzenie metody bezpośredniej](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start wywołano metodę bezpośrednią na urządzeniu z aplikacji zaplecza oraz odpowiedziano na wywołanie metody bezpośredniej w aplikacji urządzenia symulowanego.

Aby dowiedzieć się, jak przekierowywać komunikaty urządzenie-chmura do innych miejsc docelowych w chmurze, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Samouczek: przekierowywanie danych telemetrycznych do innych punktów końcowych w celu przetwarzania](tutorial-routing.md)
