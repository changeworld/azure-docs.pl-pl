---
title: Tworzenie urządzeń W wersji Zapoznat i wersji Play za pomocą programu Visual Studio i programu Visual Studio Code | Dokumenty firmy Microsoft
description: Użyj visual studio i visual studio kod, aby przyspieszyć tworzenie modeli urządzeń IoT Plug and Play i zaimplementowanie kodu urządzenia.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 91e7b1c0be9a38c3d79440f07d944d182980dc10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159238"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>Tworzenie urządzeń typu Plug and Play za pomocą programu Visual Studio i programu Visual Studio

Narzędzia Usługi Azure IoT tools for Visual Studio Code zapewnia zintegrowane środowisko do tworzenia modeli możliwości urządzenia (DCM) i interfejsów, publikowania do repozytoriów modelu i generowania kodu szkieletu C w celu zaimplementowania aplikacji urządzenia.

W tym artykule wyjaśniono, jak:

- Generowanie kodu urządzenia i projektu aplikacji.
- Użyj wygenerowanego kodu w projekcie urządzenia.
- Iteracji przez regenerację kodu szkieletu.

Aby dowiedzieć się więcej na temat używania [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)kodu VS do tworzenia urządzeń IoT, zobacz .

## <a name="prerequisites"></a>Wymagania wstępne

Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/).

Aby zainstalować pakiet rozszerzeń w programie VS Code, należy wykonać następujące kroki.

1. W programie VS Code wybierz kartę **Rozszerzenia.**
1. Wyszukaj i zainstaluj **narzędzia Azure IoT Tools** z portalu Marketplace.

## <a name="generate-device-code-and-project"></a>Generowanie kodu urządzenia i projektu

W programie VS Code użyj **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń, wprowadź opcję **IoT Plug and Play**i wybierz pozycję **Wygeneruj skrót kodu urządzenia,** aby skonfigurować kod szkieletu i typ projektu. Na poniższej liście opisano szczegółowo każdy krok:

- **DCM do wygenerowania kodu**. Aby wygenerować kod urządzenia szkieletowego, otwórz folder zawierający dcm i pliki interfejsu, które implementuje. Jeśli generator kodu nie można znaleźć interfejs, który wymaga dcm, pobiera go z repozytorium modelu w razie potrzeby.

- **Język kodu urządzenia**. Obecnie generator kodu obsługuje tylko ANSI C.

- **Nazwa projektu**. Nazwa projektu jest używana jako nazwa folderu wygenerowanego kodu i innych plików projektu. Folder jest domyślnie umieszczany obok pliku DCM. Aby uniknąć konieczności ręcznego kopiowania wygenerowanego folderu kodu przy każdej aktualizacji kontrolera DCM i ponownego generowania kodu urządzenia, należy zachować plik DCM w tym samym folderze co folder projektu.

