---
title: Połącz urządzenie z usługą IoT Plug and Play, aby IoT Central | Microsoft Docs
description: Jako deweloper urządzenia zapoznaj się z tematem jak używać Visual Studio Code do tworzenia i testowania urządzenia Plug and Play IoT, które nawiązuje połączenie z IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6eb73c1c2b768d82143890415702950905f261f6
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997243"
---
# <a name="use-visual-studio-code-to-create-an-iot-plug-and-play-device-that-connects-to-iot-central"></a>Użyj Visual Studio Code, aby utworzyć urządzenie IoT Plug and Play, które nawiązuje połączenie z IoT Central

Ten przewodnik przedstawia, jak:

* Jako operator, aby dodać i skonfigurować rzeczywiste urządzenie w aplikacji IoT Central platformy Azure.

* Jako deweloper urządzenia, aby użyć Visual Studio Code do utworzenia urządzenia [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) , które nawiązuje połączenie z aplikacją IoT Central.

[Model możliwości urządzenia](./concepts-architecture-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) służy do definiowania urządzenia, które nawiązuje połączenie z IoT Central. W tym przewodniku należy użyć modelu, który definiuje urządzenie czujnika środowiska.

Deweloper urządzenia używa modelu do generowania kodu klienta urządzenia, a Konstruktor używa modelu do [tworzenia szablonu urządzenia](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) w IoT Central. Model pełni rolę kontraktu między urządzeniem i aplikacją IoT Central.

Sekcja w tym przewodniku opisująca sposób tworzenia wygenerowanego kodu założono, że używasz systemu Windows.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

* Importuj model możliwości urządzenia do szablonu urządzenia w IoT Central
* Dodawanie pulpitu nawigacyjnego do szablonu wyświetlającego dane telemetryczne urządzenia
* Dodaj rzeczywiste urządzenie z szablonu
* Importuj model możliwości urządzenia do Visual Studio Code
* Wygeneruj aplikację kliencką urządzenia C z modelu
* Utwórz aplikację kliencką urządzenia C i połącz ją z usługą IoT Central

## <a name="prerequisites"></a>Wymagania wstępne

Aby przetestować kod urządzenia w tym przewodniku, potrzebna jest aplikacja IoT Central utworzona na podstawie szablonu aplikacji w **wersji** zapoznawczej. Jeśli nie masz jeszcze aplikacji IoT Central do użycia, wykonaj kroki szybkiego startu [Tworzenie aplikacji IoT Central platformy Azure (funkcje w wersji zapoznawczej)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json):

Do pracy z modelem możliwości urządzenia w tym przewodniku potrzebne są:

