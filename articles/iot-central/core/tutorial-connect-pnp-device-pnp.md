---
title: Połącz urządzenie IoT Plug and Play w wersji zapoznawczej z usługą Azure IoT Central | Microsoft Docs
description: Użyj modelu możliwości urządzenia do generowania kodu urządzenia. Następnie uruchom kod urządzenia, sprawdź, czy urządzenie nawiązuje połączenie z aplikacją IoT Central, i użyj wygenerowanych widoków.
author: dominicbetts
ms.author: dobett
ms.date: 08/08/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: fdf4b68bfb10ff02ae4f90ee4c1668274ed30e79
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957034"
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

Ukończ Przewodnik Szybki Start dotyczący [tworzenia aplikacji IoT Central platformy Azure (funkcje w wersji zapoznawczej)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) , aby utworzyć aplikację IoT Central przy użyciu szablonu **aplikacji niestandardowy > aplikacji w wersji zapoznawczej** .

Aby ukończyć ten samouczek, należy zainstalować następujące oprogramowanie na komputerze lokalnym:

* [Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) — upewnij się, że podczas instalowania programu Visual Studio dołączysz składnik **Menedżera pakietów NuGet** i **Programowanie aplikacji C++ klasycznych** .
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

### <a name="get-azure-iot-device-sdk-for-c"></a>Pobierz zestaw SDK urządzeń Azure IoT dla języka C

Przygotuj środowisko programistyczne, którego możesz użyć do utworzenia zestawu SDK urządzenia usługi Azure IoT C.