- **Metoda łączenia się z usługą Azure IoT**. Wygenerowane pliki zawierają również kod, aby skonfigurować urządzenie do łączenia się z usługą Azure IoT Hub. Możesz połączyć się bezpośrednio z [usługą Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) lub skorzystać z [usługi inicjowania obsługi administracyjnej urządzeń.](https://docs.microsoft.com/azure/iot-dps)

    - **Za pośrednictwem ciągu połączenia urządzenia Usługi IoT Hub:** określ parametry połączenia urządzenia dla aplikacji urządzenia, aby bezpośrednio połączyć się z Centrum IoT Hub.
    - **Za pomocą klucza symetrycznego DPS:** określ zakres **identyfikatora, klucz symetryczny** i **identyfikator urządzenia** dla aplikacji urządzenia, które są wymagane do łączenia się z centrum IoT hub lub IoT Central przy użyciu dps. **ID Scope**

- **Typ projektu**. Generator kodu generuje również projekt CMake lub Arduino. Obecnie obsługiwane typy projektów to:

    - **CMake Project w systemie Windows:** dla projektu urządzenia, który używa [CMake](https://cmake.org/) jako system kompilacji w systemie Windows. Ta opcja `CMakeLists.txt` jest generowany przy konfiguracjach SDK urządzenia w tym samym folderze co kod C.
    - **CMake Project na Linuksie:** dla projektu urządzenia, który używa [CMake](https://cmake.org/) jako systemu kompilacji na Linuksie. Ta opcja `CMakeLists.txt` jest generowany przy konfiguracjach SDK urządzenia w tym samym folderze co kod C.
    - **Projekt MXChip IoT DevKit:** dla projektu urządzenia, który działa na urządzeniu [MXChip IoT DevKit.](https://aka.ms/iot-devkit) Ta opcja generuje projekt Arduino, którego można [użyć w programie VS Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) lub w Arduino IDE do tworzenia i uruchamiania na urządzeniu IoT DevKit.

- **Typ SDK urządzenia**. Jeśli wybierzesz CMake jako typ projektu, jest to krok, aby skonfigurować, jak wygenerowany kod będzie zawierać zestaw SDK urządzenia Azure IoT C `CMakeLists.txt`w:

    - **Za pośrednictwem kodu źródłowego:** wygenerowany kod opiera się na [kod źródłowy sdk urządzenia](https://github.com/Azure/azure-iot-sdk-c) do uwzględnienia i kompilacji razem z nim. Jest to zalecane po dostosowaniu kodu źródłowego sdk urządzenia.
    - **Za pośrednictwem Vcpkg**: wygenerowany kod opiera się na [urządzeniu SDK Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) do uwzględnienia i kompilacji razem z nim. Jest to zalecany sposób dla urządzeń z systemem Windows, Linux lub macOS.

    > [!NOTE]
    > Obsługa systemu macOS dla urządzenia SDK Vcpkg urządzenia Usługi Azure IoT C działa w toku.

Generator kodu próbuje użyć DCM i plików interfejsu znajdujących się w folderze lokalnym. Jeśli pliki interfejsu nie znajdują się w folderze lokalnym, generator kodu wyszukuje je w repozytorium modelu publicznego lub repozytorium modelu firmy. [Typowe pliki interfejsu](./concepts-common-interfaces.md) są przechowywane w repozytorium modelu publicznego.

Po zakończeniu generowania kodu rozszerzenie otwiera nowe okno kodu VS z kodem. Jeśli otworzysz wygenerowany plik, taki jak **main.c,** może się okazać, że IntelliSense zgłasza, że nie może otworzyć plików źródłowych SDK C. Aby włączyć poprawną nawigację intellisense i kodu, należy wykonać następujące kroki, aby uwzględnić źródło SDK języka C:

1. W programie VS Code użyj **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń, wpisz i wybierz **C/C++: Edytuj konfiguracje (JSON),** aby otworzyć plik **c_cpp_properties.json.**

1. Dodaj ścieżkę sdk urządzenia `includePath` w sekcji:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Zapisz plik.

## <a name="use-the-generated-code"></a>Użyj wygenerowanego kodu

W poniższych instrukcjach opisano sposób używania wygenerowanego kodu w projekcie urządzenia na różnych platformach maszyn deweloperskich. Instrukcje zakładają, że używasz ciągu połączenia urządzenia Usługi IoT hub z wygenerowanym kodem:

### <a name="linux"></a>Linux

Aby zbudować kod urządzenia wraz z urządzeniem C SDK Vcpkg przy użyciu CMake w środowisku Linux, takim jak Ubuntu lub Debian:

1. Otwórz aplikację terminala.

1. Zainstaluj **GCC**, `cmake` **Git**, i `apt-get` wszystkie zależności za pomocą polecenia:

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Sprawdź, czy `cmake` wersja jest powyżej **2.8.12,** a wersja **GCC** jest powyżej **4.4.7**.

    ```bash
    cmake --version
    gcc --version
    ```

1. Zainstaluj Vcpkg:

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    Następnie, aby podłączyć [integrację](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)całego użytkownika, uruchom:

    ```bash
    ./vcpkg integrate install
    ```

1. Zainstaluj zestaw Vcpkg urządzenia usługi Azure IoT C:

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Utwórz `cmake` podkatalog w folderze zawierający wygenerowany skrót kodu i przejdź do tego folderu:

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby użyć CMake do tworzenia sdk urządzenia i wygenerowany skrót kodu:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. Po pomyślnym zakończeniu kompilacji uruchom aplikację określającą parametry połączenia urządzenia usługi IoT Hub jako parametr.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Aby utworzyć kod urządzenia wraz z zestawem SDK C urządzenia w systemie Windows przy użyciu CMake i kompilatorów Visual Studio C/C++ w wierszu polecenia, zobacz [szybki start i szybkiego startu IoT Plug and Play](./quickstart-create-pnp-device-windows.md). Poniższe kroki pokazują, jak skompilować kod urządzenia wraz z urządzeniem C SDK Vcpkg jako projekt CMake w programie Visual Studio.

1. Wykonaj kroki opisane w [przewodniku Szybki start,](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) aby zainstalować zestaw SDK urządzenia Usługi Azure IoT dla języka C za pośrednictwem vcpkg.

1. Zainstaluj [program Visual Studio 2019 (Społeczność, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) — upewnij się, że dołączy do nich składnik Menedżera **pakietów NuGet** i deweloper pulpitu z obciążeniem **C++.**

1. Otwórz program Visual Studio, wybierz **polecenie Plik > Otwórz > CMake...,** aby otworzyć `CMakeLists.txt` w folderze wygenerowany kod.

1. Na pasku narzędzi **Ogólne** znajdź **menu rozwijane Konfiguracje.** Wybierz **pozycję Zarządzaj konfiguracją,** aby dodać ustawienie CMake dla projektu.

    ![Zarządzanie konfiguracją](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. W **ustawieniach CMake**dodaj nową konfigurację i wybierz **x86-Debug** jako miejsce docelowe.

1. W **CMake Command Arguments**dodaj następujący wiersz:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Zapisz plik.

1. Przełącz się na **x86-Debug** w **konfiguracji** listy rozwijanej. Potrzebuje kilku sekund dla CMake do generowania pamięci podręcznej dla niego. Wyświetl okno Dane wyjściowe, aby wyświetlić postęp.

    ![CZrobo dane wyjściowe](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. W **Eksploratorze rozwiązań** `CMakeLists.txt` kliknij prawym przyciskiem myszy folder główny i wybierz **polecenie Buduj** z menu kontekstowego, aby utworzyć wygenerowany kod skrótowy za pomocą sdk urządzenia.

1. Po pomyślnym kompilacji w wierszu polecenia uruchom aplikację określającą parametry połączenia urządzenia usługi IoT Hub jako parametr.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Aby dowiedzieć się więcej na temat korzystania z CMake w programie Visual Studio, zobacz [Tworzenie projektu CMake](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

Poniższe kroki pokazują, jak skompilować kod urządzenia wraz z kodem źródłowym SDK C urządzenia w systemie macOS przy użyciu CMake:

1. Otwórz aplikację terminala.

1. Użyj [Homebrew,](https://homebrew.sh) aby zainstalować wszystkie zależności:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Sprawdź, czy [CMake](https://cmake.org/) jest co najmniej w wersji **2.8.12:**

    ```bash
    cmake --version
    ```

1. [Patch CURL](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) do najnowszej dostępnej wersji.

1. W folderze zawierającym wygenerowany kod sklonuj repozytorium [SDK języka Azure IoT C:](https://github.com/Azure/azure-iot-sdk-c)

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

1. Utwórz folder `cmake` o nazwie w folderze zawierającym wygenerowany kod i przejdź do tego folderu.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby użyć CMake do tworzenia sdk urządzenia i wygenerowany skrót kodu:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. Po pomyślnym zakończeniu kompilacji uruchom aplikację określającą parametry połączenia urządzenia usługi IoT Hub jako parametr.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Iteracji poprzez regenerację kodu szkieletu

Generator kodu można ponownie wygenerować kod, jeśli zaktualizujesz dcm lub pliki interfejsu. Zakładając, że kod urządzenia został już wygenerowany z pliku DCM, aby ponownie wygenerować kod:

1. Po otwarciu folderu z otwartymi plikami DCM użyj **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń, wprowadź **polecenie IoT Plug and Play**i wybierz pozycję **Generuj skrót kodu urządzenia**.

1. Wybierz zaktualizowany plik DCM.

1. Wybierz **pozycję Ponownie wygeneruj kod dla {nazwa projektu}**.

1. Generator kodu używa poprzedniego ustawienia, które zostały skonfigurowane i ponownie generuje kod. Jednak nie zastępuje plików, które mogą zawierać kod użytkownika, takich jak `main.c` i `{project_name}_impl.c`.

> [!NOTE]
> Jeśli zaktualizujesz identyfikator URN w pliku interfejsu, jest traktowany jako nowy interfejs. Podczas ponownego generowania kodu generator kodu generuje kod dla interfejsu, ale nie zastępuje `{project_name}_impl.c` oryginalnego w pliku.

## <a name="problems-and-feedback"></a>Problemy i opinie

Narzędzia Azure IoT tools to projekt typu open source w usłudze GitHub. W przypadku jakichkolwiek problemów i żądań funkcji można [utworzyć problem w usłudze GitHub](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Następne kroki

W tym artykule in how-to, masz dowiesz się, jak używać Visual Studio i Visual Studio Code do generowania kodu szkieletu C do zaimplementowania aplikacji urządzenia. Sugerowany następny krok jest, aby dowiedzieć się, jak zainstalować i używać narzędzia [do eksploratora Usługi Azure IoT.](./howto-install-iot-explorer.md)
