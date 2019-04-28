---
title: Protocol Buffers za pomocą symulacji urządzenia — Azure | Dokumentacja firmy Microsoft
description: W tym przewodniku z instrukcjami nauczysz się używać Protocol Buffers do wykonywania serializacji dane telemetryczne wysyłane z akceleratora rozwiązań symulacji urządzenia.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 74bb2d181533f802e1428eaa8a855f60fb855193
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447985"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serializowanie danych telemetrycznych za pomocą Protocol Buffers

Protocol Buffers (Protobuf) jest binarny format serializacji danych strukturalnych. Formatu Protobuf zaprojektowano w celu wyróżnienia prostoty i wydajności za pomocą celem są mniejsze i szybsze niż XML.

Symulacja urządzenia obsługuje **proto3** wersję protokołu buforuje języka.

Formatu Protobuf wymagają skompilowany kod do serializowania danych, dlatego należy utworzyć niestandardową wersję symulacji urządzenia.

Kroki opisane w tym jak-to-przewodniku pokazano, jak do:

1. Przygotowywanie środowiska deweloperskiego
1. Określ, przy użyciu formatu Protobuf w modelu urządzenia
1. Definiowanie formatu Protobuf
1. Generuj klasy Protobuf
1. Przetestować ją lokalnie

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, potrzebne są:

