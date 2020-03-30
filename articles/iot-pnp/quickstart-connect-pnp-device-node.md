---
title: Podłączenie przykładowego kodu urządzenia IoT Plug and Play Preview do centrum IoT Hub | Dokumenty firmy Microsoft
description: Za pomocą node.js, skompiluj i uruchom przykładowy kod urządzenia IoT Plug and Play Preview, który łączy się z centrum IoT hub. Użyj narzędzia Eksploratora IoT platformy Azure, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: c5d69ae21cd240b0c68b9694a55de2cf879a1966
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964791"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-nodejs"></a>Szybki start: łączenie przykładowej aplikacji urządzenia IoT Plug and Play Preview do centrum IoT Hub (node.js)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Ten przewodnik Szybki start pokazuje, jak utworzyć przykładową aplikację urządzenia IoT Plug and Play, połączyć ją z centrum IoT hub i użyć narzędzia do eksplorowania Usługi Azure IoT, aby wyświetlić informacje, które wysyła do centrum. Przykładowa aplikacja jest napisana dla node.js i jest uwzględniona w zestawie SDK urządzenia usługi Azure IoT Hub dla node.js. Deweloper rozwiązania można użyć narzędzia eksploratora Usługi Azure IoT, aby zrozumieć możliwości urządzenia Typu IoT Plug and Play bez konieczności wyświetlania kodu urządzenia.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten szybki start, potrzebujesz node.js na komputerze deweloperskim. Możesz pobrać najnowszą zalecaną wersję dla wielu platform z [nodejs.org](https://nodejs.org).

Możesz sprawdzić bieżącą wersję środowiska Node.js na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
node --version
```

### <a name="install-the-azure-iot-explorer"></a>Instalowanie eksploratora IoT platformy Azure

Pobierz i zainstaluj najnowszą wersję **eksploratora IoT platformy Azure** ze strony [repozytorium](https://github.com/Azure/azure-iot-explorer/releases) narzędzia, wybierając plik msi w obszarze "Zasoby" dla najnowszej aktualizacji.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby uzyskać _parametry połączenia centrum IoT_ dla koncentratora (uwaga do późniejszego użycia):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki start należy przygotować środowisko programistyczne, którego można użyć do klonowania i tworzenia zestawu SDK urządzeń usługi Azure IoT Hub dla node.js.

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować [zestaw Microsoft Azure IoT SDK dla](https://github.com/Azure/azure-iot-sdk-node) repozytorium Usługi Node.js GitHub w tej lokalizacji:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node --recursive -b digitaltwins-preview
```

Ta operacja może potrwać kilka minut.

## <a name="install-required-libraries"></a>Instalowanie wymaganych bibliotek

Użyj sdk urządzenia do tworzenia dołączonego przykładowego kodu. Aplikacja, która tworzysz symuluje urządzenie, które łączy się z centrum IoT hub. Aplikacja wysyła dane telemetryczne i właściwości i odbiera polecenia.

1. W oknie terminala lokalnego przejdź do folderu sklonowanego repozytorium i przejdź do folderu **/azure-iot-sdk-node/digitaltwins/samples/device/javascript.** Następnie uruchom następujące polecenie, aby zainstalować wymagane biblioteki:

    ```cmd/sh
    npm install
    ```
1. Konfigurowanie _ciągu połączenia urządzenia:_

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Uruchamianie przykładowego urządzenia

Uruchom przykładową aplikację w SDK, aby symulować urządzenie Typu Plug and Play IoT, które wysyła dane telemetryczne do centrum IoT hub. Aby uruchomić przykładową aplikację, użyj następującego polecenia:

```cmd\sh
    node sample_device.js
```

Zobaczysz następujące dane wyjściowe, wskazujące, że urządzenie rozpoczęło wysyłanie danych telemetrycznych do koncentratora i jest teraz gotowe do odbierania poleceń i aktualizacji właściwości.

   ![Komunikaty potwierdzające urządzenie](media/quickstart-connect-pnp-device/device-confirmation-node.png)

 Zachowaj uruchomienie próbki podczas wykonywania kolejnych kroków.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą Eksploratora IoT platformy Azure

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Aby upewnić się, że narzędzie może odczytać definicje modelu interfejsu z urządzenia, wybierz **ustawienia**. W menu Ustawienia **na podłączonym urządzeniu** może już pojawić się w konfiguracjach Plug and Play; jeśli tak nie jest, wybierz **+ Dodaj źródło definicji modułu,** a następnie **na podłączonym urządzeniu,** aby go dodać.

1. Na stronie **Przegląd urządzeń** znajdź wcześniej utworzoną tożsamość urządzenia. Gdy aplikacja urządzenia nadal działa w wierszu polecenia, sprawdź, czy **stan połączenia** urządzenia w Eksploratorze Usługi Azure IoT jest raportowanie jako _Połączone_ (jeśli nie, naciśnij **odśwież,** dopóki nie jest). Wybierz urządzenie, aby wyświetlić więcej szczegółów.

1. Rozwiń interfejs za pomocą **identyfikatora urn:contoso:com:EnvironmentalSensor:1,** aby odsłonić interfejs i prymitywy IoT Plug and Play — właściwości, polecenia i dane telemetryczne.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak podłączyć urządzenie Typu Plug and Play do centrum IoT hub. Aby dowiedzieć się więcej o tworzeniu rozwiązania współdziałanego z urządzeniami IoT Plug and Play, zobacz:

> [!div class="nextstepaction"]
> [Instrukcje: łączenie się z urządzeniem IoT Plug and Play Preview i interakcję z nim](howto-develop-solution.md)
