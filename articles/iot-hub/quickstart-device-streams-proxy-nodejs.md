---
title: Usługa Azure IoT Hub device strumieni szybkiego startu Node.js dla protokołu SSH i RDP (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki Start możesz uruchomić przykładowej aplikacji Node.js, która działa jako serwer proxy w celu włączenia protokołu SSH i RDP scenariuszy za pośrednictwem usługi IoT Hub strumieni urządzenia.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 83339273d9161c3947df191d10e788980db39b28
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446019"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Szybki start: Włącz protokół SSH i RDP przez strumień urządzenie usługi IoT Hub przy użyciu serwera proxy aplikacji w języku Node.js (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub obsługuje obecnie strumieni urządzenia jako [funkcja w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. 

Ten przewodnik Szybki Start opisano wykonywanie Node.js serwera proxy aplikacji, który działa na stronie usługi, aby umożliwić Secure Shell (SSH) i ruch protokołu RDP (Remote Desktop) do wysłania do urządzenia za pomocą strumienia urządzenia. Omówienie konfiguracji, zobacz [lokalnego przykład serwera Proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp). 

W publicznej wersji zapoznawczej zestaw Node.js SDK obsługuje strumienie urządzenia tylko na stronie usługi. W wyniku tego przewodnika Szybki Start omówiono instrukcjami, aby uruchomić tylko aplikację usługi lokalnego serwera proxy. Aby uruchomić aplikację urządzenia lokalnego serwera proxy, zobacz:  

   * [Włącz SSH i RDP przez strumieni urządzenia usługi IoT Hub przy użyciu serwera proxy aplikacji języka C](./quickstart-device-streams-proxy-c.md)
   * [Włączanie protokołu SSH i RDP za pośrednictwem strumieni urządzenia usługi IoT Hub przy użyciu C# serwera proxy aplikacji](./quickstart-device-streams-proxy-csharp.md)

W tym artykule opisano ustawienia dla protokołu SSH (przy użyciu portu 22) i następnie w tym artykule opisano sposób modyfikowania ustawień dla protokołu RDP (który jest używany port 3389). Ponieważ strumieni urządzenia — i protokołu — niezależnie od aplikacji, można zmodyfikować tej samej próbki, aby pomieścić inne rodzaje ruchu aplikacji klient serwer, zwykle przez zmodyfikowanie portów komunikacji.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Strumienie urządzenia w wersji zapoznawczej jest obecnie obsługiwane tylko w przypadku centrów IoT, które są tworzone w następujących regionach:

  * Środkowe stany USA
  * Środkowe stany USA — EUAP

* Aby uruchomić aplikację usługi lokalnej, w tym przewodniku Szybki Start, należy Node.js v10.x.x lub później na komputerze deweloperskim.
  * Pobierz [Node.js](https://nodejs.org) dla wielu platform.
  * Sprawdź bieżącą wersję środowiska Node.js na komputerze deweloperskim, za pomocą następującego polecenia:

   ```
   node --version
   ```

* Dodaj rozszerzenia usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure do swojego wystąpienia usługi Cloud Shell, uruchamiając następujące polecenie. Dodaje rozszerzenia IOT, IoT Hub, IoT Edge i usługi aprowizacji urządzeń IoT (DPS)-konkretnych poleceń interfejsu wiersza polecenia platformy Azure.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* Jeśli użytkownik jeszcze tego nie zrobiono, [Pobierz przykładowy projekt Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip) i Wyodrębnij archiwum ZIP.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-node.md), możesz pominąć ten krok.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Jeśli ukończono [Szybki Start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-node.md), możesz pominąć ten krok.

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji użyjesz usługi Azure Cloud Shell zarejestrować symulowane urządzenie.

1. Aby utworzyć tożsamości urządzenia, uruchom następujące polecenie w usłudze Cloud Shell:

   > [!NOTE]
   > * Zastąp *YourIoTHubName* symbol zastępczy wybranej nazwy Centrum IoT.
   > * Użyj *MyDevice*, jak pokazano. Jest to nazwa podana dla zarejestrowanych urządzeń. Jeśli wybierzesz inną nazwę dla danego urządzenia, używać tej nazwy, w tym artykule i zaktualizuj nazwę urządzenia w przykładowych aplikacji, można uruchomić.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Aby umożliwić aplikacji zaplecza nawiązać połączenie z Centrum IoT i pobieranie wiadomości, należy również *parametry połączenia usługi*. Następujące polecenie pobiera parametry dla Centrum IoT:

   > [!NOTE]
   > Zastąp *YourIoTHubName* symbol zastępczy wybranej nazwy Centrum IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Należy pamiętać, zwrócona wartość do późniejszego użycia w tym przewodniku Szybki Start. Wygląda to następująco:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

W tej sekcji możesz ustanowić strumień end-to-end do tunelowania ruchu protokołu SSH.

### <a name="run-the-device-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy urządzenia lokalnego

Jak wspomniano wcześniej, zestawu SDK środowiska Node.js usługi IoT Hub obsługuje strumienie urządzenia tylko na stronie usługi. Dla aplikacji lokalnych urządzeń należy użyć urządzenia serwera proxy aplikacji, które są dostępne w jednym z następujących przewodników Szybki Start:

   * [Włącz SSH i RDP przez strumieni urządzenia usługi IoT Hub przy użyciu serwera proxy aplikacji języka C](./quickstart-device-streams-proxy-c.md)
   * [Włączanie protokołu SSH i RDP za pośrednictwem strumieni urządzenia usługi IoT Hub przy użyciu C# serwera proxy aplikacji](./quickstart-device-streams-proxy-csharp.md) 

Przed przystąpieniem do następnego kroku upewnij się, że urządzenia lokalnego serwera proxy aplikacji działa.

### <a name="run-the-service-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy usługi lokalnej

Działanie aplikacji serwera proxy w lokalnych urządzeń i uruchamianie aplikacji serwera proxy usługi lokalnej, która jest napisany w języku Node.js, wykonując następujące czynności:

1. Zmienne środowiskowe Podaj swoje poświadczenia usługi, identyfikator urządzenia docelowego, na którym jest uruchomiony demon SSH i numer portu serwera proxy, który działa na urządzeniu.

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

   Zmień powyższe wartości, aby dopasować parametry połączenia i identyfikator urządzenia.

1. Przejdź do *przewodników Szybki Start/urządzeń — strumienie usługa* katalogu w folderze rozpakowanego projektu i uruchom aplikację serwera proxy usług lokalnych.

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

W systemie Linux, uruchom SSH przy użyciu `ssh $USER@localhost -p 2222` z poziomu terminalu. W Windows należy użyć swojego ulubionego klienta SSH (na przykład program PuTTY).

Dane wyjściowe z konsoli w lokalnych usług po ustanowieniu sesji SSH (serwer proxy usługi lokalnej aplikacja nasłuchuje na porcie 2222):

![Dane wyjściowe z terminalu SSH](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Dane wyjściowe aplikacji klienta SSH konsoli (do demona SSH, łącząc się z portu 22, którego aplikacja lokalnej usługi Serwer proxy nasłuchuje na komunikuje się klient SSH):

![Dane wyjściowe z klienta SSH](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>Połączenie RDP z urządzeniem za pośrednictwem strumieni urządzeń

Teraz za pomocą aplikacji klienta RDP i połączyć się z serwerem proxy usługi na porcie 2222, z dowolnego portu, którą wybrano wcześniej.

> [!NOTE]
> Upewnij się, że serwer proxy urządzenia jest poprawnie skonfigurowany dla protokołu RDP i skonfigurowany przy użyciu portu RDP 3389.

![Klient protokołu RDP nawiąże połączenie z lokalnej usługi serwera proxy aplikacji](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start zostało Konfigurowanie usługi IoT hub, zarejestrowane urządzenia z systemem i wdrożonych aplikacji serwera proxy usługi, aby włączyć protokół RDP i SSH na urządzeniu IoT. Ruch RDP i SSH będzie tunelowany przez strumień urządzenia za pomocą usługi IoT hub. Ten proces, który eliminuje potrzebę bezpośredniego połączenia z urządzeniem.

Aby dowiedzieć się więcej na temat urządzeń, strumieni, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