* Program Visual Studio Code. Możesz pobrać [programu Visual Studio Code dla komputerów Mac, Linux i Windows](https://code.visualstudio.com/download).
* .NET Core. Możesz pobrać [platformy .NET Core dla systemów Mac, Linux i Windows](https://www.microsoft.com/net/download).
* Postman. Możesz pobrać [Postman dla komputerów Mac, windows lub Linux](https://www.getpostman.com/apps).
* [Wdrożone do subskrypcji platformy Azure w Centrum IoT hub](../iot-hub/iot-hub-create-through-portal.md). Będą potrzebne parametry połączenia Centrum IoT, wykonanie czynności opisanych w tym przewodniku. Parametry połączenia można uzyskać w witrynie Azure portal.
* A [bazy danych Cosmos DB wdrożone do subskrypcji platformy Azure](../cosmos-db/create-sql-api-dotnet.md#create-account) używającej interfejsu API SQL, który jest skonfigurowany dla [wysoki poziom spójności](../cosmos-db/manage-account.md). Będą potrzebne parametry połączenia bazy danych Cosmos DB, wykonanie czynności opisanych w tym przewodniku. Parametry połączenia można uzyskać w witrynie Azure portal.
* [Konta magazynu platformy Azure wdrożone do subskrypcji platformy Azure](../storage/common/storage-quickstart-create-account.md). Będą potrzebne parametry połączenia konta magazynu, wykonanie czynności opisanych w tym przewodniku. Parametry połączenia można uzyskać w witrynie Azure portal.

## <a name="prepare-your-development-environment"></a>Przygotowywanie środowiska projektowego

Wykonaj następujące zadania w celu przygotowania swojego środowiska projektowego:

* Pobierz źródła dla mikrousług symulacji urządzenia.
* Pobierz źródła dla mikrousług adapter magazynu.
* Uruchom lokalnie mikrousług adapter magazynu.

Instrukcje w tym artykule przyjęto założenie, że używasz Windows. Jeśli używasz innego systemu operacyjnego może być konieczne dostosowanie niektórych ścieżek plików i poleceń w zależności od środowiska.

### <a name="download-the-microservices"></a>Pobierz mikrousług

Pobierz i Rozpakuj [zdalnego monitorowania Mikrousług](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) z usługi GitHub do odpowiedniej lokalizacji na komputerze lokalnym. To repozytorium zawiera mikrousług adapter magazynu, czego potrzebujesz do instruktażu.

Pobierz i Rozpakuj [mikrousług symulacji urządzenia](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) z usługi GitHub do odpowiedniej lokalizacji na komputerze lokalnym.

### <a name="run-the-storage-adapter-microservice"></a>Uruchamianie mikrousług adapter magazynu

W programie Visual Studio Code Otwórz **remote-monitoring-services-dotnet-master\storage-adapter** folderu. Kliknij dowolny **przywrócić** przycisków, aby naprawić nierozwiązane zależności.

Otwórz **.vscode/launch.json** pliku i parametry połączenia usługi Cosmos DB, aby przypisać **komputerów\_STORAGEADAPTER\_bazy danych DOCUMENTDB\_CONNSTRING** środowiska Zmienna.

> [!NOTE]
> Po uruchomieniu mikrousługi lokalnie na komputerze nadal wymaga wystąpienia usługi Cosmos DB na platformie Azure, aby zapewnić prawidłowe działanie.

Aby lokalnie uruchomić mikrousług adapter magazynu, kliknij **debugowania \> Rozpocznij debugowanie**.

**Terminalu** okna w programie Visual Studio Code przedstawiono dane wyjściowe z uruchomionego mikrousług, łącznie z adresu URL dla sprawdzenie kondycji usługi sieci web: <http://127.0.0.1:9022/v1/status>. Po przejściu do tego adresu powinna być w stanie "OK: Aktywne i dobrze".

Pozostaw mikrousług adapter magazynu, używaną w tym wystąpieniu programu Visual Studio Code wykonaj następujące czynności.

## <a name="define-your-device-model"></a>Zdefiniuj modelu urządzenia

Otwórz **urządzeń symulacji dotnet-master** folder został pobrany z usługi GitHub w nowym wystąpieniu programu Visual Studio Code. Kliknij dowolny **przywrócić** przycisków, aby poprawić nierozwiązane zależności.

W tym jak-to-przewodniku utworzysz nowy model urządzenia śledzenie zasobów:

1. Utwórz nowy plik modelu urządzenia o nazwie **assettracker 01.json** w **Services\data\devicemodels** folderu.

1. Definiowanie funkcji urządzenia w modelu urządzenia **assettracker 01.json** pliku. Dane telemetryczne części modelu urządzenia Protobuf musi:

   * Na przykład nazwa klasy Protobuf, która generuje dla urządzenia. Poniższej sekcji przedstawiono sposób generowania tej klasy.
   * Określ Protobuf jako format komunikatu.

     ```json
     {
     "SchemaVersion": "1.0.0",
     "Id": "assettracker-01",
     "Version": "0.0.1",
     "Name": "Asset Tracker",
     "Description": "An asset tracker with location, temperature, and humidity",
     "Protocol": "AMQP",
     "Simulation": {
       "InitialState": {
         "online": true,
         "latitude": 47.445301,
         "longitude": -122.296307,
         "temperature": 38.0,
         "humidity": 62.0
       },
       "Interval": "00:01:00",
       "Scripts": [
         {
           "Type": "javascript",
           "Path": "assettracker-01-state.js"
         }
       ]
     },
     "Properties": {
       "Type": "AssetTracker",
       "Location": "Field",
       "Latitude": 47.445301,
       "Longitude": -122.296307
     },
     "Telemetry": [
       {
         "Interval": "00:00:10",
         "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
         "MessageSchema": {
           "Name": "assettracker-sensors;v1",
           "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
           "Format": "Protobuf",
           "Fields": {
             "latitude": "double",
             "longitude": "double",
             "temperature": "double",
             "humidity": "double"
           }
         }
       }
     ]
     }
     ```

### <a name="create-device-behaviors-script"></a>Utwórz skrypt zachowania urządzenia

Napisać skrypt zachowanie, który definiuje, jak zachowuje się urządzenia. Aby uzyskać więcej informacji, zobacz [tworzenie zaawansowanych symulowanego urządzenia](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>Definiowanie formatu Protobuf

Gdy korzystasz z modelu urządzenia i określono formatu wiadomości, możesz utworzyć **proto** pliku. W **proto** pliku, możesz dodać:

* A `csharp_namespace` odpowiadający **ClassName** właściwości w modelu urządzenia.
* Komunikat dla każdej struktury danych, do serializacji.
* Nazwa i typ dla każdego pola w komunikacie.

1. Utwórz nowy plik o nazwie **assettracker.proto** w **Services\Models\Protobuf\proto** folderu.

1. Zdefiniuj składni, przestrzeń nazw i schemat wiadomości w **proto** pliku w następujący sposób:

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

`=1`, `=2` Znaczniki dla każdego elementu w kodowanie binarne określić unikatowych tagów używa pola. Liczby od 1 do 15 wymagają mniej jednobajtowego do zakodowania niż tymi o wyższych numerach.

## <a name="generate-the-protobuf-class"></a>Wygenerowana klasa Protobuf

Jeśli masz **proto** pliku, następnym krokiem jest do generowania klasy potrzebnych do odczytywania i zapisywania komunikatów. Aby ukończyć ten krok, musisz mieć **Protoc** Protobuf kompilatora.

1. [Pobieranie kompilatora Protobuf z usługi GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Uruchom kompilatora, określając katalog źródłowy, katalog docelowy i nazwę użytkownika **proto** pliku. Na przykład:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    To polecenie spowoduje wygenerowanie **Assettracker.cs** w pliku **Services\Models\Protobuf** folderu.

## <a name="test-protobuf-locally"></a>Test Protobuf lokalnie

W tej sekcji możesz przetestować urządzenia śledzenie zasobów, utworzony w poprzednich sekcjach lokalnie.

### <a name="run-the-device-simulation-microservice"></a>Uruchamianie mikrousług symulacji urządzenia

Otwórz **.vscode/launch.json** plików i przypisz użytkownika:

* Parametry połączenia Centrum IoT Hub **komputerów\_IOTHUB\_CONNSTRING** zmiennej środowiskowej.
* Parametry połączenia konta magazynu do **komputerów\_AZURE\_MAGAZYNU\_konta** zmiennej środowiskowej.
* Parametry połączenia usługi cosmos DB **komputerów\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** zmiennej środowiskowej.

Otwórz **WebService/Properties/launchSettings.json** plików i przypisz użytkownika:

* Parametry połączenia Centrum IoT Hub **komputerów\_IOTHUB\_CONNSTRING** zmiennej środowiskowej.
* Parametry połączenia konta magazynu do **komputerów\_AZURE\_MAGAZYNU\_konta** zmiennej środowiskowej.
* Parametry połączenia usługi cosmos DB **komputerów\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** zmiennej środowiskowej.

Otwórz **WebService\appsettings.ini** plik i zmodyfikować następujące ustawienia:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Konfigurowanie rozwiązania, aby uwzględnić nowe pliki modelu urządzenia

Domyślnie nowy model urządzenia JSON i plikami JS nie będą kopiowane do wbudowanego rozwiązania. Musisz jawnie włączyć je.

Dodaj wpis do **services\services.csproj** pliku dla każdego pliku, które chcesz uwzględnić. Na przykład:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Aby uruchomić mikrousługi lokalnie, kliknij przycisk **debugowania \> Rozpocznij debugowanie**.

**Terminalu** okna w programie Visual Studio Code przedstawiono dane wyjściowe z uruchomionego mikrousług.

Pozostaw mikrousług symulacji urządzenia używaną w tym wystąpieniu programu Visual Studio Code wykonaj kolejne kroki.

### <a name="set-up-a-monitor-for-device-events"></a>Konfigurowanie monitora dla zdarzeń urządzenia

W tej sekcji użyjesz interfejsu wiersza polecenia platformy Azure do konfigurowanie Monitora zdarzeń, aby wyświetlić dane telemetryczne wysyłane z urządzeń podłączonych do Centrum IoT.

Poniższy skrypt zakłada, że nazwa usługi IoT hub jest **urządzenia Symulacja testu**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Pozostaw Monitora zdarzeń uruchamiania podczas testowania symulowanych urządzeń.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Symulację z typem urządzenia śledzenie zasobów

W tej sekcji umożliwia narzędzie Postman żądania mikrousługi symulacji urządzenia, uruchomić symulację przy użyciu typu urządzenia śledzenia zasobów. Postman to narzędzie, które pozwala wysyłać żądania REST z usługą sieci web.

Aby skonfigurować narzędzie Postman:

1. Otwórz narzędzie Postman na komputerze lokalnym.

1. Kliknij przycisk **pliku \> importu**. Następnie kliknij przycisk **Wybieranie plików**.

1. Wybierz **symulacji urządzenia IoT Azure rozwiązanie accelerator.postman\_kolekcji** i **symulacji urządzenia IoT Azure rozwiązanie accelerator.postman\_środowiska** i Kliknij przycisk **Otwórz**.

1. Rozwiń **akcelerator rozwiązań symulacji urządzenia IoT Azure** umożliwia wyświetlanie żądań, możesz wysłać.

1. Kliknij przycisk **środowiska bez** i wybierz **akcelerator rozwiązań symulacji urządzenia IoT Azure**.

Masz teraz zbierania i środowiska załadowane w Twoim obszarze roboczym narzędzia Postman, używanej do interakcji z mikrousług symulacji urządzenia.

Aby skonfigurować i uruchomić symulację:

1. W kolekcji postman Collection, wybierz **symulację śledzenie zasobów** i kliknij przycisk **wysyłania**. To żądanie tworzy cztery wystąpienia typu urządzenia symulowanego zasobów śledzenia.

1. Dane wyjściowe Monitora zdarzeń w oknie wiersza polecenia platformy Azure przedstawia dane telemetryczne z symulowanych urządzeń.

Aby zatrzymać symulacji, wybierz **zatrzymywać symulację** żądania narzędzia Postman, a następnie kliknij przycisk **wysyłania**.

### <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz zatrzymać dwóch mikrousługi lokalnie uruchomionej w ich wystąpieniach programu Visual Studio Code (**debugowania \> Zatrzymaj debugowanie**).

Jeśli nie potrzebujesz już wystąpień usługi IoT Hub i Cosmos DB, usuń je z subskrypcji platformy Azure, aby uniknąć wszelkich zbędnych opłat.

## <a name="iot-hub-support"></a>Obsługa usługi IoT Hub

Wiele funkcji usługi IoT Hub nie obsługują natywnie Protobuf lub inne formaty binarne. Na przykład nie można skierować połączenia opartego na ładunek komunikatu, ponieważ usługa IoT Hub będzie mógł przetwarzać ładunek komunikatu. Można jednak przesyłać, oparte na nagłówki wiadomości.

## <a name="next-steps"></a>Kolejne kroki

Teraz wyjaśniono sposób dostosowywania symulacji urządzenia na potrzeby wysyłania danych telemetrycznych Protobuf, następnym krokiem jest teraz Dowiedz się, jak [wdrażanie niestandardowych obrazów w chmurze](iot-accelerators-device-simulation-deploy-image.md).
