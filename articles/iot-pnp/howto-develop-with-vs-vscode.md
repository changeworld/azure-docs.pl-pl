---
title: Korzystanie z programu Visual Studio i Visual Studio Code do kompilowania urządzeń w wersji zapoznawczej IoT Plug and Play | Microsoft Docs
description: Użyj programu Visual Studio i Visual Studio Code, aby przyspieszyć tworzenie modeli urządzeń Plug and Play IoT i implementowanie kodu urządzenia.
author: liydu
ms.author: liydu
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d68a3f99096ca64b357239f61cf7ff17d6fc3f83
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935731"
---
# <a name="use-azure-iot-tools-for-visual-studio-code"></a>Korzystanie z narzędzi Azure IoT Tools for Visual Studio Code

Narzędzia Azure IoT Tools for Visual Studio Code zapewniają zintegrowane środowisko do tworzenia modeli możliwości urządzeń (DCM) i interfejsów, publikowania w repozytoriach modeli i generowania szkieletowego kodu C w celu zaimplementowania aplikacji urządzenia.

W tym artykule pokazano, jak:

- Generuj kod urządzenia i projekt aplikacji.
- Użyj wygenerowanego kodu w projekcie urządzenia.
- Wykonaj iterację, ponownie generując kod szkieletowy.

Aby dowiedzieć się więcej o używaniu VS Code do tworzenia urządzeń IoT [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench), zobacz.

## <a name="prerequisites"></a>Wymagania wstępne

Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/).

Wykonaj następujące kroki, aby zainstalować pakiet rozszerzeń w VS Code.

1. W VS Code wybierz pozycję Karta **rozszerzenia** .
1. Wyszukaj i zainstaluj **narzędzia Azure IoT Tools** z portalu Marketplace.

## <a name="generate-device-code-and-project"></a>Generuj kod i projekt urządzenia

W VS Code **naciśnij kombinację klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń, wprowadź **Plug and Play IoT**i wybierz opcję **Generuj procedurę tworzenia kodu urządzenia** , aby skonfigurować kod szkieletu i typ projektu. Na poniższej liście opisano szczegółowo każdy krok:

- **Plik DCM, który ma zostać użyty do wygenerowania kodu**. Aby wygenerować kod urządzenia szkieletowego, Otwórz folder zawierający pliki DCM i interfejsu, które implementuje. Jeśli generator kodu nie może znaleźć interfejsu wymaganego przez DCM, pobiera go z repozytorium modeli zgodnie z wymaganiami.

- **Język kodu urządzenia**. Obecnie generator kodu obsługuje tylko ANSI C.

- **Nazwa projektu**. Nazwa projektu jest używana jako nazwa folderu dla wygenerowanego kodu i innych plików projektu. Folder jest domyślnie umieszczony obok pliku DCM. Aby uniknąć konieczności ręcznego kopiowania folderu kodu wygenerowanego za każdym razem, gdy zaktualizujesz DCM i ponownie wygenerujesz kod urządzenia, Zachowaj plik DCM w tym samym folderze, w którym znajduje się folder projektu.

