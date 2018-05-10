---
title: Symulowane urządzenie zachowanie w zdalnym rozwiązanie monitorowania - Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób użycia JavaScript do definiowania zachowania symulowane urządzenie zdalne rozwiązanie monitorowania.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/29/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 2a2cbe5379adbd2c4ad6534b621871ecc30bfc81
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="implement-the-device-model-behavior"></a>Implementuje zachowanie model urządzenia

Artykuł [zrozumieć schematu modelu urządzenia](iot-suite-remote-monitoring-device-schema.md) opisane schemat, który definiuje model symulowane urządzenie. Ten artykuł określonych dwa typy plik JavaScript, które implementują zachowanie symulowane urządzenie.

- **Stan** JavaScript pliki uruchomionymi w ustalonych odstępach czasu, aby zaktualizować stan wewnętrzny urządzenia.
- **Metoda** pliki JavaScript, który uruchamiany, gdy rozwiązanie wywołuje metodę na urządzeniu.

W tym artykule omówiono sposób wykonywania następujących zadań:

>[!div class="checklist"]
> * Formant stanu symulowane urządzenie
> * Zdefiniuj, jak wywołać reponds symulowane urządzenie, do metody z zdalnego rozwiązanie monitorowania
> * Debugowanie skryptów

## <a name="state-behavior"></a>Zachowanie stanu

[Symulacji](iot-suite-remote-monitoring-device-schema.md#simulation) sekcji schematu modelu urządzenia definiuje wewnętrzny stan klasy symulowane urządzenie:

- `InitialState` Definiuje wartości początkowe dla wszystkich właściwości obiektu stanu urządzenia.
- `Script` Określa plik JavaScript, że działa zgodnie z harmonogramem, aby zaktualizować stan urządzenia.

W poniższym przykładzie przedstawiono definicję obiektu stanu urządzenia dla urządzeń symulowane Chłodnica:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

Stan symulowanego urządzenia, zgodnie z definicją w `InitialState` sekcji, jest przechowywany w pamięci przez usługę symulacji. Informacje o stanie jest przekazywany jako dane wejściowe `main` funkcji zdefiniowanej w **Chłodnica-01-state.js**. W tym przykładzie jest uruchamiana usługa symulacji **Chłodnica-01-state.js** pliku co pięć sekund. Skrypt można modyfikować stanu symulowane urządzenie.

Poniżej pokazano konturu typowe `main` funkcji:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

`context` Parametr ma następujące właściwości:

- `currentTime` jako ciąg w formacie `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, na przykład `Simulated.Chiller.123`
- `deviceModel`, na przykład `Chiller`

`state` Parametr zawiera stan urządzenia obsługiwane przez usługę symulacji urządzenia. Ta wartość jest `state` obiektu zwróconego przez poprzednie wywołanie `main`.

W poniższym przykładzie przedstawiono Typowa implementacja `main` metody na potrzeby obsługi stanu urządzenia obsługiwane przez usługę symulacji:

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState, previousProperties) {

  restoreState(previousState);

  // Update state

  return state;
}
```

W poniższym przykładzie przedstawiono sposób `main` metody można symulować wartości dane telemetryczne, które różnią się w czasie:

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState, previousProperties) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

Możesz wyświetlić pełną [Chłodnica-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) w witrynie Github.

## <a name="method-behavior"></a>Zachowanie — metoda

[CloudToDeviceMethods](iot-suite-remote-monitoring-device-schema.md#cloudtodevicemethods) sekcji schematu modelu urządzenia definiuje metody odpowiada symulowane urządzenie.

W poniższym przykładzie przedstawiono listę metod obsługiwanych przez Chłodnica symulowane urządzenie:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

Każda metoda ma skojarzony plik JavaScript, który implementuje zachowanie metody.

Stan symulowanego urządzenia, zgodnie z definicją w `InitialState` sekcji schematu, jest przechowywany w pamięci przez usługę symulacji. Informacje o stanie jest przekazywany jako dane wejściowe `main` funkcji zdefiniowanej w pliku JavaScript podczas wywoływania metody. Skrypt można modyfikować stanu symulowane urządzenie.

Poniżej pokazano konturu typowe `main` funkcji:

```javascript
function main(context, previousState, previousProperties) {

}
```

`context` Parametr ma następujące właściwości:

- `currentTime` jako ciąg w formacie `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, na przykład `Simulated.Chiller.123`
- `deviceModel`, na przykład `Chiller`

`state` Parametr zawiera stan urządzenia obsługiwane przez usługę symulacji urządzenia.

`properties` Parametr zawiera właściwości urządzenia, które są zapisane jako właściwości zgłoszony do dwie urządzenia IoT Hub.

Istnieją trzy funkcje globalne używanego pomagających w realizacji zachowanie metody:

- `updateState` Aby zaktualizować stan posiadanych przez usługę symulacji.
- `updateProperty` Aby zaktualizować pojedynczej właściwości urządzenia.
- `sleep` Aby zatrzymać wykonywanie do symulowania długotrwałe zadanie.

W poniższym przykładzie przedstawiono skróconej wersji **IncreasePressure method.js** skryptu używany przez urządzenia Chłodnica symulowanego:

```javascript
function main(context, previousState, previousProperties) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>Debugowanie skryptów plików

Nie jest możliwe dołączanie debugera do interpreter języka Javascript używane przez usługę symulacji urządzenia do uruchamiania skryptów stanu i metody. Jednak może rejestrować informacje w dzienniku usługi. Wbudowane `log()` funkcja umożliwia zapisanie informacji do śledzenia i debugowania wykonywania funkcji.

Jeśli występuje błąd składni interpretera zakończy się niepowodzeniem, i zapisuje `Jint.Runtime.JavaScriptException` wpisu w dzienniku usługi.

[Utworzyć symulowane urządzenie](iot-suite-remote-monitoring-test.md) artykule przedstawiono sposób uruchomić usługę symulacji urządzenia lokalnie. Uruchomiona usługa lokalnie ułatwia debugowanie symulowanego urządzenia przed ich wdrożeniem w chmurze.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano sposób definiowania zachowania modelu niestandardowych symulowane urządzenie. W tym artykule pokazano należy jak do:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Formant stanu symulowane urządzenie
> * Zdefiniuj, jak wywołać reponds symulowane urządzenie, do metody z zdalnego rozwiązanie monitorowania
> * Debugowanie skryptów

Po zapoznaniu do określania zachowania symulowane urządzenie, sugerowane następnym krokiem jest Dowiedz się, jak [utworzyć symulowane urządzenie](iot-suite-remote-monitoring-test.md).

Aby uzyskać więcej informacji o deweloperów o zdalnego rozwiązanie monitorowania zobacz:

* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Przewodnik po rozwiązywaniu problemów dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->