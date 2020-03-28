---
title: Samouczek — łączenie urządzenia typu Plug and Play (wersja zapoznawcza) usługi IoT (Azure IoT Central)
description: W tym samouczku pokazano, jak używać modelu możliwości urządzenia do generowania kodu urządzenia. Następnie uruchom kod urządzenia, zobacz urządzenie połączyć się z aplikacją IoT Central i użyj widoków autogenerowanych.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 42098d54725cc12691839b63c508efbecf042aa0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80064420"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-and-connect-it-to-your-iot-central-application"></a>Samouczek: Tworzenie urządzenia Typu Plug and Play (podgląd) za pomocą modelu urządzenia umożliwia utworzenie urządzenia IoT Plug and Play (wersja zapoznawcza) i podłączenie go do aplikacji IoT Central

_Model możliwości urządzenia_ (DCM) opisuje możliwości urządzenia typu [IoT Plug and Play (podgląd).](../../iot-pnp/overview-iot-plug-and-play.md) IoT Central może używać dcm do tworzenia szablonu urządzenia i wizualizacje dla urządzenia, gdy urządzenie łączy się po raz pierwszy.

Obsługa [technologii IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) jest dostępna w wersji zapoznawczej i jest obsługiwana tylko w wybranych regionach.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Użyj programu Visual Studio Code, aby utworzyć urządzenie typu Plug and Play (podgląd) usługi IoT przy użyciu kontrolera DCM.
> * Uruchom kod urządzenia w systemie Windows i zobacz, jak łączy się z aplikacją IoT Central.
> * Wyświetl symulowane dane telemetryczne, które wysyła urządzenie.

## <a name="prerequisites"></a>Wymagania wstępne

Ukończ szybki start [aplikacji Azure IoT Central,](./quick-deploy-iot-central.md) aby utworzyć aplikację IoT Central przy użyciu niestandardowego szablonu **aplikacji > niestandardowej.**

Aby ukończyć ten samouczek, należy zainstalować następujące oprogramowanie na komputerze lokalnym:

