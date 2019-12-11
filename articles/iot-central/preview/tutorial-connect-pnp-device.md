---
title: Samouczek — łączenie urządzenia IoT Plug and Play w wersji zapoznawczej na platformie Azure IoT Central
description: W tym samouczku pokazano, jak używać modelu możliwości urządzenia do generowania kodu urządzenia. Następnie uruchom kod urządzenia, sprawdź, czy urządzenie nawiązuje połączenie z aplikacją IoT Central, i użyj wygenerowanych widoków.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 2cb07a94b2ae85cc0755e1e7069a76e1ef2a5252
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977339"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-device-and-connect-it-to-your-iot-central-application"></a>Samouczek: Używanie modelu możliwości urządzenia do tworzenia urządzenia Plug and Play IoT i łączenia go z aplikacją IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

_Model możliwości urządzenia_ (DCM) opisuje możliwości urządzenia [Plug and Play IoT](../../iot-pnp/overview-iot-plug-and-play.md) . IoT Central może użyć DCM, aby utworzyć szablon urządzenia i wizualizacje dla urządzenia podczas łączenia się urządzenia po raz pierwszy.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Użyj Visual Studio Code, aby utworzyć urządzenie usługi IoT Plug and Play przy użyciu DCM.
> * Uruchom kod urządzenia w systemie Windows i sprawdź, czy jest on połączony z aplikacją IoT Central.
> * Wyświetl symulowane dane telemetryczne wysyłane przez urządzenie.

## <a name="prerequisites"></a>Wymagania wstępne

Ukończ Przewodnik Szybki Start dotyczący [tworzenia aplikacji IoT Central platformy Azure (funkcje w wersji zapoznawczej)](./quick-deploy-iot-central.md) , aby utworzyć aplikację IoT Central przy użyciu szablonu **aplikacji niestandardowy > aplikacji w wersji zapoznawczej** .

Aby ukończyć ten samouczek, należy zainstalować następujące oprogramowanie na komputerze lokalnym:

