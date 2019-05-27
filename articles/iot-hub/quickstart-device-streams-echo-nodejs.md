---
title: Przekazywać aplikacji urządzenia w środowisku Node.js za pomocą strumieni urządzenia usługi IoT Hub (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki start uruchomisz aplikację Node.js po stronie usługi, która komunikuje się z urządzeniem IoT za pomocą strumienia urządzenia.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 9a123c35620cd82059eb08d370939761f7c2fe69
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834045"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Szybki start: komunikacja z aplikacją urządzenia w środowisku Node.js za pomocą strumieni urządzeń usługi IoT Hub (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT Hub obsługuje obecnie strumieni urządzenia jako [funkcja w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. W publicznej wersji zapoznawczej zestaw Node.js SDK obsługuje tylko strumienie urządzeń po stronie usługi. Dlatego ten przewodnik Szybki start zawiera tylko instrukcje uruchamiania aplikacji po stronie usługi. Należy uruchamiać aplikację po stronie urządzenia towarzyszące z jednego z następujących przewodników Szybki Start:

* [Przekazywać aplikacji dla urządzeń w języku C za pomocą strumieni urządzenia usługi IoT Hub](./quickstart-device-streams-echo-c.md)

* [Przekazywać aplikacji dla urządzeń w C# za pośrednictwem strumieni urządzenia usługi IoT Hub](./quickstart-device-streams-echo-csharp.md).

Aplikacja Node.js po stronie usługi w tym przewodniku Szybki start zawiera następujące funkcje:

* Tworzenie strumienia urządzenia do urządzenia IoT.

* Odczytywanie danych wejściowych z wiersza polecenia i wysyłanie ich do aplikacji urządzenia, która wyśle z powrotem echo.

Kod zademonstruje proces inicjowania strumień urządzeń, a także jak z niej korzystać, aby wysyłać i odbierać dane.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Strumienie urządzenia w wersji zapoznawczej jest obecnie obsługiwane tylko w przypadku centrów IoT Hub są tworzone w następujących regionach:

*  **Środkowe stany USA**

*  **Central US EUAP**

Aby uruchomić aplikację po stronie usługi, w tym przewodniku Szybki Start należy Node.js v10.x.x lub później na komputerze deweloperskim.

Możesz pobrać środowiska Node.js dla wielu platform z [Nodejs.org](https://nodejs.org).

Możesz sprawdzić bieżącą wersję środowiska Node.js na komputerze deweloperskim przy użyciu następującego polecenia:

```
node --version
```

Uruchom następujące polecenie, aby dodać rozszerzenia usługi Microsoft Azure IoT dla interfejsu wiersza polecenia platformy Azure do swojego wystąpienia usługi Cloud Shell. Rozszerzenia IOT dodaje określone polecenia usługi IoT Hub, IoT Edge i usługi aprowizacji urządzeń IoT (DPS) do wiersza polecenia platformy Azure.

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

1. Uruchom następujące polecenie w usłudze Azure Cloud Shell do tworzenia tożsamości urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyDevice**: jest to nazwa nadana dla zarejestrowanego urządzenia. Użyj nazwy MyDevice w pokazany sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz również używać tej nazwy w tym artykule oraz zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Potrzebne będą także *parametry połączenia usługi*, aby umożliwić aplikacji zaplecza nawiązywanie połączenia z centrum IoT i pobieranie komunikatów. Następujące polecenie pobiera parametry połączenia usługi dla centrum IoT:

    **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Zanotuj zwróconą wartość, która wygląda następująco:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Komunikacja między urządzeniem i usługą za pomocą strumieni urządzeń

W tej sekcji uruchamiania aplikacji po stronie urządzenia i aplikacji po stronie usługi i komunikować się między nimi.

### <a name="run-the-device-side-application"></a>Uruchamianie aplikacji po stronie urządzenia

Jak wspomniano wcześniej, zestaw Node.js SDK usługi IoT Hub obsługuje tylko strumienie urządzeń po stronie usługi. Dla aplikacji po stronie urządzenia należy użyć towarzyszącego programu urządzenia dostępne w jednym z tych przewodników Szybki Start:

   * [Przekazywać aplikacji dla urządzeń w języku C za pomocą strumieni urządzenia usługi IoT Hub](./quickstart-device-streams-echo-c.md)

   * [Przekazywać aplikacji dla urządzeń w C# za pośrednictwem strumieni urządzenia usługi IoT Hub](./quickstart-device-streams-echo-csharp.md)

Przed przejściem do następnego kroku upewnij się, że aplikacja po stronie urządzenia jest uruchomiona.

### <a name="run-the-service-side-application"></a>Uruchamianie aplikacji po stronie usługi

Jeśli aplikacja po stronie urządzenia jest uruchomiona, wykonaj poniższe czynności, aby uruchomić aplikację po stronie usługi napisaną w języku Node.js:

* Podaj poświadczenia usługi i identyfikator urządzenia jako zmienne środowiskowe.
 
   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Zmień ciąg `MyDevice` na wybrany przez Ciebie identyfikator urządzenia.

* Przejdź do elementu `Quickstarts/device-streams-service` w folderze rozpakowanego projektu i uruchom przykład za pomocą węzła.

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

Na końcu ostatniego kroku program po stronie usługi zainicjuje strumień do urządzenia, a po utworzeniu strumienia wyśle w nim bufor ciągu. W tym przykładzie po prostu odczytuje program po stronie usługi `stdin` na terminalu i wysyła je do urządzenia, które zostaną następnie echo go ponownie. Pozwala to pokazać pomyślną dwukierunkową komunikację między dwiema aplikacjami.

![Dane wyjściowe konsoli po stronie usługi](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Następnie możesz zakończyć program, naciskając ponownie klawisz Enter.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start skonfigurowano centrum IoT Hub, zarejestrowano urządzenie, utworzono strumień urządzenia między aplikacjami po stronie urządzenia i usługi oraz przesłano za pomocą strumienia dane między aplikacjami.

Aby dowiedzieć się więcej na temat strumieni urządzeń, użyj poniższych linków:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)