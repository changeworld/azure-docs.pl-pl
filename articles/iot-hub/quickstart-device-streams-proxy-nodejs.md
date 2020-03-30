---
title: Urządzenie usługi Azure IoT Hub przesyła strumieniowo przewodnik Node.js dla usług SSH i RDP
description: W tym przewodniku Szybki start uruchom przykładową aplikację Node.js, która działa jako serwer proxy, aby włączyć scenariusze SSH i RDP za pomocą strumieni urządzeń Usługi IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c372a0a09fd3143f570aa4b316c9191e617c69e2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675459"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Szybki start: włączanie protokołu SSH i protokołu RDP za pośrednictwem strumienia urządzeń Usługi IoT Hub przy użyciu aplikacji proxy Node.js (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

W tym przewodniku Szybki start można włączyć ruch Wschowa (Secure Shell) i RDP (Remote Desktop Protocol) wysyłany do urządzenia za pomocą strumienia urządzenia. Strumienie urządzeń usługi Azure IoT Hub umożliwiają aplikacjom usługowym i urządzeń komunikowanie się w sposób bezpieczny i przyjazny dla zapory. Ten przewodnik Szybki start opisuje wykonanie aplikacji proxy Node.js, która jest uruchomiona po stronie usługi. Podczas publicznej wersji zapoznawczej zestaw SDK node.js obsługuje strumienie urządzeń tylko po stronie usługi. W rezultacie ten szybki start obejmuje instrukcje uruchamiania tylko aplikacji serwera proxy lokalnego usługi.

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończenie [włączania strumieni urządzeń SSH i RDP za pośrednictwem usługi IoT Hub przy użyciu aplikacji proxy Języka C](./quickstart-device-streams-proxy-c.md) lub [włączania strumieni urządzeń SSH i RDP za pośrednictwem usługi IoT Hub przy użyciu aplikacji proxy języka C#.](./quickstart-device-streams-proxy-csharp.md)

* Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10+](https://nodejs.org).

* [Przykładowy projekt Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

Bieżącą wersję pliku Node.js można zweryfikować na komputerze deweloperskim za pomocą następującego polecenia:

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

Dodaj rozszerzenie Usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure do wystąpienia usługi Cloud Shell, uruchamiając następujące polecenie. Rozszerzenie IoT dodaje IoT Hub, IoT Edge i IoT Device Provisioning Service (DPS) polecenia specyficzne dla interfejsu wiersza polecenia.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-node.md), możesz pominąć ten krok.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Po zakończeniu [programu Szybki start: Wysyłanie danych telemetrycznych z urządzenia do centrum IoT hub](quickstart-send-telemetry-node.md)można pominąć ten krok.

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji używasz usługi Azure Cloud Shell do zarejestrowania symulowanego urządzenia.

1. Aby utworzyć tożsamość urządzenia, uruchom następujące polecenie w usłudze Cloud Shell:

   > [!NOTE]
   > * Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla centrum IoT.
   > * W przypadku nazwy zarejestrowanego urządzenia zaleca się użycie urządzenia *MyDevice* w sposób pokazany na rysunku. Jeśli wybierzesz inną nazwę urządzenia, użyj tej nazwy w tym artykule i zaktualizuj nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Aby umożliwić aplikacji zaplecza łączenie się z centrum IoT hub i pobieranie wiadomości, potrzebny jest również *ciąg połączenia usługi*. Następujące polecenie pobiera ciąg dla centrum IoT hub:

   > [!NOTE]
   > Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla centrum IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

   Zanotuj zwrócony ciąg połączenia usługi do późniejszego użycia w tym przewodniku Szybki start. Wygląda to następująco:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

W tej sekcji ustanawiasz strumień end-to-end do tunelu ruchu SSH.

### <a name="run-the-device-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy urządzenia lokalnego

Jak wspomniano wcześniej, zestaw SDK node.js usługi IoT Hub obsługuje strumienie urządzeń tylko po stronie usługi. W przypadku aplikacji lokalnej urządzenia użyj aplikacji serwera proxy urządzenia, która jest dostępna w jednym z następujących przewodników Szybki start:

   * [Włączanie strumieni urządzeń SSH i RDP za pośrednictwem usługi IoT Hub przy użyciu aplikacji proxy języka C](./quickstart-device-streams-proxy-c.md)
   * [Włączanie strumieni urządzeń SSH i RDP za pośrednictwem usługi IoT Hub przy użyciu aplikacji proxy języka C#](./quickstart-device-streams-proxy-csharp.md) 

Przed przejściem do następnego kroku upewnij się, że jest uruchomiona aplikacja serwera proxy lokalnego urządzenia. Aby uzyskać omówienie konfiguracji, zobacz [Przykład lokalnego serwera proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

### <a name="run-the-service-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy usługi lokalnej

W tym artykule opisano konfigurację protokołu SSH (przy użyciu portu 22), a następnie opisano sposób modyfikowania konfiguracji protokołu RDP (która używa portu 3389). Ponieważ strumienie urządzeń są niezależni od aplikacji i protokołu, można zmodyfikować ten sam przykład, aby uwzględnić inne typy ruchu aplikacji klient-serwer, zwykle modyfikując port komunikacyjny.

Po uruchomieniu aplikacji serwera proxy lokalnego urządzenia uruchom aplikację serwera proxy lokalnego usługi, która jest napisana w pliku Node.js, wykonując następujące czynności w oknie terminala lokalnego:

1. W przypadku zmiennych środowiskowych podaj poświadczenia usługi, identyfikator urządzenia docelowego, w którym działa demon SSH, oraz numer portu serwera proxy działającego na urządzeniu.

   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"
   export PROXY_PORT=2222

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   SET PROXY_PORT=2222
   ```

   Zmień symbol zastępczy ServiceConnectionString, aby dopasować parametry połączenia usługi, i **MyDevice,** aby dopasować identyfikator urządzenia, jeśli nadano jej inną nazwę.

1. Przejdź do `Quickstarts/device-streams-service` katalogu w rozpakowanym folderze projektu. Użyj następującego kodu, aby uruchomić aplikację serwera proxy lokalnego usługi:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

W systemie Linux uruchom `ssh $USER@localhost -p 2222` SSH przy użyciu terminala. W systemie Windows użyj ulubionego klienta SSH (na przykład PuTTY).

Wyjście konsoli na lokalnym usłudze po ustanowieniu sesji SSH (aplikacja serwera proxy lokalnego usługi nasłuchuje na porcie 2222):

![Wyjście zacisku SSH](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Wyjście konsoli aplikacji klienckiej SSH (klient SSH komunikuje się z demonem SSH, łącząc się z portem 22, w którym nasłuchuje lokalna aplikacja proxy usługi):

![Dane wyjściowe klienta SSH](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>Połączenie RDP z urządzeniem za pośrednictwem strumieni urządzeń

Teraz użyj aplikacji klienckiej RDP i połącz się z serwerem proxy usługi na porcie 2222, dowolnym porcie, który został wybrany wcześniej.

> [!NOTE]
> Upewnij się, że serwer proxy urządzenia jest poprawnie skonfigurowany dla protokołu RDP i skonfigurowany przy użyciu portu RDP 3389.

![Klient RDP łączy się z aplikacją serwera proxy lokalnego usługi](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurujesz centrum IoT hub, zarejestrowano urządzenie i wdrożono aplikację serwera proxy usługi, aby włączyć prow i usługę SSH na urządzeniu IoT. Ruch RDP i SSH będzie tunelowany przez strumień urządzeń przez centrum IoT hub. Ten proces eliminuje potrzebę bezpośredniej łączności z urządzeniem.

Aby dowiedzieć się więcej o strumieniach urządzeń, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
