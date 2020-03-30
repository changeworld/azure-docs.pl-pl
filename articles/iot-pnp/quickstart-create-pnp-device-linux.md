---
title: Tworzenie urządzenia w wersji Zapoznawczej usługi Azure IoT (Linux) | Dokumenty firmy Microsoft
description: Użyj modelu możliwości urządzenia do generowania kodu urządzenia. Następnie uruchom kod urządzenia i zobacz urządzenie połączyć się z Centrum IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d2cc440572d6f33480972c15f5c498cc384cb2e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75550489"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Szybki start: tworzenie urządzenia Podglądu IoT Plug and Play (Linux) za pomocą modelu funkcji urządzenia

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

_Model możliwości urządzenia_ (DCM) opisuje możliwości urządzenia Typu Plug and Play IoT. Dcm jest często skojarzony z jednostką SKU produktu. Możliwości zdefiniowane w dcm są zorganizowane w interfejsy wielokrotnegoużynia. Można wygenerować kod urządzenia szkielet z DCM. Ten szybki start pokazuje, jak używać kodu VS na Ubuntu Linux do tworzenia urządzenia IoT Plug and Play przy użyciu dcm.

## <a name="prerequisites"></a>Wymagania wstępne

Ten szybki start zakłada, że używasz Ubuntu Linux ze środowiskiem graficznym. Kroki w tym samouczku zostały przetestowane przy użyciu Ubuntu 18.04.

Aby ukończyć ten szybki start, należy zainstalować następujące oprogramowanie na lokalnym komputerze z systemem Linux:

* Zainstaluj **GCC**, **Git**, **cmake**i `apt-get` wszystkie zależności za pomocą polecenia:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Sprawdź, czy `cmake` wersja jest powyżej **2.8.12,** a wersja **GCC** jest powyżej **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