1. Otwórz wiersz polecenia. Wykonaj następujące polecenie, aby sklonować repozytorium GitHub [zestawu SDK języka C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

1. Utwórz folder `central_app` w katalogu głównym lokalnego klonu repozytorium. Ten folder służy do plików modelu urządzenia i szczątkowego kodu urządzenia.

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir central_app
    ```

## <a name="generate-device-key"></a>Generuj klucz urządzenia

Aby podłączyć urządzenie do aplikacji IoT Central, wymagany jest klucz urządzenia. Aby wygenerować klucz urządzenia:

1. Zaloguj się do aplikacji IoT Central utworzonej przy użyciu niestandardowego szablonu **aplikacji > aplikacji w wersji zapoznawczej** w temacie [Tworzenie aplikacji IoT Central platformy Azure (funkcje w wersji zapoznawczej)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

1. Przejdź do strony **Administracja** i wybierz pozycję **połączenie z urządzeniem**.

1. Zanotuj **zakres identyfikatorów** i **klucz podstawowy** , który widzisz po wybraniu opcji **Wyświetl klucze**. Te wartości są używane w dalszej części tego samouczka.

    ![Połączenie z urządzeniem](./media/tutorial-connect-pnp-device-pnp/device-connection.png)

1. Otwórz wiersz polecenia i uruchom następujące polecenie, aby wygenerować klucz urządzenia:

    ```cmd/sh
    dps-keygen  -di:mxchip-01 -mk:{Primary Key from previous step}
    ```

    Zanotuj wygenerowany _klucz urządzenia_, Użyj tej wartości w kolejnym kroku w tym samouczku.

## <a name="download-your-model"></a>Pobierz model

W tym samouczku użyjesz publicznej DCM dla urządzenia zestawu deweloperskiego IoT DevKit. Do uruchomienia kodu nie jest potrzebne rzeczywiste urządzenie DevKit. w tym samouczku skompilujesz kod do uruchomienia w systemie Windows.

1. Otwórz `azure-iot-sdk-c\central_app` folder z VS Code.

1. **Naciśnij klawisze Ctrl + Shift + P** , aby otworzyć paletę poleceń, wprowadź **Plug and Play IoT**i wybierz pozycję **Otwórz repozytorium modeli**. Wybierz pozycję **repozytorium publiczne**. VS Code przedstawia listę DCMs w repozytorium modelu publicznego.

1. Wybierz **zestawu deweloperskiego IoT DevKit** DCM z identyfikatorem `urn:mxchip:mxchip_iot_devkit:1`. Następnie wybierz pozycję **Pobierz**. Masz teraz kopię DCM w folderze `central_app`.

![Repozytorium modelu i DCM](./media/tutorial-connect-pnp-device-pnp/public-repository.png)

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

1. Wybierz **projekt CMAKE** jako typ projektu. Nie wybieraj **projektu zestawu deweloperskiego IoT DevKit**, ta opcja jest dostępna w przypadku, gdy masz prawdziwe urządzenie DevKit.

1. Wybierz pozycję **za pośrednictwem usługi DPS (usługa Device Provisioning Service)** jako metodę połączenia.

1. VS Code otwiera nowe okno z wygenerowanymi plikami zastępczymi kodu urządzenia w folderze `devkit_device`.

![Wygenerowany kod urządzenia](./media/tutorial-connect-pnp-device-pnp/generated-code.png)

## <a name="build-the-code"></a>Kompilowanie kod

Zestaw SDK urządzenia służy do tworzenia wygenerowanej klasy urządzenia. Utworzona Aplikacja symuluje urządzenie **zestawu deweloperskiego IoT DevKit** i łączy się z aplikacją IoT Central. Aplikacja wysyła dane telemetryczne i właściwości, a następnie odbiera polecenia.

1. W VS Code Otwórz plik `CMakeLists.txt` w folderze `azure-iot-sdk-c`. Upewnij się, że plik `CMakeLists.txt` jest otwarty w folderze `azure-iot-sdk-c`, a nie w folderze `devkit_device`.

1. Dodaj poniższy wiersz w dolnej części pliku `CMakeLists.txt`, aby dołączyć folder zastępczy kodu urządzenia podczas kompilowania:

    ```txt
    add_subdirectory(central_app/devkit_device)
    ```

1. Utwórz folder `cmake` w folderze `azure-iot-sdk-c` i przejdź do tego folderu w wierszu polecenia:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby skompilować zestaw SDK urządzeń i wygenerowany skrót kodu:

    ```cmd\sh
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. Po pomyślnym zakończeniu kompilacji w tym samym wierszu polecenia Uruchom aplikację. Zastąp `scopeid`, `primarykey` wartościami zanotowanymi wcześniej:

    ```cmd\sh
    .\central_app\devkit_device\Release\devkit_device.exe scopeid primarykey mxchip-001
    ```

1. Aplikacja urządzenia uruchamia wysyłanie danych do aplikacji IoT Central.

## <a name="view-the-device"></a>Wyświetlanie urządzenia

Gdy kod urządzenia zostanie połączony z IoT Central, można wyświetlić właściwości i dane telemetryczne, które wysyła:

1. W aplikacji IoT Central przejdź do strony **urządzenia** i wybierz urządzenie **zestawu deweloperskiego-01** . To urządzenie zostało automatycznie dodane po powiązaniu kodu urządzenia:

    ![Strona przeglądu](./media/tutorial-connect-pnp-device-pnp/overview-page.png)

    Po kilku minutach na tej stronie przedstawiono wykresy telemetrii wysyłanej przez urządzenie.

1. Wybierz stronę **informacje** , aby wyświetlić wartości właściwości wysłane przez urządzenie.

1. Wybierz stronę **polecenia** , aby wywołać polecenia na urządzeniu. Urządzenie może być widoczne w wierszu polecenia, w którym jest uruchamiany kod urządzenia.

1. Przejdź do strony **Szablony urządzeń** , aby wyświetlić szablon, który IoT Central utworzony na podstawie DCM w repozytorium publicznym:

    ![Strona szablonów urządzeń](./media/tutorial-connect-pnp-device-pnp/device-template.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play, które zostało wygenerowane na podstawie DCM w repozytorium modelu publicznego.

Aby dowiedzieć się więcej o DCMs i sposobach tworzenia własnych modeli, przejdź do przewodnika krok po kroku:

> [!div class="nextstepaction"]
> [Konfigurowanie szablonu urządzenia i zarządzanie nim](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
