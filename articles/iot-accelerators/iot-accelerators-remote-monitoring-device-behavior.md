---
title: Symulowane urządzenie w rozwiązaniu do zdalnego monitorowania — Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób używania języka JavaScript do definiowania zachowania symulowanego urządzenia w rozwiązaniu do zdalnego monitorowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c39ca0a018bd22844cf7e5350e6d3586319aac16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890858"
---
# <a name="implement-the-device-model-behavior"></a>Implementowanie zachowania modelu urządzenia

W artykule [Zrozumieć schemat modelu urządzenia](iot-accelerators-remote-monitoring-device-schema.md) opisano schemat, który definiuje symulowany model urządzenia. Ten artykuł odnosił się do dwóch typów plików JavaScript, które implementują zachowanie symulowanego urządzenia:

- **Państwo** Pliki JavaScript, które są uruchamiane w ustalonych odstępach czasu w celu zaktualizowania stanu wewnętrznego urządzenia.
- **Metoda** Pliki JavaScript, które są uruchamiane, gdy rozwiązanie wywołuje metodę na urządzeniu.

> [!NOTE]
> Zachowania modelu urządzenia są tylko dla symulowanych urządzeń hostowanych w usłudze symulacji urządzeń. Jeśli chcesz utworzyć prawdziwe urządzenie, zobacz [Łączenie urządzenia z akceleratorem rozwiązań do zdalnego monitorowania](iot-accelerators-connecting-devices.md).

W tym artykule omówiono sposób wykonywania następujących zadań:

>[!div class="checklist"]
> * Sterowanie stanem symulowanego urządzenia
> * Definiowanie reakcji symulowanego urządzenia na wywołanie metody z rozwiązania Do zdalnego monitorowania
> * Debugowanie skryptów

## <a name="state-behavior"></a>Zachowanie stanu

Sekcja [Symulacja](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) schematu modelu urządzenia definiuje stan wewnętrzny symulowanego urządzenia:

- `InitialState`definiuje wartości początkowe dla wszystkich właściwości obiektu stanu urządzenia.
- `Script`identyfikuje plik JavaScript, który działa zgodnie z harmonogramem, aby zaktualizować stan urządzenia.

W poniższym przykładzie przedstawiono definicję obiektu stanu urządzenia dla symulowanego urządzenia agregatu chłodniczego:

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

Stan symulowanego urządzenia, zgodnie z `InitialState` definicją w sekcji, jest przechowywany w pamięci przez usługę symulacji. Informacje o stanie są przekazywane `main` jako dane wejściowe do funkcji zdefiniowanej w **pliku chłodniczej-01-state.js**. W tym przykładzie usługa symulacji uruchamia plik **chiller-01-state.js** co pięć sekund. Skrypt może zmodyfikować stan symulowanego urządzenia.

Poniżej przedstawiono konspekt typowej `main` funkcji:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

Parametr `context` ma następujące właściwości:

- `currentTime`jako ciąg z formatem`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, na przykład`Simulated.Chiller.123`
- `deviceModel`, na przykład`Chiller`

Parametr `state` zawiera stan urządzenia utrzymywany przez usługę symulacji urządzenia. Ta wartość `state` jest obiektem zwróconym `main`przez poprzednie wywołanie .

W poniższym przykładzie przedstawiono typową implementację `main` metody obsługi stanu urządzenia obsługiwanego przez usługę symulacji:

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

W poniższym przykładzie pokazano, `main` jak metoda może symulować wartości telemetrii, które zmieniają się w czasie:

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

Możesz wyświetlić kompletny [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) na GitHub.

## <a name="method-behavior"></a>Zachowanie metody

[CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) sekcji schematu modelu urządzenia definiuje metody symulowane urządzenie odpowiada.

Poniższy przykład przedstawia listę metod obsługiwanych przez symulowane urządzenie agregat chłodnicza:

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

Stan symulowanego urządzenia, zgodnie z `InitialState` definicją w sekcji schematu, jest przechowywany w pamięci przez usługę symulacji. Informacje o stanie są przekazywane `main` jako dane wejściowe do funkcji zdefiniowanej w pliku JavaScript, gdy metoda jest wywoływana. Skrypt może zmodyfikować stan symulowanego urządzenia.

Poniżej przedstawiono konspekt typowej `main` funkcji:

```javascript
function main(context, previousState, previousProperties) {

}
```

Parametr `context` ma następujące właściwości:

- `currentTime`jako ciąg z formatem`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, na przykład`Simulated.Chiller.123`
- `deviceModel`, na przykład`Chiller`

Parametr `state` zawiera stan urządzenia utrzymywany przez usługę symulacji urządzenia.

Parametr `properties` zawiera właściwości urządzenia, które są zapisywane jako zgłaszane właściwości do bliźniaczej reprezentacji urządzenia usługi IoT Hub.

Istnieją trzy funkcje globalne, których można użyć, aby ułatwić implementowanie zachowania metody:

- `updateState`, aby zaktualizować stan posiadany przez usługę symulacji.
- `updateProperty`, aby zaktualizować właściwość pojedynczego urządzenia.
- `sleep`, aby wstrzymać wykonywanie, aby symulować długotrwałe zadanie.

W poniższym przykładzie przedstawiono skróconą wersję skryptu **IncreasePressure-method.js** używanego przez symulowane urządzenia agregujące agregaty chłodnicza:

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

## <a name="debugging-script-files"></a>Debugowanie plików skryptów

Nie można dołączyć debugera do interpretera Javascript używanego przez usługę symulacji urządzenia do uruchamiania skryptów stanu i metody. Można jednak rejestrować informacje w dzienniku usługi. `log()` Wbudowana funkcja umożliwia zapisywanie informacji w celu śledzenia i debugowania wykonania funkcji.

Jeśli występuje błąd składni, interpreter kończy się `Jint.Runtime.JavaScriptException` niepowodzeniem i zapisuje wpis w dzienniku usługi.

Uruchom [usługę lokalnie](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) artykuł na GitHub pokazuje, jak uruchomić usługę symulacji urządzenia lokalnie. Uruchamianie usługi lokalnie ułatwia debugowanie symulowanych urządzeń przed wdrożeniem ich w chmurze.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób definiowania zachowania własnego niestandardowego symulowanego modelu urządzenia. W tym artykule pokazano, jak:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Sterowanie stanem symulowanego urządzenia
> * Definiowanie reakcji symulowanego urządzenia na wywołanie metody z rozwiązania Do zdalnego monitorowania
> * Debugowanie skryptów

Teraz, gdy już wiesz, jak określić zachowanie symulowanego urządzenia, sugerowanym następnym krokiem jest nauczenie się [tworzenia symulowanego urządzenia](iot-accelerators-remote-monitoring-create-simulated-device.md).

Aby uzyskać więcej informacji dewelopera na temat rozwiązania do zdalnego monitorowania, zobacz:

* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Przewodnik po rozwiązywaniu problemów dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