* [Program Visual Studio Code](https://code.visualstudio.com/)

### <a name="install-azure-iot-tools"></a>Instalowanie narzędzi Usługi Azure IoT

Aby zainstalować pakiet rozszerzeń [narzędzia IoT dla programu VS Code,](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) należy wykonać następujące kroki:

1. W programie VS Code wybierz kartę **Rozszerzenia.**
1. Wyszukaj **narzędzia IoT platformy Azure**.
1. Wybierz pozycję **Zainstaluj**.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Pobierz ciąg połączenia dla repozytorium modelu firmy

Parametry połączenia _repozytorium repozytorium modelu firmy_ można znaleźć w portalu [portalu Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) po zalogowaniu się za pomocą konta służbowego firmy Microsoft lub identyfikatora partnera firmy Microsoft, jeśli go masz. Po zalogowaniu się wybierz **repozytorium firmowe,** a następnie **parametry połączenia**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki start można użyć menedżera biblioteki [Vcpkg,](https://github.com/microsoft/vcpkg) aby zainstalować zestaw SDK urządzenia Usługi Azure IoT C w środowisku deweloperskim.

Otwórz powłokę. Wykonaj następujące polecenie, aby zainstalować Vcpkg:

```bash
cd ~
git clone https://github.com/microsoft/vcpkg
cd vcpkg
./bootstrap-vcpkg.sh
./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
```

Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

## <a name="author-your-model"></a>Tworzenie modelu

W tym przewodniku Szybki start można użyć istniejącego przykładowego modelu możliwości urządzenia i skojarzonych interfejsów.

1. Utwórz `pnp_app` katalog na dysku lokalnym. Ten folder jest używany dla plików modelu urządzenia i skrótu kodu urządzenia.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Pobierz do `pnp_app` folderu przykładowy model możliwości urządzenia i przykładowe pliki interfejsu.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Otwórz `pnp_app` folder z kodem VS. Pliki można wyświetlać za pomocą programu IntelliSense:

    ![Model możliwości urządzenia](media/quickstart-create-pnp-device-linux/dcm.png)

1. W pobranych plikach `<YOUR_COMPANY_NAME_HERE>` zastąp `@id` w polach i `schema` z unikatową wartością. Używaj tylko znaków a-z, A-Z, 0-9 i podkreślenia. Aby uzyskać więcej informacji, zobacz [Format identyfikatora Digital Twin](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generowanie skrótu kodu C

Teraz, gdy masz DCM i jego skojarzone interfejsy, można wygenerować kod urządzenia, który implementuje model. Aby wygenerować skrót kodu C w programie VS Code:

1. Po `pnp_app` otwarciu folderu w programie VS Code użyj **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń, wprowadź polecenie **IoT Plug and Play**i wybierz pozycję **Generuj skrót kodu urządzenia**.

    > [!NOTE]
    > Przy pierwszym użyciu narzędzia Generator kodu IoT Plug and Play trwa kilka sekund, aby pobrać i zainstalować automatycznie.

1. Wybierz plik **SampleDevice.capabilitymodel.json,** który będzie używany do generowania skrótu kodu urządzenia.

1. Wprowadź nazwę projektu **sample_device**. Będzie to nazwa aplikacji urządzenia.

1. Wybierz **ANSI C** jako swój język.

1. Wybierz za pośrednictwem ciągu **połączenia urządzenia Usługi IoT** jako metodę połączenia.

1. Wybierz **CMake Project w systemie Linux** jako szablon projektu.

1. Wybierz **via Vcpkg** jako sposób na włączenie sdk urządzenia.

1. Nowy folder o nazwie **sample_device** jest tworzony w tym samym miejscu co plik DCM, a w nim są wygenerowane pliki skrótowe kodu urządzenia. Vs Code otwiera nowe okno, aby je wyświetlić.
    ![Kod urządzenia](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>Tworzenie i uruchamianie kodu

Kod źródłowy sdk urządzenia służy do tworzenia skrótu wygenerowanego kodu urządzenia. Aplikacja, która tworzysz symuluje urządzenie, które łączy się z centrum IoT hub. Aplikacja wysyła dane telemetryczne i właściwości i odbiera polecenia.

1. Utwórz folder kompilacji **CMake** dla aplikacji **sample_device:**

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Uruchom CMake do tworzenia aplikacji za pomocą SDK. Następujące polecenie zakłada zainstalowanie **vcpkg** w folderze domowym:

    ```bash
    cmake .. -DCMAKE_TOOLCHAIN_FILE=~/vcpkg/scripts/buildsystems/vcpkg.cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build .
    ```

1. Po pomyślnym zakończeniu kompilacji uruchom aplikację przekazującą parametry połączenia urządzenia ioT jako parametr.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "<YourDeviceConnectionString>"
    ```

1. Aplikacja urządzenia rozpoczyna wysyłanie danych do usługi IoT Hub.

    ![Aplikacja urządzenia uruchomiona](media/quickstart-create-pnp-device-linux/device-app-running.png)

## <a name="validate-the-code"></a>Sprawdzanie poprawności kodu

### <a name="publish-device-model-files-to-model-repository"></a>Publikowanie plików modelu urządzenia w repozytorium modelu

Aby sprawdzić poprawność kodu urządzenia za pomocą interfejsu wiersza polecenia **az,** należy opublikować pliki w repozytorium modelu.

1. Po `pnp_app` otwarciu folderu w kodzie VS użyj **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń, wpisać i wybrać opcję **IoT Plug & Play: Submit files to Model Repozytorium**.

1. `SampleDevice.capabilitymodel.json` Zaznaczanie `EnvironmentalSensor.interface.json` i pliki.

1. Wprowadź parametry połączenia repozytorium modelu firmy.

    > [!NOTE]
    > Parametry połączenia są wymagane tylko przy pierwszym połączeniu z repozytorium.

1. W oknie wyjściowym kodu VS i powiadomieniu można sprawdzić, czy pliki zostały pomyślnie opublikowane.

    > [!NOTE]
    > Jeśli podczas publikowania plików modelu urządzenia pojawia się błędy, możesz spróbować użyć polecenia **IoT Plug and Play: Wyloguj się z repozytorium modelu,** aby się wylogować i wykonać kolejne czynności.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą interfejsu wiersza polecenia IoT platformy Azure

Po uruchomieniu przykładu klienta urządzenia można sprawdzić, czy działa z interfejsu wiersza polecenia platformy Azure.

Użyj następującego polecenia, aby wyświetlić dane telemetryczne wysyłane przez przykładowe urządzenie. Może być konieczne odczekanie minuty lub dwóch, zanim zobaczysz dane telemetryczne w danych wyjściowych:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubNme> --device-id <YourDeviceID>
```

Aby wyświetlić wszystkie właściwości wysyłane przez urządzenie, użyj następującego polecenia:

```azurecli-interactive
az iot dt list-properties --device-id <YourDeviceID> --hub-name <YourIoTHubNme> --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć urządzenie Typu Plug and Play usługi IoT przy użyciu kontrolera DCM.

Aby dowiedzieć się więcej o dcms i jak tworzyć własne modele, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i testowanie modelu możliwości urządzenia przy użyciu kodu programu Visual Studio](tutorial-pnp-visual-studio-code.md)
