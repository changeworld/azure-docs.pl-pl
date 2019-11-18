---
title: Podłączanie przykładowego kodu urządzenia do programu IoT Plug and Play w wersji zapoznawczej do IoT Hub (Windows) | Microsoft Docs
description: Kompiluj i uruchamiaj Podgląd Plug and Play IoT przykładowe kod urządzenia w systemie Windows, który łączy się z Centrum IoT. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0d89be9da55c97a5b49157251896d3a513c2c6db
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152072"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>Szybki Start: łączenie przykładowej aplikacji urządzenia IoT Plug and Play w wersji zapoznawczej w systemie Windows do IoT Hub (C Windows)

W tym przewodniku szybki start przedstawiono sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play, łączenia jej z usługą IoT Hub i używania narzędzia Azure IoT Explorer do wyświetlania informacji wysyłanych do centrum. Przykładowa aplikacja jest zapisywana w języku C i jest zawarta w zestawie SDK usługi Azure IoT Hub Device. Deweloperzy rozwiązań mogą korzystać z narzędzia Azure IoT Explorer, aby zrozumieć możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz zainstalować następujące oprogramowanie na komputerze lokalnym:

* [Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) — upewnij się, że podczas instalowania programu Visual Studio dołączysz składnik **Menedżera pakietów NuGet** i **Programowanie aplikacji C++ klasycznych** .
* [Git](https://git-scm.com/download/).
* [CMAKE](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Instalowanie programu Azure IoT Explorer

Pobierz i zainstaluj najnowszą wersję programu **Azure IoT Explorer** ze strony [repozytorium](https://github.com/Azure/azure-iot-explorer/releases) narzędzi, wybierając plik msi w obszarze "zasoby" dla najnowszej aktualizacji.

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku szybki start przygotowano środowisko programistyczne, którego można użyć do klonowania i kompilowania zestawu SDK języka C dla systemu Azure IoT Hub.

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować repozytorium [usługi Azure IoT C SDK i biblioteki](https://github.com/Azure/azure-iot-sdk-c) GitHub do tej lokalizacji:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

## <a name="build-the-code"></a>Kompilowanie kod

Zestaw SDK urządzenia służy do tworzenia dołączonego przykładowego kodu. Utworzona Aplikacja symuluje urządzenie, które nawiązuje połączenie z usługą IoT Hub. Aplikacja wysyła dane telemetryczne i właściwości oraz odbiera polecenia.

1. Utwórz podkatalog `cmake` w folderze głównym zestawu SDK urządzeń i przejdź do tego folderu:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby skompilować zestaw SDK urządzeń i wygenerowany skrót kodu:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Jeśli CMAKE nie może znaleźć C++ kompilatora, podczas uruchamiania poprzedniego polecenia pojawiają się błędy kompilacji. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

## <a name="run-the-device-sample"></a>Uruchamianie przykładu urządzenia

Uruchom przykładową aplikację w zestawie SDK, aby symulować urządzenie Plug and Play IoT wysyłające dane telemetryczne do centrum IoT. Aby uruchomić przykładową aplikację, Użyj tych poleceń i przekaż _Parametry połączenia urządzenia_ jako parametr.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

Urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do centrum. Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

1. Otwórz program Azure IoT Explorer. Zostanie wyświetlona strona **konfiguracje aplikacji** .

1. Wprowadź _Parametry połączenia IoT Hub_ i wybierz pozycję **Połącz**.

1. Po nawiązaniu połączenia zostanie wyświetlona strona przegląd **urządzeń** .

1. Aby upewnić się, że narzędzie może odczytywać definicje modeli interfejsów z urządzenia, wybierz pozycję **Ustawienia**. W menu ustawienia **na połączonym urządzeniu** mogą już występować konfiguracje Plug and Play; Jeśli tak nie jest, wybierz pozycję **+ Dodaj źródło definicji modułu** , a następnie **na podłączonym urządzeniu** , aby je dodać.

1. Na stronie Przegląd **urządzeń** Znajdź wcześniej utworzoną tożsamość urządzenia. Gdy aplikacja urządzenia jest nadal uruchomiona w wierszu polecenia, sprawdź, czy **stan połączenia** urządzenia w programie Azure IoT Explorer jest raportowany jako _połączony_ (jeśli nie, należy **odświeżyć** do momentu). Wybierz urządzenie, aby wyświetlić więcej szczegółów.

1. Rozwiń interfejs o IDENTYFIKATORze **urn: YOUR_COMPANY_NAME_HERE: EnvironmentalSensor: 1** , aby odsłonić interfejs i elementy podstawowe Plug and Play IoT — właściwości, polecenia i dane telemetryczne.

1. Wybierz stronę **Telemetria** i zacznij od _początku_ , aby wyświetlić dane telemetryczne wysyłane przez urządzenie.

1. Wybierz stronę **właściwości (bez możliwości zapisu)** , aby wyświetlić właściwości, które nie zostały zapisywalne zgłoszone przez urządzenie.

1. Wybierz stronę **właściwości (zapisywalne)** , aby wyświetlić właściwości do zapisu, które można zaktualizować.

1. Rozwiń węzeł **Nazwa**właściwości, Aktualizuj przy użyciu nowej nazwy i wybierz opcję **Aktualizuj modyfikowalną Właściwość**. 

1. Aby wyświetlić nową nazwę wyświetlaną w kolumnie **raportowane właściwości** , wybierz przycisk **Odśwież** w górnej części strony.

1. Wybierz stronę **polecenia** , aby wyświetlić wszystkie polecenia obsługiwane przez urządzenie.

1. Rozwiń polecenie **Blink** i Ustaw nowy przedział czasu. Wybierz pozycję **Wyślij polecenie** , aby wywołać polecenie na urządzeniu.

1. Przejdź do wiersza polecenia symulowanego urządzenia i zapoznaj się z wydrukowanymi komunikatami potwierdzającymi, aby sprawdzić, czy polecenia zostały wykonane zgodnie z oczekiwaniami.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w usłudze IoT Hub. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

> [!div class="nextstepaction"]
> [Porada: łączenie z urządzeniem IoT Plug and Play Preview i korzystanie z niego](howto-develop-solution.md)