* [Visual Studio Code](https://code.visualstudio.com/download): Visual Studio Code jest dostępna dla wielu platform
* Rozszerzenie Workbench urządzenia Azure IoT dla Visual Studio Code. Wykonaj następujące kroki, aby zainstalować rozszerzenie Workbench urządzenia Azure IoT w VS Code:

    1. W VS Code wybierz kartę **rozszerzenia** .
    1. Wyszukaj **usługę Azure IoT Device Workbench**.
    1. Wybierz pozycję **Zainstaluj**.

    > [!NOTE]
    > Bieżąca wersja generatora kodu w rozszerzeniu nie obsługuje typów schematu geopunktowego i **wektorowego** , typów semantyki **przyspieszania**, **szybkości**i **lokalizacji** . Te schematy i typy semantyczne są obsługiwane przez IoT Central.

    > [!NOTE]
    > Aby można było korzystać z IoT Central, model możliwości urządzenia musi mieć wszystkie interfejsy zdefiniowane wewnętrznie w tym samym pliku.

Aby utworzyć wygenerowany kod C w systemie Windows w tym przewodniku, musisz:

* [Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) — upewnij się, że podczas instalowania programu Visual Studio dołączysz składnik **Menedżera pakietów NuGet** i **Programowanie aplikacji C++ klasycznych** .
* [Usługa Git](https://git-scm.com/download)
* [CMAKE](https://cmake.org/download/) — po zainstalowaniu **CMAKE**wybierz opcję **Dodaj CMAKE do ścieżki systemowej**.
* Lokalna kopia zestawu SDK usługi Azure IoT C:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

Aby postępować zgodnie z instrukcjami podanymi na urządzeniu przed końcem tej procedury, należy również zainstalować następujące polecenie:

* [Node.js](https://nodejs.org)
* [Narzędzie DPS-Keygen](https://www.npmjs.com/package/dps-keygen):

    ```cmd/sh
    npm i -g dps-keygen
    ```

## <a name="create-device-template"></a>Utwórz szablon urządzenia

Utwórz folder o nazwie **pnp_app** w folderze **Azure-IoT-SDK-c** zawierający sklonowany zestaw SDK języka c dla usługi Azure IoT. Pobierz model możliwości urządzenia [EnvironmentalSensor. capabilitymodel. JSON](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) z usługi GitHub i Zapisz plik w folderze **pnp_app** . Zastąp dwa wystąpienia `<YOUR_COMPANY_NAME_HERE>` o nazwie. Używaj tylko znaków a-z, A-Z, 0-9 i znaku podkreślenia. Zanotuj pełną wartość `"@id"` pola, które jednoznacznie identyfikuje model możliwości urządzenia, będziesz potrzebować później. Ten model zawiera dwa interfejsy:

* Wspólny interfejs **DeviceManagement** .
* niestandardowy interfejs **EnvironmentalSensor** .

Aby utworzyć szablon urządzenia dla czujnika środowiska w IoT Central:

1. Przejdź do strony **Szablony urządzeń** i wybierz pozycję **+ Nowy**. Następnie wybierz pozycję **niestandardowy**.

1. Wprowadź **czujnik środowiska** jako nazwę szablonu urządzenia.

1. Wybierz pozycję **Importuj model możliwości**. Następnie odszukaj utworzony plik **EnvironmentalSensor. capabilitymodel. JSON** , a następnie wybierz pozycję **Otwórz**. Dwa interfejsy, **Informacje o urządzeniu** i **czujnik środowiska**są wyświetlane w **modelu możliwości czujnika środowiska**.

1. Wybierz pozycję **widoki** , a następnie **Wizualizuj urządzenie**.

1. Na liście pól, którą można dodać do pulpitu nawigacyjnego, wybierz dwie wartości telemetrii , wilgotność i **temperaturę** , a następnie wybierz pozycję **Połącz**. Następnie wybierz polecenie **Zapisz**.

Masz teraz szablon urządzenia, który używa modelu **czujnika środowiska** i który ma prosty pulpit nawigacyjny do wyświetlania danych telemetrycznych z urządzenia.

## <a name="publish-the-template-and-add-a-real-device"></a>Publikowanie szablonu i Dodawanie rzeczywistego urządzenia

Aby opublikować szablon i dodać rzeczywiste urządzenie, przejdź do strony **Szablony urządzeń** i wybierz szablon urządzenia **czujnika środowiska** . Wybierz pozycję **Publikuj**, przejrzyj informacje, a następnie wybierz pozycję **Publikuj**.

Przed połączeniem aplikacji klienckiej należy dodać urządzenie na stronie **urządzenia** i uzyskać informacje o połączeniu:

1. Przejdź do strony **urządzenia** i wybierz pozycję **czujnik środowiska**. Strona **urządzenia** umożliwia zarządzanie urządzeniami, które mogą nawiązywać połączenia z aplikacją.

1. Aby dodać rzeczywiste urządzenie, wybierz pozycję **+ Nowy**, Zmień identyfikator urządzenia na **sensor01**, a następnie wybierz pozycję **Utwórz**. Upewnij się, że symulowane jest **wyłączone**.

1. Na liście urządzeń wybierz urządzenie **czujnika środowiska** . Następnie wybierz pozycję **Połącz**.

1. Zanotuj **Identyfikator zakresu**, **Identyfikator urządzenia**i **klucz podstawowy**. Następnie wybierz pozycję **Zamknij**.

## <a name="generate-a-device-client-application"></a>Generowanie aplikacji klienckiej urządzenia

Użyj Visual Studio Code do wygenerowania szkieletowej aplikacji klienckiej urządzenia z modelu możliwości urządzenia:

1. Uruchom Visual Studio Code i Otwórz folder **pnp_app** .

1. **Naciśnij klawisze Ctrl + Shift + P** , aby otworzyć paletę poleceń.

1. Wprowadź **Plug and Play** a następnie wybierz pozycję **Generuj procedurę tworzenia kodu urządzenia**.

1. Wybierz plik modelu możliwości urządzenia **EnvironmentalSensor. capabilitymodel. JSON** .

1. Wprowadź **sensor_app** jako nazwę projektu.

1. Wybierz **ANSI C** jako język.

1. Wybierz **projekt CMAKE** jako element docelowy.

1. Wybierz pozycję **za pośrednictwem usługi DPS (usługa Device Provisioning Service)** jako metodę połączenia.

Visual Studio Code otwiera nowe okno z wygenerowanym kodem C w folderze **sensor_app** .

## <a name="add-connection-details-to-the-device-client"></a>Dodawanie szczegółów połączenia do klienta urządzenia

Aby dodać informacje o połączeniu do klienta urządzenia, Otwórz **Main. c** w folderze zawierającym wygenerowany kod:

1. Zamień `[DPS Id Scope]` na wartość **identyfikatora zakresu** , która została wcześniej zanotowana.

1. Zamień `[DPS symmetric key]` na wartość **klucza podstawowego** , która została wcześniej zanotowana.

1. Zamień `[device registration Id]` na wartość **identyfikatora urządzenia** zanotowaną wcześniej.

1. Zapisz zmiany.

## <a name="build-and-run-the-client"></a>Kompilowanie i uruchamianie klienta

Aby skompilować i uruchomić klienta, należy dostosować kompilację dla zestawu SDK usługi Azure IoT C:

1. Otwórz plik **CMakeLists. txt** w folderze głównym folderu **Azure-IoT-SDK-c** .

1. Dodaj następujący wiersz na końcu tego pliku, aby dołączyć nową aplikację kliencką do kompilacji:

    ```txt
    add_subdirectory(pnp_app/sensor_app)
    ```

1. Zapisz zmiany.

1. Otwórz wiersz polecenia i przejdź do folderu **Azure-IoT-SDK-c** .

1. Aby skompilować aplikację, uruchom następujące polecenia:

    ```cmd
    mkdir cmake
    cd cmake
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. Aby uruchomić aplikację, uruchom następujące polecenie w wierszu polecenia:

    ```cmd
    pnp_app\sensor_app\Release\sensor_app.exe
    ```

## <a name="view-the-telemetry"></a>Wyświetlanie telemetrii

Po kilku minutach można zobaczyć dane telemetryczne z urządzenia na pulpicie nawigacyjnym urządzenia w aplikacji IoT Central.

## <a name="connect-device-first"></a>Połącz urządzenie — najpierw

Urządzenie usługi IoT Plug and Play można połączyć za pośrednictwem połączenia z urządzeniem pierwszym, zgodnie z opisem w temacie [łączność](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). Proces odnajdywania następuje po następującej kolejności:

1. Kojarzy z szablonem urządzenia, jeśli został już opublikowany w aplikacji IoT Central.

1. Pobiera model możliwości z [publicznego repozytorium](https://aka.ms/ACFI) opublikowanych i certyfikowanych modeli możliwości.

Za pomocą Visual Studio Code i **Generuj polecenie stub kodu urządzenia** , do którego odwołuje się powyżej, możesz wykonać następujące kroki, aby połączyć urządzenie — po raz pierwszy i automatycznie wprowadzić w opublikowanym modelu możliwości urządzenia z repozytorium publicznego do IoT Central:

1. Użyj istniejącego modelu możliwości urządzenia, który został opublikowany w repozytorium publicznym. Potrzebujesz pełnego modelu możliwości urządzenia i zanotuj nazwę URN tego modelu.

1. Wykonaj powyższe kroki, aby [wygenerować aplikację kliencką urządzenia](#generate-a-device-client-application) do użycia Visual Studio Code i wygenerować kod urządzenia.

1. W aplikacji IoT Central przejdź do karty **Administracja** i wybierz sekcję **połączenie urządzenia** . Zanotuj wartości **Identyfikator zakresu** i **klucz podstawowy** dla swojej aplikacji.

    > [!NOTE]
    > **Klucz podstawowy** wyświetlany na tej stronie to sygnatura dostępu współdzielonego grupy, której można użyć do wygenerowania wielu kluczy urządzeń dla aplikacji.

1. Użyj narzędzia [DPS Keygen](https://www.npmjs.com/package/dps-keygen) do wygenerowania klucza urządzenia z klucza podstawowego, dla którego wykonano wcześniej uwagi. Aby wygenerować klucz urządzenia, uruchom następujące polecenie:

    ```cmd/sh
    dps-keygen -mk:<Primary_Key> -di:<device_id>
    ```

1. Wykonaj kroki opisane w poprzedniej sekcji [Dodaj szczegóły połączenia do klienta urządzenia](#add-connection-details-to-the-device-client) , aby dodać **Identyfikator zakresu**, **klucz podstawowy**i **Identyfikator urządzenia**.

1. Wykonaj kroki opisane w poprzedniej sekcji, aby [skompilować i uruchomić klienta programu](#build-and-run-the-client).

1. Teraz zobaczysz, że urządzenie nawiązuje połączenie z aplikacją IoT Central i automatycznie przyniesie model możliwości urządzenia z repozytorium publicznego jako szablon urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak podłączyć rzeczywiste urządzenie do IoT Central, sugerowanym następnym krokiem jest dowiedzenie więcej o szablonach urządzeń w temacie [Konfigurowanie szablonu urządzenia](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
