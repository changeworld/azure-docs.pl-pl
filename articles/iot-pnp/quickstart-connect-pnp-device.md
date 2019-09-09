---
title: Łączenie przykładowego kodu urządzenia w usłudze IoT Plug and Play w wersji zapoznawczej IoT Hub | Microsoft Docs
description: Kompiluj i uruchamiaj przykładowy kod urządzenia z usługą IoT Plug and Play w wersji zapoznawczej, który łączy się z usługą IoT Hub. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 4802c120f4722796fcad4ea99f25adad368451fd
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806542"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub"></a>Szybki start: Połącz przykładową aplikację urządzenia IoT Plug and Play w wersji zapoznawczej do IoT Hub

W tym przewodniku szybki start przedstawiono sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play, łączenia jej z usługą IoT Hub i używania narzędzia Azure IoT Explorer do wyświetlania informacji wysyłanych do centrum. Przykładowa aplikacja jest zapisywana w języku C i jest uwzględniona w zestawie SDK urządzeń Azure IoT dla języka C. Deweloperzy rozwiązań mogą korzystać z narzędzia Azure IoT Explorer, aby zrozumieć możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz zainstalować następujące oprogramowanie na komputerze lokalnym:

* [Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) — upewnij się, że podczas instalowania programu Visual Studio dołączysz składnik **Menedżera pakietów NuGet** i **Programowanie aplikacji C++ klasycznych** .
* [Git](https://git-scm.com/download/).
* [CMAKE](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Instalowanie programu Azure IoT Explorer

Pobierz i zainstaluj narzędzie Azure IoT Explorer na stronie [najnowszej wersji](https://github.com/Azure/azure-iot-explorer/releases) .

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

Otwórz wiersz polecenia. Wykonaj następujące polecenie, aby sklonować repozytorium GitHub [zestawu SDK języka C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c):

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

## <a name="build-the-code"></a>Kompilowanie kod

Utworzona Aplikacja symuluje urządzenie, które nawiązuje połączenie z usługą IoT Hub. Aplikacja wysyła dane telemetryczne i właściwości oraz odbiera polecenia.

1. `cmake` Utwórz podkatalog w folderze głównym zestawu SDK urządzeń i przejdź do tego folderu:

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

Uruchom aplikację, przekazując parametry połączenia urządzenia usługi IoT Hub jako parametr.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "[IoT Hub device connection string]"
```

Aplikacja urządzenia uruchamia wysyłanie danych do IoT Hub.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

1. Otwórz program Azure IoT Explorer, zobaczysz stronę **konfiguracje aplikacji** .

1. Wprowadź parametry połączenia IoT Hub i kliknij przycisk **Połącz**.

1. Po nawiązaniu połączenia zostanie wyświetlona strona Przegląd urządzenia.

1. Aby dodać repozytorium firmy, wybierz opcję **Ustawienia**, a następnie pozycję **+ Nowy**, a następnie **na podłączonym urządzeniu**.

1. Na stronie Przegląd urządzenia Znajdź wcześniej utworzoną tożsamość urządzenia i wybierz ją, aby wyświetlić więcej szczegółów.

1. Rozwiń interfejs o IDENTYFIKATORze **urn: YOUR_COMPANY_NAME_HERE: EnvironmentalSensor: 1** , aby wyświetlić elementy podstawowe Plug and Play IoT — właściwości, polecenia i dane telemetryczne.

1. Wybierz stronę **telemetrii** , aby wyświetlić dane telemetryczne wysyłane przez urządzenie.

1. Wybierz stronę **właściwości (bez możliwości zapisu)** , aby wyświetlić właściwości, które nie zostały zapisywalne zgłoszone przez urządzenie.

1. Wybierz stronę **właściwości (zapisywalne)** , aby wyświetlić właściwości do zapisu, które można zaktualizować.

1. Rozwiń węzeł **Nazwa**właściwości, Aktualizuj przy użyciu nowej nazwy i wybierz opcję **Aktualizuj modyfikowalną Właściwość**. 

1. Aby zobaczyć, że nowa nazwa jest wyświetlana w kolumnie **Właściwość raportowana** , kliknij przycisk **Odśwież** w górnej części strony.

1. Wybierz stronę **polecenia** , aby wyświetlić wszystkie polecenia obsługiwane przez urządzenie.

1. Rozwiń polecenie **Blink** i Ustaw nowy przedział czasu. Wybierz pozycję **Wyślij polecenie** , aby wywołać polecenie na urządzeniu.

1. Przejdź do symulowanego urządzenia, aby sprawdzić, czy polecenie zostało wykonane zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w usłudze IoT Hub. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

> [!div class="nextstepaction"]
> [Instrukcje: Łączenie się z urządzeniem Plug and Play IoT w wersji zapoznawczej i korzystanie z niego](howto-develop-solution.md)
