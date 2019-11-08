---
title: Tworzenie urządzenia w wersji zapoznawczej IoT Plug and Play | Microsoft Docs
description: Użyj modelu możliwości urządzenia do generowania kodu urządzenia. Następnie uruchom kod urządzenia i sprawdź, czy urządzenie jest połączone z IoT Hub.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 019dbe8b977932c6a806f7efca8c0724597718d8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818071"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Szybki Start: korzystanie z modelu możliwości urządzenia do tworzenia urządzenia w wersji zapoznawczej IoT Plug and Play (Windows)

_Model możliwości urządzenia_ (DCM) opisuje możliwości urządzenia Plug and Play IoT. DCM jest często skojarzony z jednostką SKU produktu. Możliwości zdefiniowane w DCM są zorganizowane w interfejsy wielokrotnego użytku. Można wygenerować kod urządzenia szkieletowego na podstawie DCM. W tym przewodniku szybki start pokazano, jak używać usługi VS Code w systemie Windows do tworzenia urządzeń Plug and Play IoT przy użyciu DCM.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz zainstalować następujące oprogramowanie na komputerze lokalnym:

* [Narzędzia kompilacji dla programu Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) z  **C++ narzędziami kompilacji** i obciążeniami **składników Menedżera pakietów NuGet** . Lub jeśli masz już [program Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 lub 2015 z zainstalowanymi takimi samymi obciążeniami.
* [Git](https://git-scm.com/download/).
* [CMAKE](https://cmake.org/download/).
* [Program Visual Studio Code](https://code.visualstudio.com/)

### <a name="install-azure-iot-tools"></a>Instalowanie narzędzi Azure IoT Tools

Wykonaj następujące kroki, aby zainstalować pakiet Microsoft [Azure IoT Tools for vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack:

1. W VS Code wybierz kartę **rozszerzenia** .
1. Wyszukaj **narzędzia Azure IoT Tools**.
1. Wybierz pozycję **Zainstaluj**.

### <a name="install-the-azure-iot-explorer"></a>Instalowanie programu Azure IoT Explorer

Pobierz i zainstaluj najnowszą wersję programu **Azure IoT Explorer** ze strony [repozytorium](https://github.com/Azure/azure-iot-explorer/releases) narzędzi, wybierając plik msi w obszarze "zasoby" dla najnowszej aktualizacji.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Pobierz parametry połączenia dla repozytorium modelu firmy

_Parametry połączenia repozytorium modelu firmy_ można znaleźć w portalu [Azure Certified for IoT Portal](https://preview.catalog.azureiotsolutions.com) , gdy zalogujesz się przy użyciu konta służbowego firmy Microsoft lub identyfikatora partnera firmy Microsoft, jeśli go masz. Po zalogowaniu wybierz pozycję **repozytorium firmowe** , a następnie **Parametry połączenia**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Przygotowywanie Centrum IoT Hub

Aby ukończyć ten przewodnik Szybki Start, potrzebujesz również usługi Azure IoT Hub w ramach subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> W publicznej wersji zapoznawczej funkcje Plug and Play IoT są dostępne tylko w centrach IoT, które zostały utworzone w regionach **środkowe stany USA**, **Europa Północna**i **Japonia Wschodnia** .

Dodaj Microsoft Azure rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Uruchom następujące polecenie, aby utworzyć tożsamość urządzenia w centrum IoT Hub. Zastąp symbole zastępcze **YourIoTHubName** i **YourDevice** własnymi nazwami. Jeśli nie masz IoT Hub, postępuj zgodnie [z poniższymi instrukcjami, aby go utworzyć](../iot-hub/iot-hub-create-using-cli.md):

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Uruchom następujące polecenia, aby uzyskać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Uruchom następujące polecenia, aby uzyskać _Parametry połączenia usługi IoT Hub_ dla centrum:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

```json
HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}
```

Ta wartość zostanie użyta w dalszej części przewodnika Szybki Start.

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

### <a name="get-azure-iot-device-sdk-for-c"></a>Pobierz zestaw SDK urządzeń Azure IoT dla języka C

W tym przewodniku szybki start przygotujesz środowisko programistyczne, instalując zestaw SDK urządzeń Azure IoT C za pośrednictwem [Vcpkg](https://github.com/microsoft/vcpkg).

1. Otwórz wiersz polecenia. Wykonaj następujące polecenie, aby zainstalować Vcpkg:

    ```cmd/sh
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Następnie, aby podłączyć [integrację](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)obejmującą wiele użytkowników, uruchom następujące polecenie (Uwaga: wymaga od administratora przy pierwszym użyciu):

    ```cmd/sh
    .\vcpkg.exe integrate install
    ```

1. Zainstaluj zestaw SDK urządzeń usługi Azure IoT C Vcpkg:

    ```cmd/sh
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>Tworzenie modelu

W tym przewodniku szybki start użyjesz istniejącego modelu możliwości przykładowego urządzenia i skojarzonych interfejsów.

1. Utwórz katalog `pnp_app` na dysku lokalnym.

1. Pobierz przykładowy [model możliwości urządzenia](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) i [interfejs](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) i zapisz pliki w folderze `pnp_app`.

    > [!TIP]
    > Aby pobrać plik z usługi GitHub, przejdź do pliku, kliknij prawym przyciskiem myszy pozycję **RAW**, a następnie wybierz pozycję **Zapisz łącze jako**.

1. Otwórz `pnp_app` folder z VS Code. Pliki można wyświetlić przy użyciu funkcji IntelliSense:

    ![Model możliwości urządzenia](media/quickstart-create-pnp-device/dcm.png)

1. W pobranych plikach Zastąp `<YOUR_COMPANY_NAME_HERE>` w polach `@id` i `schema` z unikatową wartością. Używaj tylko znaków a-z, A-Z, 0-9 i znaku podkreślenia. Aby uzyskać więcej informacji, zobacz [Format identyfikatora cyfrowego przędzy](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generuj procedurę tworzenia kodu w języku C

Teraz, gdy masz już DCM i skojarzone z nią interfejsy, możesz wygenerować kod urządzenia, który implementuje model. Aby wygenerować skrót kodu C w VS Code:

1. Po otwarciu folderu z plikami DCM Użyj **kombinacji klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń, wprowadź **Plug and Play IoT**i wybierz pozycję **Generuj procedurę tworzenia kodu urządzenia**.

    > [!NOTE]
    > Przy pierwszym użyciu interfejsu wiersza polecenia IoT Plug and Play CodeGen można pobrać i zainstalować automatycznie kilka sekund.

1. Wybierz plik DCM, którego chcesz użyć do wygenerowania szczątkowego kodu urządzenia.

1. Wprowadź nazwę projektu **sample_device**, będzie to nazwa aplikacji urządzenia.

1. Wybierz **ANSI C** jako język.

1. Wybierz pozycję **przez IoT Hub parametry połączenia urządzenia** jako metodę połączenia.

1. Wybierz **projekt CMAKE w systemie Windows** jako szablon projektu.

1. Wybierz pozycję **Via Vcpkg** , aby uwzględnić zestaw SDK urządzeń.

1. Nowy folder o nazwie **sample_device** jest tworzony w tej samej lokalizacji, w której znajduje się plik DCM, a w tym wygenerowane pliki zastępcze kodu urządzenia. VS Code otwiera nowe okno, aby je wyświetlić.
    ![](media/quickstart-create-pnp-device/device-code.png) kod urządzenia

## <a name="build-the-code"></a>Kompilowanie kod

Utworzoną procedurę tworzenia kodu urządzenia tworzy się wraz z zestawem SDK urządzeń. Utworzona Aplikacja symuluje urządzenie, które nawiązuje połączenie z usługą IoT Hub. Aplikacja wysyła dane telemetryczne i właściwości oraz odbiera polecenia.

1. Utwórz podkatalog `cmake` w folderze `sample_device` i przejdź do tego folderu:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby utworzyć wytworzoną procedurę tworzenia kodu (zastępując symbol zastępczy katalogiem repozytorium Vcpkg):

    ```cmd\sh
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Jeśli używasz programu Visual Studio 2017 lub 2015, musisz określić Generator CMake na podstawie używanych narzędzi kompilacji:
    >```cmd\sh
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Jeśli CMAKE nie może znaleźć C++ kompilatora, podczas uruchamiania poprzedniego polecenia pojawiają się błędy kompilacji. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Po pomyślnym zakończeniu kompilacji Uruchom aplikację, przekazując parametry połączenia urządzenia usługi IoT Hub jako parametr.

    ```cmd\sh
    .\Debug\sample_device.exe "[IoT Hub device connection string]"
    ```

1. Aplikacja urządzenia uruchamia wysyłanie danych do IoT Hub.

    ![Uruchomiono aplikację urządzenia](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Sprawdzanie poprawności kodu

### <a name="publish-device-model-files-to-model-repository"></a>Publikuj pliki modelu urządzenia w repozytorium modelu

Aby sprawdzić poprawność kodu urządzenia za pomocą programu **Azure IoT Explorer**, należy opublikować pliki w repozytorium modeli.

1. Po otwarciu folderu z plikami DCM w VS Code Użyj **kombinacji klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń, wpisz i wybierz pozycję **IoT plug & Play: Prześlij pliki do repozytorium modeli**.

1. Wybierz `SampleDevice.capabilitymodel.json` i `EnvironmentalSensor.interface.json` pliki.

1. Wprowadź parametry połączenia repozytorium modelu firmy.

    > [!NOTE]
    > Parametry połączenia są wymagane tylko podczas pierwszego połączenia z repozytorium.

1. W VS Code oknie danych wyjściowych i powiadomieniu można sprawdzić, czy pliki zostały pomyślnie opublikowane.

    > [!NOTE]
    > W przypadku wystąpienia błędów podczas publikowania plików modelu urządzenia można spróbować użyć polecenia **IoT Plug and Play: Wyloguj repozytorium modeli** , aby się wylogować, i wykonaj kroki ponownie.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

1. Otwórz program Azure IoT Explorer, zobaczysz stronę **konfiguracje aplikacji** .

1. Wprowadź parametry połączenia IoT Hub i kliknij przycisk **Połącz**.

1. Po nawiązaniu połączenia zostanie wyświetlona strona Przegląd urządzenia.

1. Aby dodać repozytorium firmy, wybierz pozycję **Ustawienia**, a następnie **+ Dodaj źródło definicji modułu**, a następnie pozycję **repozytorium firmy**. Dodaj parametry połączenia repozytorium modelu firmy i wybierz pozycję **Zapisz i Połącz**.

1. Na stronie Przegląd urządzenia Znajdź wcześniej utworzoną tożsamość urządzenia i wybierz ją, aby wyświetlić więcej szczegółów.

1. Rozwiń interfejs o IDENTYFIKATORze **urn: < YOUR_INTERFACE_NAME >: EnvironmentalSensor: 1** , aby wyświetlić elementy podstawowe Plug and Play IoT — właściwości, polecenia i dane telemetryczne. Nazwa interfejsu, która zostanie wyświetlona, to nazwa wprowadzona podczas tworzenia modelu.

1. Wybierz stronę **Telemetria** i zacznij od _początku_ , aby wyświetlić dane telemetryczne wysyłane przez urządzenie.

1. Wybierz stronę **właściwości (bez możliwości zapisu)** , aby wyświetlić właściwości, które nie zostały zapisywalne zgłoszone przez urządzenie.

1. Wybierz stronę **właściwości (zapisywalne)** , aby wyświetlić właściwości do zapisu, które można zaktualizować.

1. Rozwiń węzeł **Nazwa**właściwości, Aktualizuj przy użyciu nowej nazwy i wybierz opcję **Aktualizuj modyfikowalną Właściwość**.

1. Aby zobaczyć, że nowa nazwa jest wyświetlana w kolumnie **Właściwość raportowana** , kliknij przycisk **Odśwież** w górnej części strony.

1. Wybierz stronę **polecenia** , aby wyświetlić wszystkie polecenia obsługiwane przez urządzenie.

1. Rozwiń polecenie **Blink** i Ustaw nowy przedział czasu. Wybierz pozycję **Wyślij polecenie** , aby wywołać polecenie na urządzeniu.

1. Przejdź do symulowanego urządzenia, aby sprawdzić, czy polecenie zostało wykonane zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia urządzenia Plug and Play IoT przy użyciu DCM.

Aby dowiedzieć się więcej o usłudze IoT Plug and Play, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie i testowanie modelu możliwości urządzenia przy użyciu Visual Studio Code](tutorial-pnp-visual-studio-code.md)
