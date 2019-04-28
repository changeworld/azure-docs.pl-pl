---
title: Utwórz model zaawansowany symulowane urządzenie — Azure | Dokumentacja firmy Microsoft
description: W tym przewodniku z instrukcjami dowiesz się, jak utworzyć model zaawansowane urządzenia do użytku z akcelerator rozwiązań symulacji urządzenia.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 03/18/2019
ms.author: troyhop
ms.openlocfilehash: 4401d4b93a27e76554368ce72d256b38de61df4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61449063"
---
# <a name="create-an-advanced-device-model"></a>Utwórz model zaawansowanych urządzeń

W tym przewodniku opisano plików JSON i języka JavaScript, które definiują model urządzenia niestandardowe. Artykuł zawiera niektóre przykładowe pliki definicji modelu urządzenia i pokazuje, jak przekazać je do swojego wystąpienia symulacji urządzenia. Można tworzyć modele urządzeń zaawansowane, aby zasymulować bardziej realistycznego zachowania urządzenia na potrzeby testów.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, należy wdrożone wystąpienie symulacji urządzenia w ramach subskrypcji platformy Azure.

Jeśli nie wdrożono jeszcze symulacji urządzenia, należy wykonać czynności opisane w przewodniku Szybki start [Wdrażanie i uruchamianie symulacji urządzenia usługi IoT na platformie Azure](quickstart-device-simulation-deploy.md).

### <a name="open-device-simulation"></a>Otwieranie symulacji urządzenia