* [Narzędzia kompilacji dla programu Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) z  **C++ narzędziami kompilacji** i obciążeniami **składników Menedżera pakietów NuGet** . Lub jeśli masz już [program Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 lub 2015 z zainstalowanymi takimi samymi obciążeniami.
* [Git](https://git-scm.com/download/).
* [CMAKE](https://cmake.org/download/) — po zainstalowaniu **CMAKE**wybierz opcję **Dodaj CMAKE do ścieżki systemowej**.
* [Program Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* Narzędzie `dps-keygen`:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Instalowanie narzędzi Azure IoT Tools

Wykonaj następujące kroki, aby zainstalować pakiet rozszerzeń narzędzi Azure IoT Tools w VS Code:

1. W VS Code wybierz kartę **rozszerzenia** .
1. Wyszukaj **narzędzia Azure IoT Tools**.
1. Wybierz pozycję **Zainstaluj**.

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym samouczku za pomocą Menedżera bibliotek [Vcpkg](https://github.com/microsoft/vcpkg) można zainstalować zestaw SDK urządzeń Azure IoT C w środowisku deweloperskim.

1. Otwórz wiersz polecenia. Wykonaj następujące polecenie, aby zainstalować Vcpkg:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Następnie, aby podłączyć [integrację](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)obejmującą wiele użytkowników, uruchom następujące polecenie. Przy pierwszym uruchomieniu tego polecenia wymagane są prawa administracyjne:

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Zainstaluj zestaw SDK urządzeń usługi Azure IoT C Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Generuj klucz urządzenia

Aby podłączyć urządzenie do aplikacji IoT Central, wymagany jest klucz urządzenia. Aby wygenerować klucz urządzenia:

1. Zaloguj się do aplikacji IoT Central utworzonej przy użyciu niestandardowego szablonu **aplikacji > aplikacji w wersji zapoznawczej** w temacie [Tworzenie aplikacji IoT Central platformy Azure (funkcje w wersji zapoznawczej)](./quick-deploy-iot-central.md) .

1. Przejdź do strony **Administracja** i wybierz pozycję **połączenie z urządzeniem**.

1. Zanotuj **zakres identyfikatorów** i **klucz podstawowy** , który widzisz po wybraniu opcji **Wyświetl klucze**. Te wartości są używane w dalszej części tego samouczka.

    ![Połączenie z urządzeniem](./media/tutorial-connect-pnp-device/device-connection.png)

1. Otwórz wiersz polecenia i uruchom następujące polecenie, aby wygenerować klucz urządzenia:

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Zanotuj wygenerowany _klucz urządzenia_, Użyj tej wartości w kolejnym kroku w tym samouczku.

## <a name="download-your-model"></a>Pobierz model

W tym samouczku użyjesz publicznej DCM dla urządzenia zestawu deweloperskiego IoT DevKit. Do uruchomienia kodu nie jest potrzebne rzeczywiste urządzenie DevKit. w tym samouczku skompilujesz kod do uruchomienia w systemie Windows.

1. Utwórz folder o nazwie `central_app` i otwórz go w VS Code.

1. **Naciśnij klawisze Ctrl + Shift + P** , aby otworzyć paletę poleceń, wprowadź **Plug and Play IoT**i wybierz pozycję **Otwórz repozytorium modeli**. Wybierz pozycję **repozytorium publiczne**. VS Code przedstawia listę DCMs w repozytorium modelu publicznego.

1. Wybierz **zestawu deweloperskiego IoT DevKit** DCM z identyfikatorem `urn:mxchip:mxchip_iot_devkit:1`. Następnie wybierz pozycję **Pobierz**. Masz teraz kopię DCM w folderze `central_app`.

![Repozytorium modelu i DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> Aby można było korzystać z IoT Central, model możliwości urządzenia musi mieć wszystkie interfejsy zdefiniowane wewnętrznie w tym samym pliku.

## <a name="generate-the-c-code-stub"></a>Generuj procedurę tworzenia kodu w języku C

Teraz masz **zestawu deweloperskiego IoT DevKit** i skojarzone z nią interfejsy, można wygenerować kod urządzenia, który implementuje model. Aby wygenerować skrót kodu C w programie VS Code:

1. Po otwarciu folderu z plikami DCM Użyj **kombinacji klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń, wprowadź **Plug and Play IoT**i wybierz pozycję **Generuj procedurę tworzenia kodu urządzenia**.

    > [!NOTE]
    > Przy pierwszym użyciu narzędzia generatora kodu Plug and Play IoT trwa kilka sekund.

1. Wybierz pobrany plik DCM **zestawu deweloperskiego IoT DevKit** .

1. Wprowadź nazwę projektu **devkit_device**.

1. Wybierz **ANSI C** jako język.

1. Wybierz pozycję **za pośrednictwem usługi DPS (usługa Device Provisioning Service)** jako metodę połączenia.

1. Wybierz **projekt CMAKE w systemie Windows** jako typ projektu. Nie wybieraj **projektu zestawu deweloperskiego IoT DevKit**, ta opcja jest dostępna w przypadku, gdy masz prawdziwe urządzenie DevKit.

1. Wybierz pozycję **Via Vcpkg** , aby dodać zestaw SDK.

1. VS Code otwiera nowe okno z wygenerowanymi plikami zastępczymi kodu urządzenia w folderze `devkit_device`.

![Wygenerowany kod urządzenia](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>Kompilowanie kod

Zestaw SDK urządzenia służy do tworzenia wygenerowanej klasy urządzenia. Utworzona Aplikacja symuluje urządzenie **zestawu deweloperskiego IoT DevKit** i łączy się z aplikacją IoT Central. Aplikacja wysyła dane telemetryczne i właściwości, a następnie odbiera polecenia.

1. W wierszu polecenia Utwórz podkatalog `cmake` w folderze `devkit_device` i przejdź do tego folderu:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby utworzyć wytworzoną procedurę tworzenia kodu. Zastąp symbol zastępczy `<directory of your Vcpkg repo>` ścieżką do kopii repozytorium **Vcpkg** :

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Jeśli używasz programu Visual Studio 2017 lub 2015, musisz określić Generator CMake na podstawie narzędzi kompilacji, z których korzystasz:

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. Po pomyślnym zakończeniu kompilacji w tym samym wierszu polecenia Uruchom aplikację. Zastąp `<scopeid>` i `<devicekey>` wartościami zanotowanymi wcześniej:

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <devicekey>
    ```

1. Aplikacja urządzenia uruchamia wysyłanie danych do IoT Hub. Czasami zobaczysz błąd `Error registering device for DPS` przy pierwszym uruchomieniu poprzedniego polecenia. Jeśli ten błąd jest wyświetlany, spróbuj ponownie wykonać polecenie.

## <a name="view-the-device"></a>Wyświetlanie urządzenia

Gdy kod urządzenia zostanie połączony z IoT Central, można wyświetlić właściwości i dane telemetryczne, które wysyła:

1. W aplikacji IoT Central przejdź do strony **urządzenia** i wybierz urządzenie **zestawu deweloperskiego-01** . To urządzenie zostało automatycznie dodane po powiązaniu kodu urządzenia:

    ![Strona przeglądu](./media/tutorial-connect-pnp-device/overview-page.png)

    Po kilku minutach na tej stronie przedstawiono wykresy telemetrii wysyłanej przez urządzenie.

1. Wybierz stronę **informacje** , aby wyświetlić wartości właściwości wysłane przez urządzenie.

1. Wybierz stronę **polecenia** , aby wywołać polecenia na urządzeniu. Urządzenie może być widoczne w wierszu polecenia, w którym jest uruchamiany kod urządzenia.

1. Przejdź do strony **Szablony urządzeń** , aby wyświetlić szablon, który IoT Central utworzony na podstawie DCM w repozytorium publicznym:

    ![Strona szablonów urządzeń](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play, które zostało wygenerowane na podstawie DCM w repozytorium modelu publicznego.

Aby dowiedzieć się więcej o DCMs i sposobach tworzenia własnych modeli, przejdź do przewodnika krok po kroku:

> [!div class="nextstepaction"]
> [Tworzenie grupy urządzeń](./tutorial-use-device-groups.md)
