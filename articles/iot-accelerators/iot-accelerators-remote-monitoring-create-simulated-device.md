---
title: Symulacja urządzenia za pomocą zdalnego monitorowania IoT — Azure | Dokumentacja firmy Microsoft
description: Ten poradnik pokazuje, jak za pomocą usług symulator urządzenia zdalnego akcelerator rozwiązań do monitorowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 5044f8b85e59911633a4ffab509efc000948144a
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65832577"
---
# <a name="create-and-test-a-new-simulated-device"></a>Tworzenie i testowanie nowych symulowanych urządzeń

Akcelerator rozwiązania monitorowania zdalnego umożliwia definiowanie symulowanych urządzeń. W tym artykule pokazano, jak zdefiniować nowe urządzenie symulowane żarówki i przetestować go lokalnie. Akcelerator rozwiązań obejmuje symulowane urządzenia, takie jak chłodniach i ciężarówek. Można jednak zdefiniować symulowanych urządzeń do testowania rozwiązań IoT, przed przystąpieniem do wdrażania prawdziwych urządzeń.

> [!NOTE]
> W tym artykule opisano korzystanie z symulowanych urządzeń hostowanej w usłudze symulacji urządzenia. Jeśli chcesz utworzyć rzeczywistego urządzenia, zobacz [Podłączanie urządzenia do akceleratora rozwiązania monitorowania zdalnego](iot-accelerators-connecting-devices.md).

Ten poradnik pokazuje, jak dostosować mikrousług symulacji urządzenia. Ta mikrousług jest częścią akceleratora rozwiązania monitorowania zdalnego. Aby wyświetlić urządzenia symulacji możliwości, w tym przewodniku używa dwa scenariusze aplikacji IoT firmy Contoso:

W przypadku pierwszego scenariusza, należy dodać, istniejącej nowy typ danych telemetrycznych do firmy Contoso **Chłodnica** typu urządzenia.

W drugim scenariuszu firma Contoso chce przetestować nowe urządzenie inteligentne ikony żarówki. Aby uruchomić testy, należy utworzyć nowe urządzenie symulowane o następującej charakterystyce:

*Właściwości*

| Name (Nazwa)                     | Wartości                      |
| ------------------------ | --------------------------- |
| Kolor                    | White, Red, Blue            |
| Jasność               | 0 do 100                    |
| Szacowany pozostały okres | Odliczanie z 10 000 godzin |

*Dane telemetryczne*

W poniższej tabeli przedstawiono dane ikonę żarówki raportów w chmurze jako strumień danych:

| Name (Nazwa)   | Wartości      |
| ------ | ----------- |
| Stan | "włączone" "wyłączone" |
| Temperatura | Stopnie F |
| online | wartość true, false |

> [!NOTE]
> **Online** wartość jest obowiązkowa w przypadku wszystkich symulowanych typów.

*Methods*

W poniższej tabeli przedstawiono akcje, które obsługuje nowe urządzenie:

| Name (Nazwa)        |
| ----------- |
| Przełącz się   |
| Wyłącz  |

*Stan początkowy*

W poniższej tabeli przedstawiono początkowy stan urządzenia:

| Name (Nazwa)                     | Wartości |
| ------------------------ | -------|
| Kolor początkowy            | Biały  |
| Jasność początkowej       | 75     |
| Początkowe pozostały okres   | 10 000 |
| Stan początkowy telemetrii | "włączone"   |
| Temperatura początkowej danych telemetrycznych | 200   |

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, konieczne jest aktywna subskrypcja platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać instrukcje opisane w tym przewodniku, należy:

* Program Visual Studio Code. Możesz [pobierania programu Visual Studio Code dla komputerów Mac, Linux i Windows](https://code.visualstudio.com/download).
* .NET Core. Możesz pobrać [platformy .NET Core dla systemów Mac, Linux i Windows](https://www.microsoft.com/net/download).
* [Środowisko C# dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* Postman. Możesz pobrać [Postman dla komputerów Mac, Windows lub Linux](https://www.getpostman.com/apps).
* [Wdrożone do subskrypcji platformy Azure w Centrum IoT hub](../../articles/iot-hub/iot-hub-create-through-portal.md). Będą potrzebne parametry połączenia Centrum IoT, wykonanie czynności opisanych w tym przewodniku. Parametry połączenia można uzyskać w witrynie Azure portal.
* Bazy danych Cosmos DB, za pomocą interfejsu API SQL, który jest skonfigurowany dla [wysoki poziom spójności](../../articles/cosmos-db/how-to-manage-database-account.md). Będą potrzebne parametry połączenia bazy danych Cosmos DB, wykonanie czynności opisanych w tym przewodniku. Parametry połączenia można uzyskać w witrynie Azure portal.

## <a name="prepare-your-development-environment"></a>Przygotowywanie środowiska projektowego

Wykonaj następujące zadania w celu przygotowania swojego środowiska projektowego:

* Pobierz źródła dla mikrousług symulacji urządzenia.
* Pobierz źródła dla mikrousług adapter magazynu.
* Uruchom lokalnie mikrousług adapter magazynu.

Instrukcje w tym artykule przyjęto założenie, że używasz Windows. Jeśli używasz innego systemu operacyjnego może być konieczne dostosowanie niektórych ścieżek plików i poleceń w zależności od środowiska.

### <a name="download-the-microservices"></a>Pobierz mikrousług

Pobierz i Rozpakuj [zdalne monitorowanie mikrousług](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) z usługi GitHub do odpowiedniej lokalizacji na komputerze lokalnym. Tego artykułu przyjęto założenie, nazwa tego folderu jest **zdalnego monitorowania usługi dotnet wzorca**.

Pobierz i Rozpakuj [mikrousług symulacji urządzenia](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) z usługi GitHub do odpowiedniej lokalizacji na komputerze lokalnym. Tego artykułu przyjęto założenie, nazwa tego folderu jest **urządzeń symulacji dotnet-master**.

### <a name="run-the-storage-adapter-microservice"></a>Uruchamianie mikrousług adapter magazynu

Otwórz **remote-monitoring-services-dotnet-master\storage-adapter** folderu w programie Visual Studio Code. Kliknij dowolny **przywrócić** przycisków, aby poprawić nierozwiązane zależności.

Otwórz **storage-adapter/WebService/appsettings.ini** pliku i parametry połączenia usługi Cosmos DB, aby przypisać **documentDBConnectionString** zmiennej.

Aby uruchomić mikrousługi lokalnie, kliknij przycisk **Debuguj > Rozpocznij debugowanie**.

**Terminalu** okna w programie Visual Studio Code przedstawiono dane wyjściowe z uruchomionego mikrousług, łącznie z adresu URL dla sprawdzenie kondycji usługi sieci web: [ http://127.0.0.1:9022/v1/status ](http://127.0.0.1:9022/v1/status). Po przejściu do tego adresu powinna być w stanie "OK: Aktywne i dobrze".

Pozostaw mikrousług adapter magazynu, używaną w tym wystąpieniu programu Visual Studio Code wykonaj kolejne kroki.

## <a name="modify-the-chiller"></a>Modyfikowanie Chłodnica

W tej sekcji dodasz nowy **temperatura wewnętrzna** typu telemetrii do istniejących **Chłodnica** typu urządzenia:

1. Utwórz nowy folder **C:\temp\devicemodels** na komputerze lokalnym.

1. Skopiuj następujące pliki do nowego folderu z pobranych kopii mikrousług symulacji urządzenia:

    | Source | Lokalizacja docelowa |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.json | C:\temp\devicemodels\chiller-01.json |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-Method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-Method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Otwórz **C:\temp\devicemodels\chiller-01.json** pliku.

1. W **stan początkowy** Dodaj następujące dwie definicje:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. W **Telemetrii** tablicy, dodaj następującą definicję:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Zapisz **C:\temp\devicemodels\chiller-01.json** pliku.

1. Otwórz **C:\temp\devicemodels\scripts\chiller-01-state.js** pliku.

1. Dodaj następujące pola do **stanu** zmiennej:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Aktualizacja **głównego** funkcji w następujący sposób:

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global state before generating the new telemetry, so that
        // the telemetry can apply changes using the previous function state.
        restoreSimulation(previousState, previousProperties);

        // 75F +/- 5%,  Min 25F, Max 100F
        state.temperature = vary(75, 5, 25, 100);

        // 70% +/- 5%,  Min 2%, Max 99%
        state.humidity = vary(70, 5, 2, 99);

        // 65F +/- 2%,  Min 15F, Max 125F
        state.internal_temperature = vary(65, 2, 15, 125);

        log("Simulation state: " + state.simulation_state);
        if (state.simulation_state === "high_pressure") {
            // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
            state.pressure = vary(250, 25, 50, 300);
        } else {
            // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
            state.pressure = vary(150, 10, 50, 300);
        }

        updateState(state);
        return state;
    }
    ```

1. Zapisz **C:\temp\devicemodels\scripts\chiller-01-state.js** pliku.

## <a name="create-the-lightbulb"></a>Utwórz ikonę żarówki

W tej sekcji można zdefiniować nową **żarówki** typu urządzenia:

1. Utwórz plik **C:\temp\devicemodels\lightbulb-01.json** i dodaj następującą zawartość:

    ```json
    {
      "SchemaVersion": "1.0.0",
      "Id": "lightbulb-01",
      "Version": "0.0.1",
      "Name": "Lightbulb",
      "Description": "Smart lightbulb device.",
      "Protocol": "MQTT",
      "Simulation": {
        "InitialState": {
          "online": true,
          "temperature": 200.0,
          "temperature_unit": "F",
          "status": "on"
        },
        "Interval": "00:00:20",
        "Scripts": [
          {
            "Type": "javascript",
            "Path": "lightbulb-01-state.js"
          }
        ]
      },
      "Properties": {
        "Type": "Lightbulb",
        "Color": "White",
        "Brightness": 75,
        "EstimatedRemainingLife": 10000
      },
      "Tags": {
        "Location": "Building 2",
        "Floor": "2",
        "Campus": "Redmond"
      },
      "Telemetry": [
        {
          "Interval": "00:00:20",
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
          "MessageSchema": {
            "Name": "lightbulb-status;v1",
            "Format": "JSON",
            "Fields": {
              "temperature": "double",
              "temperature_unit": "text",
              "status": "text"
            }
          }
        }
      ],
      "CloudToDeviceMethods": {
        "SwitchOn": {
          "Type": "javascript",
          "Path": "SwitchOn-method.js"
        },
        "SwitchOff": {
          "Type": "javascript",
          "Path": "SwitchOff-method.js"
        }
      }
    }
    ```

    Czy zapisać zmiany **C:\temp\devicemodels\lightbulb-01.json**.

1. Utwórz plik **C:\temp\devicemodels\scripts\lightbulb-01-state.js** i dodaj następującą zawartość:

    ```javascript
    "use strict";

    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };

    // Default device properties
    var properties = {};

    /**
     * Restore the global state using data from the previous iteration.
     *
     * @param previousState device state from the previous iteration
     * @param previousProperties device properties from the previous iteration
     */
    function restoreSimulation(previousState, previousProperties) {
      // If the previous state is null, force a default state
      if (previousState) {
        state = previousState;
      } else {
        log("Using default state");
      }

      if (previousProperties) {
        properties = previousProperties;
      } else {
        log("Using default properties");
      }
    }

    /**
     * Simple formula generating a random value around the average
     * in between min and max
     *
     * @returns random value with given parameters
     */
    function vary(avg, percentage, min, max) {
      var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
      value = Math.max(value, min);
      value = Math.min(value, max);
      return value;
    }

    /**
     * Simple formula that sometimes flips the status of the lightbulb
     */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }

    /**
     * Entry point function called by the simulation engine.
     * Returns updated simulation state.
     * Device property updates must call updateProperties() to persist.
     *
     * @param context             The context contains current time, device model and id
     * @param previousState       The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState, previousProperties) {

      // Restore the global device properties and the global state before
      // generating the new telemetry, so that the telemetry can apply changes
      // using the previous function state.
      restoreSimulation(previousState, previousProperties);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      updateState(state);

      return state;
    }
    ```

    Czy zapisać zmiany **C:\temp\devicemodels\scripts\lightbulb-01-state.js**.

1. Utwórz plik **C:\temp\devicemodels\scripts\SwitchOn-method.js** i dodaj następującą zawartość:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch On method.");
      state.status = "on";
      updateState(state);
    }
    ```

    Czy zapisać zmiany **C:\temp\devicemodels\scripts\SwitchOn-method.js**.

1. Utwórz plik **C:\temp\devicemodels\scripts\SwitchOff-method.js** i dodaj następującą zawartość:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch Off method.");
      state.status = "off";
      updateState(state);
    }
    ```

    Czy zapisać zmiany **C:\temp\devicemodels\scripts\SwitchOff-method.js**.

Udało Ci się utworzyć dostosowaną wersję **Chłodnica** typu urządzenia i utworzyć nowy **żarówki** typu urządzenia.

## <a name="test-the-devices"></a>Testowanie urządzeń

W tej sekcji możesz przetestować typów urządzeń, utworzony w poprzednich sekcjach lokalnie.

### <a name="run-the-device-simulation-microservice"></a>Uruchamianie mikrousług symulacji urządzenia

Otwórz **urządzeń symulacji dotnet-master** folder został pobrany z usługi GitHub w nowym wystąpieniu programu Visual Studio Code. Kliknij dowolny **przywrócić** przycisków, aby poprawić nierozwiązane zależności.

Otwórz **WebService/appsettings.ini** pliku i parametry połączenia usługi Cosmos DB, aby przypisać **documentdb_connstring** zmiennej i również zmodyfikować następujące ustawienia:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Aby uruchomić mikrousługi lokalnie, kliknij przycisk **Debuguj > Rozpocznij debugowanie**.

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

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Symulację przy użyciu zaktualizowanych Chłodnica typ urządzenia

W tej sekcji umożliwia narzędzie Postman żądania mikrousługi symulacji urządzenia, uruchomić symulację przy użyciu zaktualizowanych Chłodnica typu urządzenia. Postman to narzędzie, które pozwala wysyłać żądania REST z usługą sieci web. Pliki konfiguracji narzędzia Postman potrzebne znajdują się w lokalnej kopii **urządzeń symulacji dotnet** repozytorium.

Aby skonfigurować narzędzie Postman:

1. Otwórz narzędzie Postman na komputerze lokalnym.

1. Kliknij przycisk **Plik > Import**. Następnie kliknij przycisk **Wybieranie plików**.

1. Przejdź do **urządzenia — symulacji dotnet-master/docs/postman** folderu. Wybierz **symulacji urządzenia IoT Azure rozwiązanie accelerator.postman_collection** i **symulacji urządzenia IoT Azure rozwiązanie accelerator.postman_environment** i kliknij przycisk **Otwórz**.

1. Rozwiń **akcelerator rozwiązań symulacji urządzenia IoT Azure** żądania, możesz wysłać.

1. Kliknij przycisk **środowiska bez** i wybierz **akcelerator rozwiązań symulacji urządzenia IoT Azure**.

Masz teraz zbierania i środowiska załadowane w Twoim obszarze roboczym narzędzia Postman, używanej do interakcji z mikrousług symulacji urządzenia.

Aby skonfigurować i uruchomić symulację:

1. W kolekcji postman Collection, wybierz **Utwórz zmodyfikowane symulacji Chłodnica** i kliknij przycisk **wysyłania**. To żądanie tworzy cztery wystąpienia typu Chłodnica symulowanego urządzenia.

1. Dane wyjściowe Monitora zdarzeń w oknie wiersza polecenia platformy Azure przedstawia dane telemetryczne z symulowanych urządzeń, w tym nowym **internal_temperature** wartości.

Aby zatrzymać symulacji, wybierz **zatrzymywać symulację** żądania narzędzia Postman, a następnie kliknij przycisk **wysyłania**.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Symulację z typem urządzenia żarówka

W tej sekcji umożliwia narzędzie Postman żądania mikrousługi symulacji urządzenia, uruchomić symulację przy użyciu typu urządzenia ikony żarówki. Postman to narzędzie, które pozwala wysyłać żądania REST z usługą sieci web.

Aby skonfigurować i uruchomić symulację:

1. W kolekcji postman Collection, wybierz **symulację żarówki** i kliknij przycisk **wysyłania**. To żądanie tworzy dwa wystąpienia typu urządzenia symulowanego ikony żarówki.

1. Dane wyjściowe Monitora zdarzeń w oknie wiersza polecenia platformy Azure przedstawia dane telemetryczne z symulowanych lightbulbs.

Aby zatrzymać symulacji, wybierz **zatrzymywać symulację** żądania narzędzia Postman, a następnie kliknij przycisk **wysyłania**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz zatrzymać dwóch mikrousługi lokalnie uruchomionej w ich wystąpieniach programu Visual Studio Code (**Debuguj > Zatrzymaj debugowanie**).

Jeśli nie potrzebujesz już wystąpień usługi IoT Hub i Cosmos DB, usuń je z subskrypcji platformy Azure, aby uniknąć wszelkich zbędnych opłat.

## <a name="next-steps"></a>Kolejne kroki

W przewodniku pokazano, jak utworzyć symulowane urządzenie niestandardowe typy i Testuj je lokalnie uruchamiając mikrousług symulacji urządzenia.

Sugerowane następnym krokiem jest sposób wdrażania typów niestandardowych symulowane urządzenie na [zdalne monitorowanie akcelerator rozwiązań](iot-accelerators-remote-monitoring-deploy-simulated-device.md).