Aby uruchomić symulację urządzenia w przeglądarce, przejdź najpierw do [akceleratorów rozwiązań usługi Microsoft Azure IoT](https://www.azureiotsolutions.com).

Może być konieczne zalogowanie się przy użyciu poświadczeń subskrypcji platformy Azure.

Następnie kliknij przycisk **Uruchom** na kafelku symulacji urządzenia została wdrożona w [wdrażanie i uruchamianie symulacji urządzenia IoT na platformie Azure](quickstart-device-simulation-deploy.md) Szybki Start.

## <a name="device-models"></a>Modele urządzeń

Każde symulowane urządzenie należy do modelu określonego urządzenia, która definiuje zachowanie symulacji. To zachowanie obejmuje jak często wysyłają dane telemetryczne, jakiego rodzaju komunikatów do wysłania i obsługiwanych metod.

Należy zdefiniować model urządzenia przy użyciu pliku definicji JSON urządzenia i grupy plików JavaScript. Te pliki JavaScript zdefiniuj zachowanie symulacji, np. losowe dane telemetryczne i logikę metody.

Model urządzenia typowe ma:

* Jeden plik JSON dla każdego modelu urządzenia (na przykład elevator.json).
* Jeden plik skryptu JavaScript zachowanie dla każdego modelu urządzenia (na przykład state.js elevator)
* Jeden plik skryptu metody JavaScript dla każdej metody urządzenia (na przykład elevator-go-down.js)

> [!NOTE]
> Nie wszystkie modele urządzeń definiowania metod. W związku z tym modelu urządzenia może lub nie może mieć metody skryptów. Jednak wszystkie modele urządzenia musi mieć skrypt zachowanie.

## <a name="device-definition-file"></a>Plik definicji urządzenia

Każdy plik definicji urządzenia zawiera szczegóły dotyczące modelu symulowanego urządzenia, w tym następujące informacje:

* Nazwa modelu urządzenia: ciąg znaków.
* Protokół: AMQP | MQTT | HTTP.
* Początkowy stan urządzenia.
* Jak często odświeżyć stan urządzenia.
* Plik JavaScript, który służy do odświeżania stanu urządzenia.
* Lista komunikatów telemetrii do wysłania, każdy z określonej częstotliwości.
* Schemat komunikaty telemetryczne, używane przez serwer zaplecza w aplikacji do analizowania danych telemetrycznych odebrane.
* Lista obsługiwanych metod i plik JavaScript na potrzeby symulacji każdej metody.

### <a name="file-schema"></a>Schemat pliku

Wersja schematu jest zawsze "1.0.0" i zależy od formatu tego pliku:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Opis modelu urządzenia

Następujące właściwości opisują model urządzenia. Każdy typ ma unikatowy identyfikator, wersji semantycznej, nazwę i opis:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>Protokół IoT

Połączyć urządzenia IoT przy użyciu różnych protokołów. Symulacja pozwala użyć **AMQP**, **MQTT**, lub **HTTP**:

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Stan urządzenia symulowanego

Każde symulowane urządzenie ma stan wewnętrzny, który musi być zdefiniowany. Stan definiuje również właściwości, które mogą być zgłaszane w danych telemetrycznych. Na przykład Chłodnica mieć początkowy stan takich jak:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Przenoszenie urządzenia przy użyciu kilku czujników może mieć więcej właściwości, na przykład:

```json
"InitialState": {
    "latitude": 47.445301,
    "longitude": -122.296307,
    "speed": 30.0,
    "speed_unit": "mph",
    "cargotemperature": 38.0,
    "cargotemperature_unit": "F"
}
```

Stan urządzenia jest przechowywany w pamięci przez usługę symulacji, a podana jako dane wejściowe dla funkcji języka JavaScript. Funkcja języka JavaScript można określić:

* Aby ignorować stan i generować dane losowe.
* Aby zaktualizować stan urządzenia w jakiś sposób realistyczne w danym scenariuszu.

Funkcja, która generuje stan otrzymuje również jako dane wejściowe:

* Identyfikator urządzenia.
* Model urządzenia.
* Bieżąca godzina. Ta wartość umożliwia generowanie różnych danych przez urządzenia i czasu.

### <a name="generating-telemetry-messages"></a>Generowanie komunikaty telemetryczne

Usługa symulacji można wysłać kilka typów danych telemetrycznych dla każdego urządzenia. Zazwyczaj telemetria zawiera dane ze stanu urządzenia. Na przykład symulowane miejsca może wysyłać informacje o temperatury i wilgotności co 10 sekund. Należy zwrócić uwagę symbole zastępcze w poniższym fragmencie kodu, które automatycznie są zastępowane wartościami ze stanu urządzenia:

```json
"Telemetry": [
    {
        "Interval": "00:00:10",
        "MessageTemplate":
            "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
        "MessageSchema": {
            "Name": "RoomComfort;v1",
            "Format": "JSON",
            "Fields": {
                "temperature": "double",
                "temperature_unit": "text",
                "humidity": "integer"
            }
        }
    }
],
```

Symbole zastępcze należy użyć specjalnej składni **${NAME}** gdzie **nazwa** klucza z obiektu stanu urządzenia, które są zwracane przez kod JavaScript jest **głównego** funkcji. Ciągi powinny być podawane podczas numery nie należy.

#### <a name="message-schema"></a>Schemat wiadomości

Każdy typ komunikatu musi być dobrze zdefiniowany schemat. Schemat wiadomości jest również opublikowany do usługi IoT Hub, aby ponownie użyć informacji do interpretacji przychodzących danych telemetrycznych aplikacji zaplecza.

Schemat obsługuje format JSON, który umożliwia łatwe analizowanie, transformacji i analizy, w wielu systemach i z usługi.

Pola wymienione w schemacie można się z następujących typów:

* Obiekt - zserializowanym przy użyciu formatu JSON
* Plik binarny - serializacji przy użyciu base64
* Text
* Boolean
* Liczba całkowita
* Double
* DateTime

### <a name="supported-methods"></a>Obsługiwane metody

Symulowane urządzenia może reagować na wywołania metody, w tym przypadku wykonać logikę i podaj kilka odpowiedzi. Podobnie jak symulacji logikę metody są przechowywane w pliku JavaScript i mogą wchodzić w interakcje ze stanem urządzenia. Na przykład:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Utwórz plik definicji urządzenia

W tym jak-to-przewodniku zobaczysz, jak utworzyć model urządzenia dla nagrodę: drona. Drona będzie losowo podnoszenia wokół początkowy zestaw współrzędnych zmiana lokalizacji i wysokość.

Skopiuj następujące dane JSON do edytora tekstu i zapisz go jako **drone.json**.

### <a name="device-definition-json-example"></a>Przykład kodu JSON definicji urządzenia

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "drone",
  "Version": "0.0.1",
  "Name": "Drone",
  "Description": "Simple drone.",
  "Protocol": "AMQP",
  "Simulation": {
    "InitialState": {
      "velocity": 0.0,
      "velocity_unit": "mm/sec",
      "acceleration": 0.0,
      "acceleration_unit": "mm/sec^2",
      "latitude": 47.476075,
      "longitude": -122.192026,
      "altitude": 0.0
    },
    "Interval": "00:00:05",
    "Scripts": [{
      "Type": "JavaScript",
      "Path": "drone-state.js"
    }]
  },
  "Properties": {
    "Type": "Drone",
    "Firmware": "1.0",
    "Model": "P-96"
  },
  "Tags": {
    "Owner": "Contoso"
  },
  "Telemetry": [{
      "Interval": "00:00:05",
      "MessageTemplate": "{\"velocity\":\"${velocity}\",\"acceleration\":\"${acceleration}\",\"position\":\"${latitude}|${longitude}|${altitude}\"}",
      "MessageSchema": {
        "Name": "drone-event-sensor;v1",
        "Format": "JSON",
        "Fields": {
          "velocity": "double",
          "velocity_unit": "text",
          "acceleration": "double",
          "acceleration_unit": "text",
          "latitude": "double",
          "longitude": "double",
          "altitude": "double"
        }
      }
    }
  ],
    "CloudToDeviceMethods": {
        "RecallDrone": {
            "Type": "JavaScript",
            "Path": "droneRecall-method.js"
        }
    }
}
```

## <a name="behavior-script-files"></a>Pliki skryptów zachowanie

Kod w pliku skryptu zachowanie przenosi drona. Skrypt zmienia podniesienia uprawnień i położenie drona, manipulowanie stanu pamięci urządzenia.

Pliki JavaScript muszą mieć **głównego** funkcja, która przyjmuje dwa parametry:

* A **kontekstu** obiekt, który zawiera trzy właściwości:
    * **bieżącagodzina** jako ciąg w formacie **RRRR MM-dd'T'HH:mm:sszzz**.
    * **deviceId**. Na przykład **Simulated.Elevator.123**.
    * **deviceModel**. Na przykład **Elevator**.
* A **stanu** obiekt, który jest wartością zwróconą przez funkcję poprzedniego wywołania. Ten stan urządzenia jest obsługiwana przez usługę symulacji i używany do generowania komunikaty telemetryczne.

**Głównego** funkcja zwraca nowy stan urządzenia. Na przykład:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Tworzenie pliku skryptu zachowanie

Skopiuj następujący kod JavaScript w edytorze tekstu i zapisz go jako **state.js drona**.

### <a name="device-model-javascript-simulation-example"></a>Przykład symulacji JavaScript modelu urządzenia

```JavaScript
"use strict";

