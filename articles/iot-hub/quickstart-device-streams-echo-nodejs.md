---
title: Komunikacja z aplikacją urządzenia w języku Node. js za pośrednictwem IoT Hub strumieni urządzenia (wersja zapoznawcza) | Microsoft Docs
description: W tym przewodniku szybki start uruchomisz aplikację po stronie usługi dla środowiska Node. js, która komunikuje się z urządzeniem IoT za pośrednictwem strumienia urządzeń.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 4acfe148e424d41308e310abd2a10e4e21133799
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900845"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Szybki Start: komunikacja z aplikacją urządzenia w języku Node. js za pośrednictwem IoT Hub strumieni urządzenia (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT Hub obecnie obsługuje strumienie urządzeń jako [funkcję w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. W publicznej wersji zapoznawczej zestaw Node.js SDK obsługuje tylko strumienie urządzeń po stronie usługi. Dlatego ten przewodnik Szybki start zawiera tylko instrukcje uruchamiania aplikacji po stronie usługi. Należy uruchomić towarzyszącą aplikację po stronie urządzenia z jednego z następujących przewodników szybki start:

* [Komunikacja z aplikacjami urządzeń w języku C za pośrednictwem IoT Hub strumieni urządzeń](./quickstart-device-streams-echo-c.md)

* [Komunikacja z aplikacjami urządzeń w C# programie za pośrednictwem IoT Hub strumieni urządzeń](./quickstart-device-streams-echo-csharp.md).

Aplikacja Node.js po stronie usługi w tym przewodniku Szybki start zawiera następujące funkcje:

* Tworzenie strumienia urządzenia do urządzenia IoT.

* Odczytywanie danych wejściowych z wiersza polecenia i wysyłanie ich do aplikacji urządzenia, która wyśle z powrotem echo.

Kod będzie demonstrować proces inicjowania strumienia urządzenia, a także sposób korzystania z niego do wysyłania i odbierania danych.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Podgląd strumieni urządzeń jest obecnie obsługiwany tylko dla centrów IoT utworzonych w następujących regionach:

  * Środkowe stany USA
  * Środkowe stany USA — EUAP
  * Europa Północna
  * Azja Południowo-Wschodnia

Aby uruchomić aplikację po stronie usługi w tym przewodniku Szybki Start, musisz mieć Node. js v10. x. x lub nowszy na komputerze deweloperskim.

W programie Node. js można pobrać wiele platform z [NodeJS.org](https://nodejs.org).

Możesz sprawdzić bieżącą wersję środowiska Node.js na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
node --version
```

Uruchom następujące polecenie, aby dodać rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell. Rozszerzenie IOT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Jeśli nie zostało to jeszcze zrobione, pobierz przykładowy projekt Node.js z https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip i wyodrębnij archiwum ZIP.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-node.md), możesz pominąć ten krok.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-node.md), możesz pominąć ten krok.

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   Moje **urządzenie**: jest to nazwa urządzenia, które jest rejestrowany. Zalecane jest korzystanie z **urządzenia** , jak pokazano. W przypadku wybrania innej nazwy dla urządzenia należy również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. Potrzebne będą także *parametry połączenia usługi*, aby umożliwić aplikacji zaplecza nawiązywanie połączenia z centrum IoT i pobieranie komunikatów. Następujące polecenie pobiera parametry połączenia usługi dla centrum IoT:

    **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Zwróć uwagę na zwrócone parametry połączenia usługi do późniejszego użycia w tym przewodniku Szybki Start. Wygląda to następująco:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Komunikacja między urządzeniem i usługą za pomocą strumieni urządzeń

W tej sekcji uruchomisz aplikację po stronie urządzenia i aplikację po stronie usługi i komunikują się między nimi.

### <a name="run-the-device-side-application"></a>Uruchamianie aplikacji po stronie urządzenia

Jak wspomniano wcześniej, zestaw Node.js SDK usługi IoT Hub obsługuje tylko strumienie urządzeń po stronie usługi. W przypadku aplikacji po stronie urządzenia Użyj jednego z towarzyszących programów urządzenia dostępnych w tych przewodnikach szybki start:

   * [Komunikacja z aplikacjami urządzeń w języku C za pośrednictwem IoT Hub strumieni urządzeń](./quickstart-device-streams-echo-c.md)

   * [Komunikacja z aplikacjami urządzeń w C# programie za pośrednictwem IoT Hub strumieni urządzeń](./quickstart-device-streams-echo-csharp.md)

Przed przejściem do następnego kroku upewnij się, że aplikacja po stronie urządzenia jest uruchomiona.

### <a name="run-the-service-side-application"></a>Uruchamianie aplikacji po stronie usługi

Przy założeniu, że aplikacja po stronie urządzenia jest uruchomiona, wykonaj poniższe kroki w lokalnym oknie terminala, aby uruchomić aplikację po stronie usługi w programie Node. js:

* Podaj poświadczenia usługi i identyfikator urządzenia jako zmienne środowiskowe.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Zmień symbol zastępczy serviceconnectionstring w taki sposób, aby pasował do parametrów połączenia z usługą, a **urządzenie** jest zgodne z identyfikatorem urządzenia, jeśli podałeś inną nazwę.

* Przejdź do elementu `Quickstarts/device-streams-service` w folderze rozpakowanego projektu i uruchom przykład za pomocą węzła.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

Na końcu ostatniego kroku program po stronie usługi zainicjuje strumień do urządzenia, a po utworzeniu strumienia wyśle w nim bufor ciągu. W tym przykładzie program po stronie usługi po prostu odczytuje `stdin` w terminalu i wysyła go do urządzenia, które następnie będzie je ponownie wyrównać. Pozwala to pokazać pomyślną dwukierunkową komunikację między dwiema aplikacjami.

![Dane wyjściowe konsoli po stronie usługi](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Następnie możesz zakończyć program, naciskając ponownie klawisz Enter.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz usługę IoT Hub, zarejestrowano urządzenie, nałożyłeś strumień urządzeń między aplikacjami na urządzeniu i usłudze oraz użyto strumienia do przesyłania danych z powrotem i między aplikacjami.

Aby dowiedzieć się więcej na temat strumieni urządzeń, użyj poniższych linków:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md) 