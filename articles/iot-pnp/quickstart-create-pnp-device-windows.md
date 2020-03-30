---
title: Tworzenie urządzenia IoT Plug and Play Preview (Windows) | Dokumenty firmy Microsoft
description: Użyj modelu możliwości urządzenia do generowania kodu urządzenia. Następnie uruchom kod urządzenia i zobacz urządzenie połączyć się z Centrum IoT Hub.
author: miagdp
ms.author: miag
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e80194f53a406b8b378d0fb787df627937125a27
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75867488"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Szybki start: tworzenie urządzenia Typu Plug and Play Preview (Windows) za pomocą modelu funkcji urządzenia

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

_Model możliwości urządzenia_ (DCM) opisuje możliwości urządzenia Typu Plug and Play IoT. Dcm jest często skojarzony z jednostką SKU produktu. Możliwości zdefiniowane w dcm są zorganizowane w interfejsy wielokrotnegoużynia. Można wygenerować kod urządzenia szkielet z DCM. Ten przewodnik Szybki start pokazuje, jak używać programu VS Code w systemie Windows do tworzenia urządzenia Typu Plug and Play IoT przy użyciu kontrolera DCM.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten szybki start, należy zainstalować następujące oprogramowanie na komputerze lokalnym:

* [Tworzenie narzędzi dla programu Visual Studio za](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) pomocą narzędzi **kompilacji języka C++** i obciążeń **składników menedżera pakietów NuGet.** Lub jeśli masz już [visual studio (społeczność, professional lub przedsiębiorstwo)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 lub 2015 z tymi samymi obciążeniami zainstalowanymi.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).
* [Program Visual Studio Code](https://code.visualstudio.com/)

### <a name="install-azure-iot-tools"></a>Instalowanie narzędzi Usługi Azure IoT

Aby zainstalować pakiet rozszerzeń [narzędzia IoT dla programu VS Code,](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) należy wykonać następujące kroki:

1. W programie VS Code wybierz kartę **Rozszerzenia.**
1. Wyszukaj **narzędzia IoT platformy Azure**.
1. Wybierz pozycję **Zainstaluj**.

### <a name="install-the-azure-iot-explorer"></a>Instalowanie eksploratora IoT platformy Azure

Pobierz i zainstaluj najnowszą wersję **eksploratora IoT platformy Azure** ze strony [repozytorium](https://github.com/Azure/azure-iot-explorer/releases) narzędzia, wybierając plik msi w obszarze "Zasoby" dla najnowszej aktualizacji.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Pobierz ciąg połączenia dla repozytorium modelu firmy

Parametry połączenia _repozytorium repozytorium modelu firmy_ można znaleźć w portalu [portalu Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) po zalogowaniu się za pomocą konta służbowego firmy Microsoft lub identyfikatora partnera firmy Microsoft, jeśli go masz. Po zalogowaniu się wybierz **repozytorium firmowe,** a następnie **parametry połączenia**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby uzyskać _parametry połączenia centrum IoT_ dla koncentratora (uwaga do późniejszego użycia):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki start można użyć menedżera biblioteki [Vcpkg,](https://github.com/microsoft/vcpkg) aby zainstalować zestaw SDK urządzenia Usługi Azure IoT C w środowisku deweloperskim.

1. Otwórz wiersz polecenia. Wykonaj następujące polecenie, aby zainstalować Vcpkg:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Następnie, aby podłączyć [integrację](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)całego użytkownika, uruchom następujące czynności (uwaga: wymaga administratora przy pierwszym użyciu):

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Zainstaluj zestaw Vcpkg urządzenia usługi Azure IoT C:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>Tworzenie modelu

W tym przewodniku Szybki start można użyć istniejącego przykładowego modelu możliwości urządzenia i skojarzonych interfejsów.

1. Utwórz `pnp_app` folder na dysku lokalnym. Ten folder jest używany dla plików modelu urządzenia i skrótu kodu urządzenia.

1. Pobierz [model możliwości urządzenia i przykładowe pliki interfejsu](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) oraz `pnp_app` [przykładowe pliki interfejsu](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) i zapisz pliki w folderze.

    > [!TIP]
    > Aby pobrać plik z gitHub, przejdź do pliku, kliknij prawym przyciskiem myszy **raw**, a następnie wybierz pozycję **Zapisz łącze jako**.

1. Otwórz `pnp_app` folder z kodem VS. Pliki można wyświetlać za pomocą programu IntelliSense:

    ![Model możliwości urządzenia](media/quickstart-create-pnp-device/dcm.png)

1. W pobranych plikach `<YOUR_COMPANY_NAME_HERE>` zastąp `@id` w polach i `schema` z unikatową wartością. Używaj tylko znaków a-z, A-Z, 0-9 i podkreślenia. Aby uzyskać więcej informacji, zobacz [Format identyfikatora Digital Twin](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generowanie skrótu kodu C

Teraz, gdy masz DCM i jego skojarzone interfejsy, można wygenerować kod urządzenia, który implementuje model. Aby wygenerować skrót kodu C w programie VS Code:

1. Po `pnp_app` otwarciu folderu w programie VS Code użyj **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń, wprowadź polecenie **IoT Plug and Play**i wybierz pozycję **Generuj skrót kodu urządzenia**.

    > [!NOTE]
    > Przy pierwszym użyciu interfejsu wiersza polecenia IoT Plug and Play CodeGen, pobieranie i instalowanie jest możliwe przez kilka sekund.

1. Wybierz plik **SampleDevice.capabilitymodel.json,** który będzie używany do generowania skrótu kodu urządzenia.

1. Wprowadź nazwę projektu **sample_device**. Jest to nazwa aplikacji urządzenia.

1. Wybierz **ANSI C** jako swój język.

1. Wybierz za pośrednictwem ciągu **połączenia urządzenia Usługi IoT** jako metodę połączenia.

1. Wybierz **CMake Project w systemie Windows** jako szablon projektu.

1. Wybierz **via Vcpkg** jako sposób na włączenie sdk urządzenia.

1. Nowy folder o nazwie **sample_device** jest tworzony w tym samym miejscu co plik DCM, a w nim są wygenerowane pliki skrótowe kodu urządzenia. Vs Code otwiera nowe okno, aby je wyświetlić.
    ![Kod urządzenia](media/quickstart-create-pnp-device/device-code.png)

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

1. Po pomyślnym zakończeniu kompilacji uruchom aplikację, przekazując ciąg połączenia urządzenia ioT jako parametr.

    ```cmd\sh
    .\Debug\sample_device.exe "<YourDeviceConnectionString>"
    ```

1. Aplikacja urządzenia rozpoczyna wysyłanie danych do usługi IoT Hub.

    ![Aplikacja urządzenia uruchomiona](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Sprawdzanie poprawności kodu

### <a name="publish-device-model-files-to-model-repository"></a>Publikowanie plików modelu urządzenia w repozytorium modelu

Aby sprawdzić poprawność kodu urządzenia za pomocą **usługi Azure IoT Explorer,** należy opublikować pliki w repozytorium modelu.

1. Po `pnp_app` otwarciu folderu w programie VS Code użyj **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń, wpisać i wybrać **opcję IoT Plug & Play: Prześlij pliki do repozytorium modelu**.

1. `SampleDevice.capabilitymodel.json` Zaznaczanie `EnvironmentalSensor.interface.json` i pliki.

1. Wprowadź parametry połączenia repozytorium modelu firmy.

    > [!NOTE]
    > Parametry połączenia są wymagane tylko przy pierwszym połączeniu z repozytorium.

1. W oknie wyjściowym kodu VS i powiadomieniu można sprawdzić, czy pliki zostały pomyślnie opublikowane.

    > [!NOTE]
    > Jeśli podczas publikowania plików modelu urządzenia pojawia się błędy, możesz spróbować użyć polecenia **IoT Plug and Play: Wyloguj się z repozytorium modelu,** aby się wylogować i wykonać kolejne czynności.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą Eksploratora IoT platformy Azure

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Aby dodać repozytorium firmowe, wybierz **pozycję Ustawienia**, a następnie **+ Dodaj źródło definicji modułu**, a następnie **repozytorium firmowe**. Dodaj ciąg połączenia repozytorium modelu firmy i wybierz pozycję **Zapisz i połącz**.

1. Na stronie **Przegląd urządzeń** znajdź wcześniej utworzoną tożsamość urządzenia. Gdy aplikacja urządzenia nadal działa w wierszu polecenia, sprawdź, czy **stan połączenia** urządzenia w Eksploratorze Usługi Azure IoT jest raportowanie jako _Połączone_ (jeśli nie, naciśnij **odśwież,** dopóki nie jest). Wybierz urządzenie, aby wyświetlić więcej szczegółów.

1. Rozwiń interfejs z urna:<**YOUR_INTERFACE_NAME>:EnvironmentalSensor:1,** aby zobaczyć IoT Plug and Play prymitywów - właściwości, polecenia i telemetrii. Nazwa interfejsu, która pojawi się jest nazwa włożona podczas tworzenia modelu.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć urządzenie Typu Plug and Play usługi IoT przy użyciu kontrolera DCM.

Aby dowiedzieć się więcej o dcms i jak tworzyć własne modele, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i testowanie modelu możliwości urządzenia przy użyciu kodu programu Visual Studio](tutorial-pnp-visual-studio-code.md)