* [Tworzenie narzędzi dla programu Visual Studio za](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) pomocą narzędzi **kompilacji języka C++** i obciążeń **składników menedżera pakietów Nuget.** Lub jeśli masz już [visual studio (społeczność, professional lub przedsiębiorstwo)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 lub 2015 z tymi samymi obciążeniami zainstalowanymi.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/) - po zainstalowaniu **CMake**, wybierz opcję **Dodaj CMake do ścieżki systemu**.
* [Program Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* Narzędzie: `dps-keygen`

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Instalowanie narzędzi Usługi Azure IoT

Aby zainstalować pakiet rozszerzeń narzędzi Azure IoT Tools w programie VS Code, należy wykonać następujące kroki:

1. W programie VS Code wybierz kartę **Rozszerzenia.**
1. Wyszukaj **narzędzia IoT platformy Azure**.
1. Wybierz pozycję **Zainstaluj**.

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym samouczku używasz menedżera biblioteki [Vcpkg,](https://github.com/microsoft/vcpkg) aby zainstalować zestaw SDK urządzenia Usługi Azure IoT C w środowisku deweloperskim.

1. Otwórz wiersz polecenia. Wykonaj następujące polecenie, aby zainstalować Vcpkg:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Następnie, aby podłączyć [integrację](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)całego użytkownika, uruchom następujące polecenie. Przy pierwszym uruchomieniu tego polecenia wymaga ono praw administracyjnych:

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Zainstaluj zestaw Vcpkg urządzenia usługi Azure IoT C:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Generowanie klucza urządzenia

Aby podłączyć urządzenie do aplikacji IoT Central, potrzebny jest klucz urządzenia. Aby wygenerować klucz urządzenia:

1. Zaloguj się do aplikacji IoT Central utworzonej przy użyciu szablonu **aplikacji niestandardowej** w przewodniku Szybki start [aplikacji Azure IoT Central.](./quick-deploy-iot-central.md)

1. Przejdź do strony **Administracja** i wybierz pozycję **Połączenie urządzenia**.

1. Zanotuj **zakres identyfikatora** i **klucz podstawowy** widoczny po wybraniu opcji **Zobacz klawisze**. Te wartości są używane w dalszej części tego samouczka.

    ![Połączenie urządzenia](./media/tutorial-connect-pnp-device/device-connection.png)

1. Otwórz wiersz polecenia i uruchom następujące polecenie, aby wygenerować klucz urządzenia:

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Zanotuj wygenerowany _klucz urządzenia_, użyj tej wartości w późniejszym kroku w tym samouczku.

## <a name="download-your-model"></a>Pobierz swój model

W tym samouczku używasz publicznego kontrolera DCM dla urządzenia MxChip IoT DevKit. Nie potrzebujesz rzeczywistego urządzenia DevKit do uruchomienia kodu, w tym samouczku skompilować kod do uruchomienia w systemie Windows.

1. Utwórz folder `central_app` o nazwie i otwórz go w programie VS Code.

1. Użyj **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń, wprowadzić opcję **IoT Plug and Play**i wybrać opcję Otwórz **repozytorium modelu**. Wybierz **publiczne repozytorium**. Vs Code pokazuje listę dcms w repozytorium modelu publicznego.

1. Wybierz **MXChip IoT DevKit** DCM `urn:mxchip:mxchip_iot_devkit:1`z identyfikatorem . Następnie wybierz pozycję **Pobierz**. Teraz masz kopię DCM w `central_app` folderze.

![Repozytorium modeli i DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> Aby można było pracować z IoT Central, model możliwości urządzenia musi mieć wszystkie interfejsy zdefiniowane w tym samym pliku.

## <a name="generate-the-c-code-stub"></a>Generowanie skrótu kodu C

Teraz masz **MXChip IoT DevKit** DCM i jego skojarzone interfejsy, można wygenerować kod urządzenia, który implementuje model. Aby wygenerować skrót kodu C w kodzie VS:

1. Po otwarciu folderu z otwartymi plikami DCM użyj **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń, wprowadź **polecenie IoT Plug and Play**i wybierz pozycję **Generuj skrót kodu urządzenia**.

    > [!NOTE]
    > Przy pierwszym użyciu narzędzia Generator kodu IoT Plug and Play trwa kilka sekund.

1. Wybierz właśnie pobrany plik **MXChip IoT DevKit** DCM.

1. Wprowadź nazwę projektu **devkit_device**.

1. Wybierz **ANSI C** jako swój język.

1. Wybierz **za pośrednictwem DPS (Usługa inicjowania obsługi urządzeń) klucz symetryczny** jako metodę połączenia.

1. Wybierz **CMake Project w systemie Windows** jako typ projektu. Nie wybieraj **programu MXChip IoT DevKit Project**, ta opcja jest dostępna w przypadku posiadania prawdziwego urządzenia DevKit.

1. Wybierz **via Vcpkg** jako sposób na włączenie SDK.

1. Program VS Code otwiera nowe okno z `devkit_device` wygenerowanymi plikami skrótowymi kodu urządzenia w folderze.

![Wygenerowany kod urządzenia](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>Kompilowanie kod

Użyj sdk urządzenia do tworzenia wygenerowanego skrótu kodu urządzenia. Aplikacja, która tworzysz symuluje urządzenie **MXChip IoT DevKit** i łączy się z aplikacją IoT Central. Aplikacja wysyła dane telemetryczne i właściwości i odbiera polecenia.

1. W wierszu polecenia `cmake` utwórz podkatalog w folderze `devkit_device` i przejdź do tego folderu:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby utworzyć wygenerowany skrót kodu. Zastąp `<directory of your Vcpkg repo>` symbol zastępczy ścieżką do kopii repozytorium **Vcpkg:**

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Jeśli używasz programu Visual Studio 2017 lub 2015, należy określić generator CMake na podstawie narzędzi kompilacji, których używasz:

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. Po pomyślnym zakończeniu kompilacji w tym samym wierszu polecenia uruchom aplikację. Zastąp `<scopeid>` i `<devicekey>` wartości, które zostały wcześniej odnotowane:

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <devicekey>
    ```

1. Aplikacja urządzenia rozpoczyna wysyłanie danych do usługi IoT Hub. Czasami pojawia się `Error registering device for DPS` błąd przy pierwszym uruchomieniu poprzedniego polecenia. Jeśli ten błąd zostanie wyświetlony, ponów próbę wykonania polecenia.

## <a name="view-the-device"></a>Wyświetlanie urządzenia

Po połączeniu kodu urządzenia z centrum IoT, można wyświetlić właściwości i dane telemetryczne, które wysyła:

1. W aplikacji IoT Central przejdź do strony **Urządzenia** i wybierz urządzenie **mxchip-01.** To urządzenie zostało automatycznie dodane po podłączeniu kodu urządzenia:

    ![Strona przeglądowa](./media/tutorial-connect-pnp-device/overview-page.png)

    Po kilku minutach na tej stronie przedstawiono wykresy danych telemetrycznych wysyłanych przez urządzenie.

1. Wybierz stronę **Informacje,** aby wyświetlić wartości właściwości wysłanego urządzenia.

1. Wybierz stronę **Polecenia,** aby wywołać polecenia na urządzeniu. Możesz zobaczyć urządzenie odpowiadające w wierszu polecenia, który jest uruchomiony kod urządzenia.

1. Przejdź do **strony Szablony urządzeń,** aby wyświetlić szablon utworzony przez IoT Central z kontrolera DCM w publicznym repozytorium:

    ![Strona szablony urządzeń](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak połączyć urządzenie Typu Plug and Play (wersja zapoznawcza) (wersja zapoznawcza), które zostało wygenerowane z kontrolera DCM w publicznym repozytorium modelu.

Aby dowiedzieć się więcej o dcms i jak tworzyć własne modele, przejdź do poradnika:

> [!div class="nextstepaction"]
> [Definiowanie nowego typu urządzenia IoT](./howto-set-up-template.md)
