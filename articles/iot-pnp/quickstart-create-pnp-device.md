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
ms.openlocfilehash: 6e5e08df444f66f2c5500d968c805552d20901c5
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861208"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-device"></a>Szybki start: Tworzenie urządzenia Plug and Play IoT przy użyciu modelu możliwości urządzenia

_Model możliwości urządzenia_ (DCM) opisuje możliwości urządzenia Plug and Play IoT. DCM jest często skojarzony z jednostką SKU produktu. Możliwości zdefiniowane w DCM są zorganizowane w interfejsy wielokrotnego użytku. Można wygenerować kod urządzenia szkieletowego na podstawie DCM. W tym przewodniku szybki start pokazano, jak za pomocą usługi VS Code utworzyć urządzenie usługi IoT Plug and Play przy użyciu DCM.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz zainstalować następujące oprogramowanie na komputerze lokalnym:

* [Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) — upewnij się, że podczas instalowania programu Visual Studio dołączysz składnik **Menedżera pakietów NuGet** i **Programowanie aplikacji C++ klasycznych** .
* [Git](https://git-scm.com/download/).
* [CMAKE](https://cmake.org/download/).
* [Program Visual Studio Code](https://code.visualstudio.com/)

### <a name="install-azure-iot-tools"></a>Instalowanie narzędzi Azure IoT Tools

Wykonaj następujące kroki, aby zainstalować pakiet Microsoft [Azure IoT Tools for vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack:

1. W VS Code wybierz kartę **rozszerzenia** .
1. Wyszukaj **narzędzia Azure IoT Tools**.
1. Wybierz pozycję **Zainstaluj**.

### <a name="install-the-azure-iot-explorer"></a>Instalowanie programu Azure IoT Explorer

Pobierz i zainstaluj narzędzie Azure IoT Explorer na stronie [najnowszej wersji](https://github.com/Azure/azure-iot-explorer/releases) .

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

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

### <a name="get-azure-iot-device-sdk-for-c"></a>Pobierz zestaw SDK urządzeń Azure IoT dla języka C

W tym przewodniku szybki start przygotowano środowisko programistyczne, którego można użyć do klonowania i kompilowania zestawu SDK urządzeń usługi Azure IoT C.

1. Otwórz wiersz polecenia. Wykonaj następujące polecenie, aby sklonować repozytorium GitHub [zestawu SDK języka C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

1. `pnp_app` Utwórz podkatalog w katalogu głównym lokalnego klonu repozytorium. Ten folder służy do plików modelu urządzenia i szczątkowego kodu urządzenia.

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir pnp_app
    ```

## <a name="author-your-model"></a>Tworzenie modelu

W tym przewodniku szybki start użyjesz istniejącego modelu możliwości przykładowego urządzenia i skojarzonych interfejsów.

1. Pobierz przykładowy [model możliwości urządzenia](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) i [interfejs](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) i Zapisz pliki w `pnp_app` folderze.

    > [!TIP]
    > Aby pobrać plik z usługi GitHub, przejdź do pliku, kliknij prawym przyciskiem myszy pozycję **RAW**, a następnie wybierz pozycję **Zapisz łącze jako**.

1. Otwórz `pnp_app` folder z vs Code. Pliki można wyświetlić przy użyciu funkcji IntelliSense:

    ![Model możliwości urządzenia](media/quickstart-create-pnp-device/dcm.png)

1. W pobranych plikach Zastąp `<YOUR_COMPANY_NAME_HERE>` `@id` pola i `schema` unikatowymi wartościami. Używaj tylko znaków a-z, A-Z, 0-9 i znaku podkreślenia. Aby uzyskać więcej informacji, zobacz [Format identyfikatora cyfrowego przędzy](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generuj procedurę tworzenia kodu w języku C

Teraz masz już DCM i powiązane z nim interfejsy, można wygenerować kod urządzenia, który implementuje model. Aby wygenerować skrót kodu C w programie VS Code:

1. Po otwarciu folderu z plikami DCM Użyj **kombinacji klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń, wprowadź **Plug and Play IoT**i wybierz pozycję **Generuj procedurę tworzenia kodu urządzenia**.

    > [!NOTE]
    > Przy pierwszym użyciu narzędzia generatora kodu Plug and Play IoT trwa kilka sekund.

1. Wybierz plik DCM, którego chcesz użyć do wygenerowania szczątkowego kodu urządzenia.

1. Wprowadź nazwę projektu **sample_device**, będzie to nazwa aplikacji urządzenia.

1. Wybierz **ANSI C** jako język.

1. Wybierz **projekt CMAKE** jako typ projektu.

1. Wybierz pozycję **przez IoT Hub parametry połączenia urządzenia** jako metodę połączenia.

1. VS Code otwiera nowe okno z wygenerowanymi plikami zastępczymi kodu urządzenia.
    ![Kod urządzenia](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-the-code"></a>Kompilowanie kod

Zestaw SDK urządzenia służy do tworzenia wygenerowanej klasy urządzenia. Utworzona Aplikacja symuluje urządzenie, które nawiązuje połączenie z usługą IoT Hub. Aplikacja wysyła dane telemetryczne i właściwości oraz odbiera polecenia.

1. W vs Code Otwórz `CMakeLists.txt` w folderze głównym zestawu SDK urządzeń.

1. Dodaj poniższy wiersz w dolnej części `CMakeLists.txt` pliku, aby uwzględnić folder zastępczy kodu urządzenia podczas kompilowania:

    ```txt
    add_subdirectory(pnp_app/sample_device)
    ```

1. Utwórz podkatalog CMAKE w folderze głównym zestawu SDK urządzeń i przejdź do tego folderu:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby skompilować zestaw SDK urządzeń i wygenerowany skrót kodu:

    ```cmd\sh
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Jeśli CMAKE nie może znaleźć C++ kompilatora, podczas uruchamiania poprzedniego polecenia pojawiają się błędy kompilacji. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Po pomyślnym zakończeniu kompilacji Uruchom aplikację przekazującą parametry połączenia urządzenia usługi IoT Hub jako parametr.

    ```cmd\sh
    cd azure-iot-sdk-c\cmake\pnp_app\sample_device\Release\
    sample_device.exe "[IoT Hub device connection string]"
    ```

1. Aplikacja urządzenia uruchamia wysyłanie danych do IoT Hub.

    ![Uruchomiono aplikację urządzenia](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Sprawdzanie poprawności kodu

### <a name="publish-device-model-files-to-model-repository"></a>Publikuj pliki modelu urządzenia w repozytorium modelu

Aby sprawdzić poprawność kodu urządzenia za pomocą programu **Azure IoT Explorer**, należy opublikować pliki w repozytorium modeli.

1. Po otwarciu folderu z plikami DCM Użyj **kombinacji klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń, wpisz i **wybierz pozycję IoT Plug & Play: Prześlij pliki do repozytorium**modeli.

1. Wybierz `SampleDevice.capabilitymodel.json` pozycję `EnvironmentalSensor.interface.json` i.

1. Wprowadź parametry połączenia repozytorium modelu firmy.

    > [!NOTE]
    > Parametry połączenia są wymagane tylko podczas pierwszego połączenia z repozytorium.

1. W oknie danych wyjściowych i powiadomieniu VS Code można sprawdzić, czy pliki zostały pomyślnie opublikowane.

    > [!NOTE]
    > W przypadku wystąpienia błędów podczas publikowania plików modelu urządzenia można spróbować użyć polecenia **IoT Plug and Play: Wyloguj repozytorium** modeli, aby się wylogować, i wykonaj kroki ponownie.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

1. Otwórz program Azure IoT Explorer, zobaczysz stronę **konfiguracje aplikacji** .

1. Wprowadź parametry połączenia IoT Hub i kliknij przycisk **Połącz**.

1. Po nawiązaniu połączenia zostanie wyświetlona strona Przegląd urządzenia.

1. Aby dodać repozytorium firmy, wybierz opcję **Ustawienia**, a następnie pozycję **+ Nowy**, a następnie pozycję **repozytorium firmy**.

1. Dodaj parametry połączenia repozytorium modelu firmy. Wybierz przycisk **Połącz**.

1. Na stronie Przegląd urządzenia Znajdź wcześniej utworzoną tożsamość urządzenia i wybierz ją, aby wyświetlić więcej szczegółów.

1. Rozwiń interfejs o IDENTYFIKATORze **urn: azureiot: EnvironmentalSensor: 1** , aby wyświetlić elementy podstawowe Plug and Play IoT — właściwości, polecenia i dane telemetryczne.

1. Wybierz stronę **telemetrii** , aby wyświetlić dane telemetryczne wysyłane przez urządzenie.

1. Wybierz stronę **właściwości (bez możliwości zapisu)** , aby wyświetlić właściwości, które nie zostały zapisywalne zgłoszone przez urządzenie.

1. Wybierz stronę **właściwości (zapisywalne)** , aby wyświetlić właściwości do zapisu, które można zaktualizować.

1. Rozwiń węzeł **Nazwa**właściwości, Aktualizuj przy użyciu nowej nazwy i wybierz opcję **Aktualizuj modyfikowalną Właściwość**. 
2. Aby zobaczyć, że nowa nazwa jest wyświetlana w kolumnie **Właściwość raportowana** , kliknij przycisk **Odśwież** w górnej części strony.

1. Wybierz stronę **polecenia** , aby wyświetlić wszystkie polecenia obsługiwane przez urządzenie.

1. Rozwiń polecenie **Blink** i Ustaw nowy przedział czasu. Wybierz pozycję **Wyślij polecenie** , aby wywołać polecenie na urządzeniu.

1. Przejdź do symulowanego urządzenia, aby sprawdzić, czy polecenie zostało wykonane zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia urządzenia Plug and Play IoT przy użyciu DCM.

Aby dowiedzieć się więcej o usłudze IoT Plug and Play, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie i testowanie modelu możliwości urządzenia przy użyciu Visual Studio Code](tutorial-pnp-visual-studio-code.md)
