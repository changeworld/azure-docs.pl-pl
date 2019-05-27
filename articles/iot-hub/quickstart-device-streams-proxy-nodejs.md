---
title: Strumienie urządzeń usługi Azure IoT Hub w środowisku Node.js — Szybki start dla protokołu SSH/RDP (wersja zapoznawcza) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz przykładową aplikację Node.js, która działa jako serwer proxy obsługujący protokół SSH/RDP w strumieniach urządzeń usługi IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: c4f994638420819da41a355d679cb03785b94a3a
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65832344"
---
# <a name="quickstart-sshrdp-over-an-iot-hub-device-stream-using-a-nodejs-proxy-application-preview"></a>Szybki start: SSH/RDP przez strumień urządzenie usługi IoT Hub przy użyciu serwera proxy aplikacji w języku Node.js (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub obsługuje obecnie strumieni urządzenia jako [funkcja w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. W tym przewodniku Szybki start opisano wykonywanie aplikacji serwera proxy Node.js uruchomionej po stronie usługi w celu obsługi ruchu SSH i RDP wysyłanego do urządzenia w strumieniu urządzenia. Zobacz [przykład lokalnego serwera proxy dla protokołu SSH lub RDP](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) Omówienie instalacji. W publicznej wersji zapoznawczej zestaw Node.js SDK obsługuje tylko strumienie urządzeń po stronie usługi. Dlatego ten przewodnik Szybki start zawiera tylko instrukcje uruchamiania serwera proxy usługi lokalnej. Należy uruchomić jeden z następujących przewodników Szybki Start urządzenia lokalnego serwera proxy:  

   * [SSH/RDP, za pośrednictwem strumieni urządzenia usługi IoT Hub przy użyciu aplikacji serwera proxy w języku C](./quickstart-device-streams-proxy-c.md)

   * [SSH/RDP, za pośrednictwem usługi IoT Hub device strumieni, za pomocą C# serwera proxy aplikacji](./quickstart-device-streams-proxy-csharp.md)

Najpierw opisano konfigurację protokołu SSH (za pomocą portu 22). Następnie opisano sposób modyfikowania konfiguracji protokołu RDP (który używa portu 3389). Strumienie urządzeń nie wymagają protokołu i aplikacji, dlatego można zmodyfikować ten sam przykład w celu obsługi innych typów ruchu klient/serwer aplikacji (zazwyczaj przez modyfikację portu komunikacyjnego).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Strumienie urządzenia w wersji zapoznawczej jest obecnie obsługiwane tylko w przypadku centrów IoT Hub są tworzone w następujących regionach:

   * **Środkowe stany USA**

   * **Central US EUAP**

Aby uruchomić aplikację lokalne usługi w tym przewodniku Szybki Start należy Node.js v10.x.x lub później na komputerze deweloperskim.

* Pobieranie środowiska Node.js dla wielu platform z [nodejs.org](https://nodejs.org).

Możesz sprawdzić bieżącą wersję środowiska Node.js na komputerze deweloperskim przy użyciu następującego polecenia:

```
node --version
```

* Uruchom następujące polecenie, aby dodać rozszerzenia usługi Microsoft Azure IoT dla interfejsu wiersza polecenia platformy Azure do swojego wystąpienia usługi Cloud Shell. Rozszerzenia IOT dodaje określone polecenia usługi IoT Hub, IoT Edge i usługi aprowizacji urządzeń IoT (DPS) do wiersza polecenia platformy Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

* Jeśli nie zostało to jeszcze zrobione, pobierz przykładowy projekt Node.js z https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip i wyodrębnij archiwum ZIP.

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

## <a name="ssh-to-a-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

W tej sekcji możesz ustanowić strumień end-to-end do tunelowania ruchu protokołu SSH.

### <a name="run-the-device-local-proxy"></a>Uruchamianie serwera proxy urządzenia lokalnego

Jak wspomniano wcześniej, zestaw Node.js SDK usługi IoT Hub obsługuje tylko strumienie urządzeń po stronie usługi. Dla aplikacji lokalnych urządzeń należy użyć jednej z dostępnych programów proxy urządzenia w jednym z następujących przewodników Szybki Start:

   * [SSH/RDP, za pośrednictwem strumieni urządzenia usługi IoT Hub przy użyciu języka C serwera proxy aplikacji](./quickstart-device-streams-proxy-c.md)

   * [SSH/RDP, za pośrednictwem usługi IoT Hub device strumieni, za pomocą C# aplikacje serwera proxy](./quickstart-device-streams-proxy-csharp.md). 

Przed przejściem do następnego kroku upewnij się, że serwer proxy urządzenia lokalnego jest uruchomiony.

### <a name="run-the-service-local-proxy"></a>Uruchamianie serwera proxy usługi lokalnej

Jeśli [serwer proxy urządzenia lokalnego](#run-the-device-local-proxy) jest uruchomiony, wykonaj poniższe czynności, aby uruchomić serwer proxy usługi lokalnej napisany w języku Node.js.

* Podaj poświadczenia usługi, identyfikator urządzenia docelowego, w którym jest uruchomiony demon SSH, i numer portu serwera proxy uruchomionego w urządzeniu jako zmienne środowiskowe.

```
# In Linux
export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
export STREAMING_TARGET_DEVICE="MyDevice"
export PROXY_PORT=2222

# In Windows
SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
SET STREAMING_TARGET_DEVICE=MyDevice
SET PROXY_PORT=2222
```

  Zmień powyższe wartości, aby były zgodne z Twoimi parametrami połączenia i identyfikatorem urządzenia.

* Przejdź do elementu `Quickstarts/device-streams-service` w folderze rozpakowanego projektu i uruchom serwer proxy usługi lokalnej.

```
cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

# Install the preview service SDK, and other dependencies
npm install azure-iothub@streams-preview
npm install

# Run the service-local proxy application
node proxy.js
```

### <a name="ssh-to-your-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

W systemie Linux uruchom protokół SSH przy użyciu elementu `ssh $USER@localhost -p 2222` w terminalu. W systemie Windows użyj swojego ulubionego klienta SSH (np. PuTTY).

Dane wyjściowe konsoli usługi lokalnej po utworzeniu sesji SSH (serwer proxy usługi lokalnej nasłuchuje na porcie 2222):

![Dane wyjściowe z terminalu SSH](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Dane wyjściowe konsoli programu klienta SSH (klient SSH komunikuje się z demonem SSH przez połączenie z portem 22, na którym nasłuchuje serwer proxy usługi lokalnej):

![Dane wyjściowe z klienta SSH](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>Połączenie RDP z urządzeniem za pośrednictwem strumieni urządzeń

Teraz użyj programu klienta RDP i nawiąż połączenie z serwerem proxy usługi na porcie 2222 (jest to port wybrany wcześniej).

> [!NOTE]
> Upewnij się, że serwer proxy urządzenia jest poprawnie skonfigurowany dla protokołu RDP i skonfigurowany przy użyciu portu RDP 3389.

![Protokół RDP, klient nawiąże połączenie z lokalnej usługi serwera proxy](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start skonfigurowano centrum IoT Hub, zarejestrowano urządzenie oraz wdrożono program serwera proxy usługi w celu włączenia obsługi połączeń SSH i RDP z urządzeniem IoT. Ruch RDP i SSH będzie tunelowany przez strumień urządzenia w usłudze IoT Hub. Eliminuje to konieczność nawiązywania bezpośredniego połączenia z urządzeniem.

Aby dowiedzieć się więcej na temat strumieni urządzeń, użyj poniższych linków:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