// Position control
const DefaultLatitude = 47.476075;
const DefaultLongitude = -122.192026;
const DistanceVariation = 10;

// Altitude control
const DefaultAltitude = 0.0;
const AverageAltitude = 499.99;
const AltitudeVariation = 5;

// Velocity control
const AverageVelocity = 60.00;
const MinVelocity = 20.00;
const MaxVelocity = 120.00;
const VelocityVariation = 5;

// Acceleration control
const AverageAcceleration = 2.50;
const MinAcceleration = 0.01;
const MaxAcceleration = 9.99;
const AccelerationVariation = 1;

// Display control for position and other attributes
const GeoSpatialPrecision = 6;
const DecimalPrecision = 2;

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: DefaultLatitude,
    longitude: DefaultLongitude,
    altitude: DefaultAltitude
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
 */
function vary(avg, percentage, min, max) {
    var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
    value = Math.max(value, min);
    value = Math.min(value, max);
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
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global state before generating the new telemetry, so that
    // the telemetry can apply changes using the previous function state.
    restoreSimulation(previousState, previousProperties);

    state.acceleration = vary(AverageAcceleration, AccelerationVariation, MinAcceleration, MaxAcceleration).toFixed(DecimalPrecision);
    state.velocity = vary(AverageVelocity, VelocityVariation, MinVelocity, MaxVelocity).toFixed(DecimalPrecision);

    // Use the last coordinates to calculate the next set with a given variation
    var coords = varylocation(Number(state.latitude), Number(state.longitude), DistanceVariation);
    state.latitude = Number(coords.latitude).toFixed(GeoSpatialPrecision);
    state.longitude = Number(coords.longitude).toFixed(GeoSpatialPrecision);

    // Fluctuate altitude between given variation constant by more or less
    state.altitude = vary(AverageAltitude, AltitudeVariation, AverageAltitude - AltitudeVariation, AverageAltitude + AltitudeVariation).toFixed(DecimalPrecision);

    return state;
}

