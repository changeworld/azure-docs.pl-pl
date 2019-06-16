---
title: Symulowane zachowanie urządzenia w rozwiązaniu do monitorowania zdalnego — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak zdefiniować zachowanie symulowanego urządzenia w rozwiązaniu do zdalnego monitorowania przy użyciu języka JavaScript.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: 04d2ad2f0e86ee977600af86a2ffd1e9d7680375
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65823417"
---
# <a name="implement-the-device-model-behavior"></a>Implementowanie zachowania modelu urządzenia

Artykuł [zrozumieć schematu modelu urządzenia](iot-accelerators-remote-monitoring-device-schema.md) opisano schemat, który definiuje model urządzenia symulowanego. Artykuł z określonych dwa rodzaje plików JavaScript implementujące zachowanie symulowanego urządzenia.

- **Stan** pliki JavaScript, uruchamianą w ustalonych odstępach czasu, aby zaktualizować stan wewnętrzny urządzenia.
- **Metoda** pliki JavaScript, który uruchamiany, gdy rozwiązanie wywołuje metodę na urządzeniu.

> [!NOTE]
> Zachowania modelu urządzenia są przeznaczone tylko dla symulowanych urządzeń hostowanej w usłudze symulacji urządzenia. Jeśli chcesz utworzyć rzeczywistego urządzenia, zobacz [Podłączanie urządzenia do akceleratora rozwiązania monitorowania zdalnego](iot-accelerators-connecting-devices.md).

W tym artykule omówiono sposób wykonywania następujących zadań:

>[!div class="checklist"]
> * Kontrola stanu symulowanego urządzenia
> * Zdefiniuj, jak symulowane urządzenie odpowiada na wywołanie metody rozwiązania do zdalnego monitorowania
> * Debugowanie skryptów

## <a name="state-behavior"></a>Zachowanie stanu

[Symulacji](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) sekcja schematu modelu urządzenia definiuje stan wewnętrzny symulowane urządzenia:

- `InitialState` definiuje początkowe wartości dla właściwości obiektu stanu urządzenia.
- `Script` Określa plik języka JavaScript uruchamiana zgodnie z harmonogramem, aby zaktualizować stan urządzenia.

Definicja obiektu stanu urządzenia dla urządzenia symulowanego Chłodnica można znaleźć w poniższym przykładzie:

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

Stan Symulowane urządzenie, zgodnie z definicją w `InitialState` sekcji, są przechowywane w pamięci przez usługę symulacji. Informacje o stanie jest przekazywany jako dane wejściowe `main` funkcję zdefiniowaną w **Chłodnica-01-state.js**. W tym przykładzie jest uruchamiana usługa symulacji **Chłodnica-01-state.js** pliku co pięć sekund. Skrypt można modyfikować stanu symulowanego urządzenia.

Poniżej pokazano zarys typowej `main` funkcji:

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

`state` Parametr zawiera stan urządzenia obsługiwane przez usługę symulacji urządzenia. Ta wartość jest `state` obiekt zwrócony przez poprzednie wywołanie `main`.

W poniższym przykładzie pokazano Typowa implementacja metody `main` metody, aby obsłużyć stan urządzenia obsługiwane przez usługę symulacji:

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

W poniższym przykładzie pokazano sposób, w jaki `main` metoda można symulować wartości telemetryczne, które różnią się wraz z upływem czasu:

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

Możesz wyświetlić pełne [Chłodnica-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) w witrynie GitHub.

## <a name="method-behavior"></a>Zachowanie metody

[CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) sekcja schematu modelu urządzenia definiuje metody symulowane urządzenie odpowiada.

Lista metod obsługiwanych przez urządzenie symulowane Chłodnica można znaleźć w poniższym przykładzie:

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

Każda metoda charakteryzuje się skojarzony plik JavaScript, który implementuje zachowanie metody.

Stan Symulowane urządzenie, zgodnie z definicją w `InitialState` sekcji schematów, są przechowywane w pamięci przez usługę symulacji. Informacje o stanie jest przekazywany jako dane wejściowe `main` funkcję zdefiniowaną w pliku JavaScript, gdy wywoływana jest metoda. Skrypt można modyfikować stanu symulowanego urządzenia.

Poniżej pokazano zarys typowej `main` funkcji:

```javascript
function main(context, previousState, previousProperties) {

}
```

`context` Parametr ma następujące właściwości:

- `currentTime` jako ciąg w formacie `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, na przykład `Simulated.Chiller.123`
- `deviceModel`, na przykład `Chiller`

`state` Parametr zawiera stan urządzenia obsługiwane przez usługę symulacji urządzenia.

`properties` Parametr zawiera właściwości urządzenia, które są zapisywane w postaci zgłaszanych właściwości do bliźniaczej reprezentacji urządzenia usługi IoT Hub.

Istnieją trzy funkcje globalne, używane w celu zaimplementować to zachowanie metody:

- `updateState` Aby zaktualizować stan przechowywanych przez usługę symulacji.
- `updateProperty` Aby zaktualizować pojedynczej właściwości urządzenia.
- `sleep` Aby zatrzymać wykonywanie, aby zasymulować długotrwałe zadanie.

Poniższy przykład pokazuje, skróconą wersję **IncreasePressure method.js** używany przez urządzenia symulowanego Chłodnica skryptu:

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

## <a name="debugging-script-files"></a>Pliki debugowania skryptu

Nie jest możliwe dołączanie debugera do interpretera języka Javascript używane przez usługę symulacji urządzenia do uruchamiania skryptów stanu i metody. Jednak może rejestrować informacje w dzienniku usługi. Wbudowane `log()` funkcja umożliwia zapisanie informacji śledzenie i debugowanie wykonywania funkcji.

Jeśli występuje błąd składni interpreter zakończy się niepowodzeniem, i zapisuje `Jint.Runtime.JavaScriptException` wpis dziennika usługi.

[Lokalnie uruchomiona usługa](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) artykuł w witrynie GitHub dowiesz się, jak uruchomić usługi symulacji urządzeń lokalnie. Lokalnie uruchomiona usługa ułatwia debugowanie symulowane urządzenia, przed ich wdrożeniem w chmurze.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano sposób definiowania zachowania modelu niestandardowego symulowanego urządzenia. Ten artykuł pokazuje, jak do:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Kontrola stanu symulowanego urządzenia
> * Zdefiniuj, jak symulowane urządzenie odpowiada na wywołanie metody rozwiązania do zdalnego monitorowania
> * Debugowanie skryptów

Teraz, gdy wyjaśniono sposób określić zachowanie symulowane urządzenie, sugerowane następnym krokiem jest Dowiedz się, jak [utworzyć symulowane urządzenie](iot-accelerators-remote-monitoring-create-simulated-device.md).

Aby uzyskać więcej informacji dla deweloperów o rozwiązaniu monitorowania zdalnego Zobacz:

* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Przewodnik po rozwiązywaniu problemów dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