- **Typ projektu**. Generator kodu generuje również plik projektu, dzięki czemu można zintegrować kod z własnym projektem lub projektem zestawu SDK urządzeń. Obecnie obsługiwane typy projektów są następujące:

    - **Projekt CMAKE**: dla projektu urządzenia, który używa [CMAKE](https://cmake.org/) jako systemu kompilacji. Ta opcja generuje `CMakeLists.txt` plik w tym samym folderze co kod języka C.
    - **Zestawu deweloperskiego IoT DevKit Project**: dla projektu urządzenia uruchomionego na urządzeniu [zestawu deweloperskiego IoT DevKit](https://aka.ms/iot-devkit) . Ta opcja generuje projekt Arduino, którego można [użyć w vs Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) lub w środowisku IDE Arduino do kompilowania i uruchamiania na urządzeniu IoT DevKit.

- **Metoda nawiązywania połączenia z usługą Azure IoT**. Wygenerowane pliki zawierają również kod umożliwiający skonfigurowanie urządzenia do nawiązywania połączenia z platformą Azure IoT Hub. Możesz wybrać opcję bezpośredniego połączenia z [platformą Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) lub użyć [usługi Device Provisioning](https://docs.microsoft.com/azure/iot-dps).

    - **Za pomocą IoT Hub parametry połączenia urządzenia**: Określ parametry połączenia urządzenia, które mają być połączone IoT Hub bezpośrednio.
    - **Za pośrednictwem klucza symetrycznego usługi DPS**: Określ **Identyfikator zakresu**, **Identyfikator rejestracji**i **klucz sygnatury dostępu współdzielonego** dla aplikacji urządzenia, które są wymagane do nawiązania połączenia z usługą IoT Hub lub IoT Central przy użyciu usługi DPS.

Generator kodu próbuje użyć DCM i plików interfejsu znajdujących się w folderze lokalnym. Jeśli pliki interfejsu nie znajdują się w folderze lokalnym, generator kodu szuka ich w repozytorium modelu publicznego lub repozytorium modelu firmy. [Pliki Common Interface](./concepts-common-interfaces.md) są przechowywane w repozytorium modelu publicznego.

Po zakończeniu generowania kodu rozszerzenie otwiera nowe okno VS Code z kodem. Jeśli otworzysz wygenerowany plik, taki jak **Main. c**, może się okazać, że technologia IntelliSense nie może otworzyć plików źródłowych zestawu c SDK. Aby włączyć poprawną funkcję IntelliSense i nawigowanie po kodzie, wykonaj następujące kroki, aby dołączyć źródło zestawu SDK języka C:

1. W VS Code Użyj **kombinacji klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń, wpisz **i wybierzC++pozycję C/: Edytuj konfiguracje (JSON)** , aby otworzyć plik **c_cpp_properties. JSON** .

1. Dodaj ścieżkę zestawu SDK urządzenia w `includePath` sekcji:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Zapisz plik.

## <a name="use-the-generated-code"></a>Użyj wygenerowanego kodu

Poniższe instrukcje opisują sposób używania wygenerowanego kodu w projekcie urządzenia na różnych platformach maszyn deweloperskich. W instrukcjach przyjęto założenie, że używasz IoT Hub parametrów połączenia urządzenia z wygenerowanym kodem:

### <a name="linux"></a>Linux

Aby skompilować kod urządzenia wraz z zestawem SDK urządzenia C przy użyciu CMake w środowisku systemu Linux, takim jak Ubuntu lub Debian:

1. Otwórz aplikację terminala.

1. Zainstaluj usługi w **zatoce**, `cmake` **git**i wszystkie zależności przy użyciu `apt-get` polecenia:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Sprawdź, czy wersja `cmake` programu jest powyżej **2.8.12** , a wersja **tej wersji jest większa** niż **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

1. Sklonuj repozytorium [zestawu SDK usługi Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) :

    ```sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

1. Skopiuj folder zawierający wygenerowany kod do folderu głównego zestawu SDK urządzeń.

1. W vs Code Otwórz `CMakeLists.txt` plik w folderze głównym zestawu SDK urządzeń.

1. Dodaj następujący wiersz na końcu `CMakeLists.txt` pliku, aby uwzględnić folder zastępczy kodu urządzenia podczas kompilowania zestawu SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Utwórz folder o nazwie `cmake` w folderze głównym zestawu SDK urządzenia i przejdź do tego folderu.

    ```sh
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby użyć CMake do skompilowania zestawu SDK urządzenia i wygenerowanej klasy zastępczej kodu:

    ```cmd\sh
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON ..
    cmake --build .
    ```

1. Po pomyślnym zakończeniu kompilacji Uruchom aplikację określającą IoT Hub parametry połączenia urządzenia jako parametr.

    ```cmd\sh
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Aby skompilować kod urządzenia wraz z zestawem SDK urządzenia w systemie Windows przy użyciu CMake i Visual Studio C/C++ kompilators w wierszu polecenia, zobacz [przewodnik Szybki Start Plug and Play IoT](./quickstart-create-pnp-device.md). Poniższe kroki pokazują, jak skompilować kod urządzenia wraz z zestawem SDK urządzenia jako CMake w programie Visual Studio.

1. Zainstaluj [program Visual Studio 2019 (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) — upewnij się, że dołączysz składnik **Menedżera pakietów NuGet** i **programowanie pulpitu z C++**  obciążeniem.

1. Otwórz program Visual Studio, a na stronie **wprowadzenie** wybierz pozycję **Klonuj lub Wyewidencjonuj kod**:

1. W obszarze **Lokalizacja repozytorium**Sklonuj repozytorium [zestawu SDK usługi Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) :

    ```txt
    https://github.com/Azure/azure-iot-sdk-c
    ```

    Należy oczekiwać, że wykonanie tej operacji trwa kilka minut, postęp można zobaczyć w okienku **Team Explorer** .

1. Otwórz repozytorium **Azure-IoT-SDK-c** w **Team Explorer**, wybierz pozycję **gałęzie**, Wyszukaj gałąź **publiczna w wersji zapoznawczej** i sprawdź ją.

    ![Publiczna wersja zapoznawcza](media/howto-develop-with-vs-vscode/vs-public-preview.png)

1. Skopiuj folder zawierający wygenerowany kod do folderu głównego zestawu SDK urządzeń.

1. `azure-iot-sdk-c` Otwórz folder w programie vs.

1. `CMakeLists.txt` Otwórz plik w folderze głównym zestawu SDK urządzeń.

1. Dodaj następujący wiersz na końcu `CMakeLists.txt` pliku, aby uwzględnić folder zastępczy kodu urządzenia podczas kompilowania zestawu SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Na pasku narzędzi **Ogólne** Znajdź listę rozwijaną **konfiguracje** . Wybierz pozycję **Zarządzaj konfiguracją** , aby dodać ustawienie CMAKE dla projektu.

    ![Zarządzanie konfiguracją](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. W **ustawieniach CMAKE**Dodaj nową konfigurację i wybierz pozycję **x64-Release** jako element docelowy.

1. W **argumentach polecenia CMAKE**Dodaj następujący wiersz:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Zapisz plik.

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem `CMakeLists.txt` myszy w folderze głównym, a następnie wybierz polecenie **Kompiluj** z menu kontekstowego, aby skompilować zestaw SDK urządzenia i wygenerowany kod zastępczy.

1. Po pomyślnym zakończeniu kompilacji, w wierszu polecenia, uruchom aplikację określającą IoT Hub parametry połączenia urządzenia jako parametr.

    ```cmd
    cd %USERPROFILE%\CMakeBuilds\{workspaceHash}\build\x64-Release\{generated_code_folder_name}\
    {generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Aby dowiedzieć się więcej o korzystaniu z CMake w programie Visual Studio, zobacz [Build CMAKE Project](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

Poniższe kroki pokazują, jak skompilować kod urządzenia wraz z zestawem SDK urządzenia dla macOS przy użyciu CMake:

1. Otwórz aplikację terminala.

1. Użyj [oprogramowania Homebrew](https://homebrew.sh) , aby zainstalować wszystkie zależności:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Sprawdź, czy [CMAKE](https://cmake.org/) jest co najmniej w wersji **2.8.12**:

    ```bash
    cmake --version
    ```

1. [Zazwinięcie poprawki](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) do najnowszej dostępnej wersji.

1. Sklonuj repozytorium [zestawu SDK usługi Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) :

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

1. Skopiuj folder zawierający wygenerowany kod do folderu głównego zestawu SDK urządzeń.

1. W vs Code Otwórz `CMakeLists.txt` plik w folderze głównym zestawu SDK urządzeń.

1. Dodaj następujący wiersz na końcu `CMakeLists.txt` pliku, aby uwzględnić folder zastępczy kodu urządzenia podczas kompilowania zestawu SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Utwórz folder o nazwie `cmake` w folderze głównym zestawu SDK urządzenia i przejdź do tego folderu.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby użyć CMake do skompilowania zestawu SDK urządzenia i wygenerowanej klasy zastępczej kodu:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. Po pomyślnym zakończeniu kompilacji Uruchom aplikację określającą IoT Hub parametry połączenia urządzenia jako parametr.

    ```bash
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Wykonaj iterację przez ponowne wygenerowanie kodu szkieletowego

Generator kodu może ponownie wygenerować kod, jeśli zaktualizujesz pliki DCM lub interfejsu. Przy założeniu, że kod urządzenia został już wygenerowany z pliku DCM, aby ponownie wygenerować kod:

1. Po otwarciu folderu z plikami DCM Użyj **kombinacji klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń, wprowadź **Plug and Play IoT**i wybierz pozycję **Generuj procedurę tworzenia kodu urządzenia**.

1. Wybierz zaktualizowany plik DCM.

1. Wybierz pozycję **ponownie Generuj kod dla {Nazwa projektu}** .

1. Generator kodu używa poprzedniego skonfigurowanego ustawienia i ponownie generuje kod. Nie zastępuje jednak plików, które mogą zawierać kod użytkownika, taki jak `main.c` i. `{project_name}_impl.c`

> [!NOTE]
> Jeśli zaktualizujesz identyfikator URN w pliku interfejsu, jest on traktowany jako nowy interfejs. Po ponownym wygenerowaniu kodu generator kodu generuje kod dla interfejsu, ale nie zastępuje oryginalnego `{project_name}_impl.c` pliku.

## <a name="problems-and-feedback"></a>Problemy i opinie

Narzędzia Azure IoT Tools to projekt Open Source w witrynie GitHub. W przypadku wszelkich problemów i żądań funkcji można [utworzyć problem w usłudze GitHub](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Następne kroki

W tym artykule poznasz sposób korzystania z programu Visual Studio i Visual Studio Code do generowania szkieletowego kodu C w celu zaimplementowania aplikacji urządzenia. Sugerowanym następnym krokiem jest zapoznanie się z tematem [Instalowanie i używanie narzędzia Azure IoT Explorer](./howto-install-iot-explorer.md) .
