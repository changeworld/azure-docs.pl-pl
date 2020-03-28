---
title: Tworzenie urządzenia IoT Plug and Play Preview, które jest gotowe do certyfikacji | Dokumenty firmy Microsoft
description: Jako deweloper urządzeń dowiedz się, jak utworzyć urządzenie IoT Plug and Play Preview, które jest gotowe do certyfikacji.
author: tbhagwat3
ms.author: tanmayb
ms.date: 12/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e97aa07d2a43a03805fd881c674157ee676c37b4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239906"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>Tworzenie urządzenia IoT Plug and Play Preview, które jest gotowe do certyfikacji

W tym samouczku opisano, jak jako deweloper urządzeń można utworzyć urządzenie IoT Plug and Play Preview, które jest gotowe do certyfikacji.

Testy certyfikacyjne sprawdzają, czy:

- Kod urządzenia IoT Plug and Play jest zainstalowany na urządzeniu.
- Twój kod urządzenia IoT Plug and Play jest zbudowany za pomocą usługi Azure IoT SDK.
- Kod urządzenia obsługuje [usługę inicjowania obsługi urządzeń usługi Azure IoT Hub.](../iot-dps/about-iot-dps.md)
- Kod urządzenia implementuje interfejs informacji o urządzeniu.
- Model możliwości i kod urządzenia działają z IoT Central.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- [Kod programu Visual Studio](https://code.visualstudio.com/download)
- Pakiet rozszerzeń [narzędzia IoT usługi Azure dla programu VS](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

Należy również ukończyć [model funkcji urządzenia za pomocą urządzenia, aby utworzyć szybki](quickstart-create-pnp-device-windows.md) start urządzenia dla systemu Windows. Przewodnik Szybki start pokazuje, jak skonfigurować środowisko programistyczne przy użyciu vcpkg i utworzyć przykładowy projekt.

## <a name="store-a-capability-model-and-interfaces"></a>Przechowywanie modelu możliwości i interfejsów

W przypadku urządzeń Typu Plug and Play IoT należy tworzyć model możliwości i interfejsy definiujące możliwości urządzenia jako pliki JSON.

Te pliki JSON można przechowywać w trzech różnych lokalizacjach:

- Repozytorium modelu publicznego.
- Repozytorium modelu firmy.
- Na urządzeniu.

Obecnie, aby certyfikować urządzenie, pliki muszą być przechowywane w repozytorium modelu firmy lub w publicznym repozytorium modelu.

## <a name="include-the-required-interfaces"></a>Dołącz wymagane interfejsy

Aby przejść proces certyfikacji, należy uwzględnić i zaimplementować interfejs **informacji o urządzeniu** w modelu możliwości. Ten interfejs posiada następującą identyfikację:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Po zakończeniu [programu Szybki start: tworzenie urządzenia za pomocą modelu funkcji urządzenia](quickstart-create-pnp-device-windows.md)zostało już uwzględnione w interfejsie Informacje o **urządzeniu.**

Aby dołączyć interfejs **informacji o urządzeniu** do modelu urządzenia, `implements` dodaj identyfikator interfejsu do właściwości modelu możliwości:

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Aby wyświetlić interfejs **informacji o urządzeniu** w programie VS Code:

1. Użyj **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń.

1. Wprowadź **pozycję Plug and Play,** a następnie wybierz polecenie Otwórz **repozytorium modeli IoT Plug and Play.** Wybierz **pozycję Otwórz publiczne repozytorium modeli**. Publiczne repozytorium modelu otwiera się w programie VS Code.

1. W repozytorium modelu publicznego wybierz kartę **Interfejsy,** wybierz ikonę filtru i wprowadź **informacje o urządzeniu** w polu filtru.

1. Aby utworzyć lokalną kopię interfejsu **Informacje o urządzeniu,** zaznacz ją na liście filtrowanych, a następnie wybierz pozycję **Pobierz**. Vs Code wyświetla plik interfejsu.

Aby wyświetlić interfejs **informacji o urządzeniu** przy użyciu interfejsu wiersza polecenia platformy Azure:

1. [Zainstaluj rozszerzenie interfejsu wiersza polecenia usługi Azure IoT](howto-install-pnp-cli.md).

1. Użyj następującego polecenia interfejsu wiersza polecenia platformy Azure, aby wyświetlić interfejs o identyfikatorze interfejsu informacji o urządzeniu:

    ```azurecli
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

Aby uzyskać więcej informacji, zobacz [Instalowanie i używanie rozszerzenia Usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure.](howto-install-pnp-cli.md)

## <a name="update-device-code"></a>Aktualizowanie kodu urządzenia

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Włącz inicjowanie obsługi administracyjnej urządzeń za pośrednictwem usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT (DPS)

Aby certyfikować urządzenie, musi włączyć inicjowanie obsługi administracyjnej za pośrednictwem [usługi inicjowania obsługi administracyjnej urządzeń Usługi Azure IoT (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps). Aby dodać możliwość korzystania z DPS, można wygenerować skrót kodu C w kodzie VS. Wykonaj następujące kroki:

1. Otwórz folder z plikiem DCM w programie VS Code, użyj **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń, wprowadź polecenie **IoT Plug and Play**i wybierz pozycję **Generuj skrót kodu urządzenia**.

1. Wybierz plik DCM, którego chcesz użyć do wygenerowania skrótu kodu urządzenia.

1. Wprowadź nazwę projektu, taką jak **sample_device**. Jest to nazwa aplikacji urządzenia.

1. Wybierz **ANSI C** jako język.

1. Wybierz **za pośrednictwem DPS (Usługa inicjowania obsługi urządzeń) klucz symetryczny** jako metodę połączenia.

1. Wybierz **CMake Project w systemie Windows** jako szablon projektu.

1. Wybierz **via Vcpkg** jako sposób na włączenie sdk urządzenia.

1. Vs Code otwiera nowe okno z wygenerowanymi plikami skrótowymi kodu urządzenia.

## <a name="build-and-run-the-code"></a>Tworzenie i uruchamianie kodu

Pakiet Vcpkg służy do tworzenia wygenerowanego skrótu kodu urządzenia. Aplikacja, która tworzysz symuluje urządzenie, które łączy się z centrum IoT hub. Aplikacja wysyła dane telemetryczne i właściwości i odbiera polecenia.

1. Utwórz `cmake` podkatalog `sample_device` w folderze i przejdź do tego folderu:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby utworzyć wygenerowany skrót kodu (zastępując symbol zastępczy katalogiem repozytorium Vcpkg):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Jeśli używasz programu Visual Studio 2017 lub 2015, należy określić generator CMake na podstawie narzędzi kompilacji, których używasz:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Jeśli cmake nie może znaleźć kompilatora Języka C++, po uruchomieniu poprzedniego polecenia są otrzymują błędy kompilacji. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Po pomyślnym zakończeniu kompilacji wprowadź poświadczenia DPS **(zakres identyfikatora DPS,** **klucz symetryczny DPS**, **identyfikator urządzenia)** jako parametry aplikacji. Aby uzyskać poświadczenia z portalu certyfikacji, zobacz [Łączenie i testowanie urządzenia IoT Plug and Play](tutorial-certification-test.md#connect-and-discover-interfaces).

    ```cmd\sh
    .\Debug\sample_device.exe [Device ID] [DPS ID Scope] [DPS symmetric key]
    ```

### <a name="implement-standard-interfaces"></a>Implementowanie interfejsów standardowych

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>Implementowanie interfejsów informacji o modelu i informacji sdk

Zestaw SDK urządzenia IoT platformy Azure implementuje interfejsy informacji o modelu i informacje o modelu. Jeśli używasz funkcji generowania kodu w programie VS Code, kod urządzenia używa sDK urządzenia Typu Plug and Play IoT.

Jeśli zdecydujesz się nie używać sdk urządzenia Usługi Azure IoT, można użyć kodu źródłowego SDK jako odwołanie do własnej implementacji.

#### <a name="implement-the-device-information-interface"></a>Implementowanie interfejsu informacji o urządzeniu

Zaimplementuj interfejs **Informacji o urządzeniu** na urządzeniu i podaj informacje specyficzne dla urządzenia z urządzenia w czasie wykonywania.

Można użyć przykład implementacji interfejsu **informacje o urządzeniu** dla systemu [Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) jako odwołanie.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>Zaimplementuj wszystkie możliwości zdefiniowane w modelu

Podczas certyfikacji urządzenie jest testowane programowo, aby upewnić się, że implementuje możliwości zdefiniowane w interfejsach. Użyj kodu stanu HTTP 501, aby odpowiadać na właściwości odczytu i zapisu i żądania poleceń, jeśli urządzenie ich nie implementuje.

## <a name="next-steps"></a>Następne kroki

Po zbudowaniu urządzenia IoT Plug and Play gotowego do certyfikacji następnym krokiem jest:

> [!div class="nextstepaction"]
> [Dowiedz się, jak certyfikować urządzenie](tutorial-certification-test.md)
