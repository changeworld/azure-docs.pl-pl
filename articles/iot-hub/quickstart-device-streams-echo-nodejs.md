---
title: Komunikowanie się z aplikacją urządzenia w pliku Node.js za pomocą strumieni urządzeń usługi Azure IoT Hub
description: W tym przewodniku Szybki start uruchomisz aplikację po stronie usługi Node.js, która komunikuje się z urządzeniem IoT za pośrednictwem strumienia urządzenia.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 0757c5eb8639e4a864b049adc92c97a7cf69adba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675506"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Szybki start: komunikowanie się z aplikacją urządzenia w pliku Node.js za pośrednictwem strumieni urządzeń usługi IoT Hub (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

W tym przewodniku Szybki start uruchomisz aplikację po stronie usługi i skonfigurujesz komunikację między urządzeniem a usługą przy użyciu strumieni urządzeń. Strumienie urządzeń usługi Azure IoT Hub umożliwiają aplikacjom usługowym i urządzeń komunikowanie się w sposób bezpieczny i przyjazny dla zapory. Podczas publicznej wersji zapoznawczej node.js SDK obsługuje tylko strumienie urządzeń po stronie usługi. Dlatego ten przewodnik Szybki start zawiera tylko instrukcje uruchamiania aplikacji po stronie usługi.

## <a name="prerequisites"></a>Wymagania wstępne

* Zakończenie komunikacji z [aplikacjami urządzenia w języku C za pośrednictwem strumieni urządzeń Usługi IoT Hub](./quickstart-device-streams-echo-c.md) lub [komunikowania się z aplikacjami urządzeń w języku C# za pośrednictwem strumieni urządzeń Usługi IoT Hub](./quickstart-device-streams-echo-csharp.md).

* Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10+](https://nodejs.org).

* [Przykładowy projekt Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

Możesz sprawdzić bieżącą wersję środowiska Node.js na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
node --version
```

Usługa Microsoft Azure IoT Hub obsługuje obecnie strumienie urządzeń jako [funkcję w wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!IMPORTANT]
> Podgląd strumieni urządzeń jest obecnie obsługiwany tylko dla centrów IoT utworzonych w następujących regionach:
>
> * Środkowe stany USA
> * Centralna amerykańska EUAP
> * Europa Północna
> * Azja Południowo-Wschodnia

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Dodawanie rozszerzenia IoT platformy Azure

Uruchom następujące polecenie, aby dodać rozszerzenie IoT usługi Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia usługi Cloud Shell. Rozszerzenie IoT dodaje polecenia Usługi IoT Hub, IoT Edge i Usługi inicjowania obsługi administracyjnej urządzeń IoT (DPS) do interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-node.md), możesz pominąć ten krok.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-node.md), możesz pominąć ten krok.

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w usłudze Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyDevice**: Jest to nazwa urządzenia, które rejestrujesz. Zaleca się stosowanie **mydevice,** jak pokazano na rysunku. Jeśli wybierzesz inną nazwę urządzenia, musisz również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. Potrzebne będą także *parametry połączenia usługi*, aby umożliwić aplikacji zaplecza nawiązywanie połączenia z centrum IoT i pobieranie komunikatów. Następujące polecenie pobiera parametry połączenia usługi dla centrum IoT:

    **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Zanotuj zwrócony ciąg połączenia usługi do późniejszego użycia w tym przewodniku Szybki start. Wygląda to następująco:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Komunikacja między urządzeniem i usługą za pomocą strumieni urządzeń

W tej sekcji należy uruchomić zarówno aplikacji po stronie urządzenia i aplikacji po stronie usługi i komunikować się między nimi.

### <a name="run-the-device-side-application"></a>Uruchamianie aplikacji po stronie urządzenia

Jak wspomniano wcześniej, zestaw Node.js SDK usługi IoT Hub obsługuje tylko strumienie urządzeń po stronie usługi. W przypadku aplikacji po stronie urządzenia użyj jednego z towarzyszących programów urządzeń dostępnych w następujących programach Szybki start:

* [Komunikowanie się z aplikacjami urządzeń w języku C za pośrednictwem strumieni urządzeń Usługi IoT Hub](./quickstart-device-streams-echo-c.md)

* [Komunikowanie się z aplikacjami urządzeń w języku C# za pośrednictwem strumieni urządzeń Usługi IoT Hub](./quickstart-device-streams-echo-csharp.md)

Przed przejściem do następnego kroku upewnij się, że aplikacja po stronie urządzenia jest uruchomiona.

### <a name="run-the-service-side-application"></a>Uruchamianie aplikacji po stronie usługi

Aplikacja Node.js po stronie usługi w tym przewodniku Szybki start zawiera następujące funkcje:

* Tworzenie strumienia urządzenia do urządzenia IoT.
* Odczytywanie danych wejściowych z wiersza polecenia i wysyłanie ich do aplikacji urządzenia, która wyśle z powrotem echo.

Kod zademonstruje proces inicjacji strumienia urządzenia, a także sposób używania go do wysyłania i odbierania danych.

Zakładając, że aplikacja po stronie urządzenia jest uruchomiona, wykonaj poniższe czynności w oknie terminala lokalnego, aby uruchomić aplikację po stronie usługi w pliku Node.js:

* Podaj poświadczenia usługi i identyfikator urządzenia jako zmienne środowiskowe.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Zmień symbol zastępczy ServiceConnectionString, aby dopasować parametry połączenia usługi, i **MyDevice,** aby dopasować identyfikator urządzenia, jeśli nadano jej inną nazwę.

* Przejdź do elementu `Quickstarts/device-streams-service` w folderze rozpakowanego projektu i uruchom przykład za pomocą węzła.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

Na końcu ostatniego kroku program po stronie usługi zainicjuje strumień do urządzenia, a po utworzeniu strumienia wyśle w nim bufor ciągu. W tym przykładzie program po stronie `stdin` usługi po prostu odczytuje na terminalu i wysyła go do urządzenia, które następnie echo go z powrotem. Pozwala to pokazać pomyślną dwukierunkową komunikację między dwiema aplikacjami.

![Wyjście konsoli po stronie usługi](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Następnie możesz zakończyć program, naciskając ponownie klawisz Enter.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurowano centrum IoT hub, zarejestrowano urządzenie, ustanowiono strumień urządzeń między aplikacjami po stronie urządzenia i usługi oraz użyto strumienia do wysyłania danych między aplikacjami.

Aby dowiedzieć się więcej na temat strumieni urządzeń, użyj poniższych linków:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md) 