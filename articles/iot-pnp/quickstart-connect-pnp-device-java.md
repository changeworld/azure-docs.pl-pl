---
title: Podłączenie przykładowego kodu urządzenia IoT Plug and Play Preview do centrum IoT Hub | Dokumenty firmy Microsoft
description: Za pomocą języka Java, skompiluj i uruchom przykładowy kod urządzenia IoT Plug and Play Preview, który łączy się z centrum IoT hub. Użyj narzędzia Eksploratora IoT platformy Azure, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: aa676dd374eccf2a4b5c4622689ed402c8679e5a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964831"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-java"></a>Szybki start: łączenie przykładowej aplikacji urządzenia IoT Plug and Play Preview do usługi IoT Hub (Java)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Ten przewodnik Szybki start pokazuje, jak utworzyć przykładową aplikację urządzenia IoT Plug and Play, połączyć ją z centrum IoT hub i użyć narzędzia do eksplorowania Usługi Azure IoT, aby wyświetlić informacje, które wysyła do centrum. Przykładowa aplikacja jest napisana w języku Java i jest dostarczana jako część kolekcji Azure IoT Samples for Java. Deweloper rozwiązania można użyć narzędzia eksploratora Usługi Azure IoT, aby zrozumieć możliwości urządzenia Typu IoT Plug and Play bez konieczności wyświetlania kodu urządzenia.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten szybki start, potrzebujesz java SE 8 na komputerze deweloperskim. Musisz również zainstalować Maven 3.

Aby uzyskać szczegółowe informacje na temat konfigurowania tych funkcji, zobacz [Przygotowywanie środowiska programistycznego](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) w zestawie SDK urządzenia IoT platformy Microsoft Azure dla środowiska Java.

### <a name="install-the-azure-iot-explorer"></a>Instalowanie eksploratora IoT platformy Azure

Pobierz i zainstaluj najnowszą wersję **eksploratora IoT platformy Azure** ze strony [repozytorium](https://github.com/Azure/azure-iot-explorer/releases) narzędzia, wybierając plik msi w obszarze "Zasoby" dla najnowszej aktualizacji.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby uzyskać _parametry połączenia centrum IoT_ dla koncentratora (uwaga do późniejszego użycia):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki start przygotowujesz środowisko programistyczne, którego można użyć do klonowania i tworzenia przykładów usługi Azure IoT dla środowiska Java.

Otwórz okno terminala w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować [przykłady usługi Azure IoT dla](https://github.com/Azure-Samples/azure-iot-samples-java) repozytorium Java GitHub w tej lokalizacji:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-java
```

Ta operacja może potrwać kilka minut.

## <a name="build-the-code"></a>Kompilowanie kod

Sklonowany przykładowy kod służy do tworzenia aplikacji symulującej urządzenie, które łączy się z centrum IoT hub. Aplikacja wysyła dane telemetryczne i właściwości i odbiera polecenia.

1. W oknie terminala lokalnego przejdź do folderu sklonowanego repozytorium i przejdź do folderu **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample** folderu. Następnie uruchom następujące polecenie, aby zainstalować wymagane biblioteki i utworzyć symulowaną aplikację urządzenia:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Konfigurowanie _ciągu połączenia urządzenia:_

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-device-sample"></a>Uruchamianie próbki urządzenia

Uruchom przykładową aplikację, aby symulować urządzenie Typu Plug and Play IoT, które wysyła dane telemetryczne do centrum IoT hub. Aby uruchomić przykładową aplikację, użyj następującego polecenia:

```cmd\sh
java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
```

Są widoczne komunikaty informujące, że urządzenie jest połączone, wykonywanie różnych kroków konfiguracji i oczekiwanie na aktualizacje usługi, a następnie dzienniki telemetrii. Oznacza to, że urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do koncentratora. Zachowaj uruchomienie próbki podczas wykonywania kolejnych kroków.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą Eksploratora IoT platformy Azure

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Aby upewnić się, że narzędzie może odczytać definicje modelu interfejsu z urządzenia, wybierz **ustawienia**. W menu Ustawienia **na podłączonym urządzeniu** może już pojawić się w konfiguracjach Plug and Play; jeśli tak nie jest, wybierz **+ Dodaj źródło definicji modułu,** a następnie **na podłączonym urządzeniu,** aby go dodać.

1. Na stronie **Przegląd urządzeń** znajdź wcześniej utworzoną tożsamość urządzenia. Gdy aplikacja urządzenia nadal działa w wierszu polecenia, sprawdź, czy **stan połączenia** urządzenia w Eksploratorze Usługi Azure IoT jest raportowanie jako _Połączone_ (jeśli nie, naciśnij **odśwież,** dopóki nie jest). Wybierz urządzenie, aby wyświetlić więcej szczegółów.

1. Rozwiń interfejs za pomocą **identyfikatora urn:java_sdk_sample:EnvironmentalSensor:1,** aby odsłonić interfejs i prymitywy IoT Plug and Play — właściwości, polecenia i dane telemetryczne.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak podłączyć urządzenie Typu Plug and Play do centrum IoT hub. Aby dowiedzieć się więcej o tworzeniu rozwiązania współdziałanego z urządzeniami IoT Plug and Play, zobacz:

> [!div class="nextstepaction"]
> [Instrukcje: łączenie się z urządzeniem IoT Plug and Play Preview i interakcję z nim](howto-develop-solution.md)
