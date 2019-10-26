---
title: Usługa Azure IoT Hub urządzenia strumienie szybkiego startu Node. js dla protokołów SSH i RDP (wersja zapoznawcza) | Microsoft Docs
description: W tym przewodniku szybki start uruchomisz przykładową aplikację Node. js, która działa jako serwer proxy, aby umożliwić obsługę scenariuszy SSH i RDP przez IoT Hub strumieni urządzeń.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 676b05b94fad506fba7c5bd2bd00061ae8a0426b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900764"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Szybki Start: Włączanie protokołu SSH i RDP przez strumień urządzenia IoT Hub przy użyciu aplikacji serwera proxy środowiska Node. js (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub obecnie obsługuje strumienie urządzeń jako [funkcję w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. 

Ten przewodnik Szybki Start opisuje wykonywanie aplikacji serwera proxy Node. js, która jest uruchomiona po stronie usługi, aby umożliwić wysyłanie Secure Shell (SSH) i Remote Desktop Protocol (RDP) do urządzenia przez strumień urządzeń. Aby zapoznać się z omówieniem instalacji, zobacz [lokalny serwer proxy — przykład](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp). 

W publicznej wersji zapoznawczej zestaw SDK środowiska Node. js obsługuje strumienie urządzeń tylko po stronie usługi. W efekcie w tym przewodniku szybki start przedstawiono instrukcje dotyczące uruchamiania tylko aplikacji lokalnego serwera proxy usługi. Aby uruchomić aplikację lokalnego serwera proxy na urządzeniu, zobacz:  

   * [Włączanie strumieni urządzeń SSH i RDP przez IoT Hub przy użyciu aplikacji serwera proxy języka C](./quickstart-device-streams-proxy-c.md)
   * [Włączanie strumieni urządzeń SSH i RDP przez IoT Hub przy użyciu aplikacji C# serwera proxy](./quickstart-device-streams-proxy-csharp.md)

W tym artykule opisano konfigurację protokołu SSH (przy użyciu portu 22), a następnie opisano sposób modyfikowania konfiguracji dla połączeń RDP (która używa portu 3389). Ponieważ strumienie urządzeń są typu Application-and Protocol-niezależny od, można zmodyfikować ten sam przykład, aby pomieścić inne typy ruchu aplikacji klient-serwer, zazwyczaj modyfikując port komunikacyjny.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Podgląd strumieni urządzeń jest obecnie obsługiwany tylko w przypadku centrów IoT, które zostały utworzone w następujących regionach:

  * Środkowe stany USA
  * Środkowe stany USA — EUAP
  * Azja Południowo-Wschodnia
  * Europa Północna
  

* Do uruchomienia aplikacji lokalnej usługi w tym przewodniku szybki start potrzebny jest program Node. js v10. x. x lub nowszy na komputerze deweloperskim.
  * Pobieranie środowiska [Node. js](https://nodejs.org) dla wielu platform.
  * Sprawdź bieżącą wersję środowiska Node. js na komputerze deweloperskim, używając następującego polecenia:

   ```
   node --version
   ```

* Dodaj rozszerzenie Azure IoT dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell, uruchamiając następujące polecenie. Rozszerzenie IOT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* Jeśli jeszcze tego nie zrobiono, [Pobierz przykładowy projekt node. js](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip) i Wyodrębnij archiwum zip.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-node.md), możesz pominąć ten krok.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Jeśli ukończono [Szybki Start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-node.md), można pominąć ten krok.

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji użyjesz Azure Cloud Shell, aby zarejestrować symulowane urządzenie.

1. Aby utworzyć tożsamość urządzenia, uruchom następujące polecenie w Cloud Shell:

   > [!NOTE]
   > * Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla Centrum IoT Hub.
   > * W przypadku nazwy urządzenia, które rejestrujesz, zaleca się użycie *urządzenia* w sposób przedstawiony. Jeśli wybierzesz inną nazwę urządzenia, Użyj tej nazwy w tym artykule i zaktualizuj nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Aby umożliwić aplikacji zaplecza łączenie się z Centrum IoT Hub i pobieranie komunikatów, potrzebne są również *Parametry połączenia z usługą*. Następujące polecenie pobiera ciąg dla Centrum IoT Hub:

   > [!NOTE]
   > Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla Centrum IoT Hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

   Zwróć uwagę na zwrócone parametry połączenia usługi do późniejszego użycia w tym przewodniku Szybki Start. Wygląda to następująco:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

Ta sekcja umożliwia ustanowienie kompleksowego strumienia do tunelowania ruchu SSH.

### <a name="run-the-device-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy urządzenia lokalnego

Jak wspomniano wcześniej, zestaw SDK IoT Hub Node. js obsługuje strumienie urządzeń tylko po stronie usługi. W przypadku aplikacji lokalnej urządzenia Użyj aplikacji serwera proxy urządzenia, która jest dostępna w jednym z następujących przewodników szybki start:

   * [Włączanie strumieni urządzeń SSH i RDP przez IoT Hub przy użyciu aplikacji serwera proxy języka C](./quickstart-device-streams-proxy-c.md)
   * [Włączanie strumieni urządzeń SSH i RDP przez IoT Hub przy użyciu aplikacji C# serwera proxy](./quickstart-device-streams-proxy-csharp.md) 

Przed przejściem do następnego kroku upewnij się, że aplikacja lokalnego serwera proxy jest uruchomiona.

### <a name="run-the-service-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy usługi lokalnej

Po uruchomieniu aplikacji serwera proxy lokalnego na urządzeniu Uruchom aplikację lokalnego serwera proxy, która jest zapisywana w języku Node. js, wykonując następujące czynności w lokalnym oknie terminalu:

1. W przypadku zmiennych środowiskowych podaj poświadczenia usługi, identyfikator urządzenia docelowego, na którym działa demon SSH i numer portu serwera proxy uruchomionego na urządzeniu.

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

   Zmień symbol zastępczy serviceconnectionstring w taki sposób, aby pasował do parametrów połączenia z usługą, a **urządzenie** jest zgodne z identyfikatorem urządzenia, jeśli podałeś inną nazwę.

1. Przejdź do katalogu `Quickstarts/device-streams-service` w niespakowanym folderze projektu. Użyj następującego kodu, aby uruchomić aplikację lokalnego serwera proxy usługi:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

W systemie Linux Uruchom protokół SSH przy użyciu `ssh $USER@localhost -p 2222` w terminalu. W systemie Windows użyj ulubionego klienta SSH (na przykład.

Dane wyjściowe konsoli lokalnej usługi po ustanowieniu sesji SSH (aplikacja lokalnego serwera proxy nasłuchuje na porcie 2222):

![Dane wyjściowe terminalu SSH](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Dane wyjściowe konsoli aplikacji klienta SSH (klient SSH komunikuje się z demonem SSH przez połączenie z portem 22, w którym aplikacja lokalnego serwera proxy nasłuchuje):

![Dane wyjściowe klienta SSH](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>Połączenie RDP z urządzeniem za pośrednictwem strumieni urządzeń

Teraz należy używać aplikacji klienckiej RDP i nawiązać połączenie z serwerem proxy usługi na porcie 2222, wybranym wcześniej porcie.

> [!NOTE]
> Upewnij się, że serwer proxy urządzenia jest poprawnie skonfigurowany dla protokołu RDP i skonfigurowany przy użyciu portu RDP 3389.

![Klient RDP nawiązuje połączenie z aplikacją lokalnego serwera proxy usługi.](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz Centrum IoT, zarejestrowano urządzenie i wdrożono aplikację serwera proxy usługi, aby włączyć protokół RDP i SSH na urządzeniu IoT. Ruch RDP i SSH zostanie przetunelowany za pośrednictwem strumienia urządzenia za pośrednictwem Centrum IoT. Ten proces eliminuje konieczność bezpośredniej łączności z urządzeniem.

Aby dowiedzieć się więcej o strumieniach urządzeń, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