/**
 * Generate a random geolocation at some distance (in miles)
 * from a given location
 */
function varylocation(latitude, longitude, distance) {
    // Convert to meters, use Earth radius, convert to radians
    var radians = (distance * 1609.344 / 6378137) * (180 / Math.PI);
    return {
        latitude: latitude + radians,
        longitude: longitude + radians / Math.cos(latitude * Math.PI / 180)
    };
}
```

## <a name="create-a-method-script-file"></a>Tworzenie pliku skryptu — metoda

Metoda skrypty są podobne do zachowania skryptów. Po wywołaniu określonej chmurze metody urządzenia mogą określać zachowanie urządzenia.

Skrypt wycofaniu drona ustawia współrzędne drona stały punktu do symulacji drona, zwracając home.

Skopiuj następujący kod JavaScript w edytorze tekstu i zapisz go jako **droneRecall method.js**.

### <a name="device-model-javascript-simulation-example"></a>Przykład symulacji JavaScript modelu urządzenia

```JavaScript
"use strict";

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: 0.0,
    longitude: 0.0,
    altitude: 0.0
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
 * Entry point function called by the simulation engine.
 *
 * @param context        The context contains current time, device model and id, not used
 * @param previousState  The device state since the last iteration, not used
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global device properties and the global state before
    // generating the new telemetry, so that the telemetry can apply changes
    // using the previous function state.
    restoreSimulation(previousState, previousProperties);

    //simulate the behavior of a drone when recalled
  state.latitude = 47.476075;
  state.longitude = -122.192026;
  return state;
}
```

## <a name="debugging-script-files"></a>Pliki debugowania skryptu

Chociaż nie można dołączyć debugera do uruchomionego pliku zachowanie, istnieje możliwość zapisywania informacji przy użyciu dzienników usługi **dziennika** funkcji. Błędy składniowe interpreter nie powiedzie się i zapisuje informacje o wyjątku do dziennika.

Przykład rejestrowania:

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return updateState;
}
```

## <a name="deploy-an-advanced-device-model"></a>Wdrażanie modelu urządzenia zaawansowane

Do wdrożenia modelu urządzenia zaawansowane, przekazać pliki wystąpienia symulacji urządzenia:

Na pasku narzędzi wybierz pozycję **Modele urządzeń**. **Modele urządzeń** strona zawiera listę dostępnych w tym wystąpieniu symulacji urządzenia modeli urządzeń:

![Modele urządzeń](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Kliknij pozycję **+ Dodaj modele urządzeń** w prawym górnym rogu strony:

![Dodawanie modelu urządzenia](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Kliknij przycisk **zaawansowane** Otwórz kartę modelu zaawansowanych urządzeń:

![Advanced Tab](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Kliknij przycisk **Przeglądaj** i wybierz pliki JSON i języka JavaScript, został utworzony. Pamiętaj wybrać wszystkie trzy pliki. Jeśli brakuje dowolnej jeden plik sprawdzania poprawności zakończy się niepowodzeniem:

![Przeglądaj pliki](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Jeśli weryfikacja zakończy się pomyślnie plików, kliknij przycisk **Zapisz** i modelu urządzenia jest gotowy do użycia w symulacji. W przeciwnym razie należy naprawić wszystkie błędy i ponownego przekazywania plików:

![Zapisz](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku opisano pliki modelu urządzenia, które są używane w symulacji urządzenia oraz sposobu tworzenia modelu urządzenia zaawansowane. Następnie możesz eksplorować jak [Użyj Time Series Insights, aby zwizualizować dane telemetryczne wysyłane z akceleratora rozwiązań symulacji urządzenia](https://docs.microsoft.com/azure/iot-accelerators/iot-accelerators-device-simulation-time-series-insights).
