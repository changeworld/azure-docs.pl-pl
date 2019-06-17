---
title: Schemat urządzenia w rozwiązaniu do monitorowania zdalnego — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano schematu JSON, który definiuje symulowanego urządzenia w rozwiązaniu do zdalnego monitorowania.
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 0f9669d491648ecc621aab27d0908dcc3dc84438
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65823327"
---
# <a name="understand-the-device-model-schema"></a>Zrozumienie schematu modelu urządzenia

Symulowane urządzenia w rozwiązaniu do zdalnego monitorowania służy do testowania jego zachowanie. Rozwiązania do zdalnego monitorowania zawiera usługi symulacji urządzeń, aby uruchomić symulowane urządzenia. Podczas wdrażania rozwiązania do zdalnego monitorowania zbiór symulowanych urządzeń jest automatycznie inicjowana obsługa. Można dostosować istniejący Symulowane urządzenia, lub Utwórz swoje własne.

W tym artykule opisano schematu modelu urządzenia, która określa zachowanie symulowanego urządzenia. Model urządzenia jest przechowywany w pliku JSON.

> [!NOTE]
> Tego schematu modelu urządzenia jest tylko w przypadku symulowanych urządzeń hostowanej w usłudze symulacji urządzenia. Jeśli chcesz utworzyć rzeczywistego urządzenia, zobacz [Podłączanie urządzenia do akceleratora rozwiązania monitorowania zdalnego](iot-accelerators-connecting-devices.md).

Następujące artykuły odnoszą się do bieżącego artykułu:

* [Implementowanie zachowania modelu urządzenia](iot-accelerators-remote-monitoring-device-behavior.md) opisano pliki języka JavaScript umożliwia Implementowanie zachowania urządzenia symulowanego.
* [Utwórz nowe urządzenie symulowane](iot-accelerators-remote-monitoring-create-simulated-device.md) umieszcza go wszystko ze sobą i dowiesz się, jak wdrożyć nowy typ symulowanego urządzenia do rozwiązania.

W tym artykule omówiono sposób wykonywania następujących zadań:

>[!div class="checklist"]
> * Zdefiniuj model symulowanego urządzenia przy użyciu pliku JSON
> * Określ właściwości symulowane urządzenie
> * Określ, które symulowane urządzenia wysyłają dane telemetryczne
> * Określają metody chmury do urządzenia, które odpowiada urządzenie

## <a name="the-parts-of-the-device-model-schema"></a>Części schematu modelu urządzenia

Każdy model urządzenia, takie jak Chłodnica lub truck, definiuje typ urządzenia, które można symulować usługi symulacji. Każdy model urządzenia jest przechowywany w pliku JSON z poniższym schematem najwyższego poziomu:

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "elevator-01",
  "Version": "0.0.1",
  "Name": "Elevator",
  "Description": "Elevator with floor, vibration and temperature sensors.",
  "Protocol": "AMQP",
  "Simulation": {
    // Specify the simulation behavior
  },
  "Properties": {
    // Define properties
  },
  "Telemetry": [
    // Specify telemetry
  ],
  "CloudToDeviceMethods": {
    // Specify methods
  }
}
```

Możesz wyświetlić pliki schematów do domyślnego symulowane urządzenia w [folderu devicemodels](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) w witrynie GitHub.

W poniższej tabeli opisano wpisy schematu najwyższego poziomu:

| Schemat wejścia | Opis |
| -- | --- |
| `SchemaVersion` | Wersja schematu jest zawsze `1.0.0` i zależy od formatu tego pliku. |
| `Id` | Unikatowy identyfikator dla tego modelu urządzenia. |
| `Version` | Identyfikuje wersję modelu urządzenia. |
| `Name` | Przyjazna nazwa modelu urządzenia. |
| `Description` | Opis modelu urządzenia. |
| `Protocol` | Protokół połączenia urządzenia używa. Może być jednym z `AMQP`, `MQTT`, i `HTTP`. |

W poniższych sekcjach opisano inne sekcje w schemacie JSON:

## <a name="simulation"></a>Symulacja

W `Simulation` sekcji, zdefiniuj stan wewnętrzny symulowanego urządzenia. Wszystkie wartości telemetryczne wysyłane przez urządzenie musi być częścią tego stanu urządzenia.

Definicja stan urządzenia ma dwa elementy:

* `InitialState` definiuje początkowe wartości dla właściwości obiektu stanu urządzenia.
* `Script` Określa plik języka JavaScript uruchamiana zgodnie z harmonogramem, aby zaktualizować stan urządzenia. Można użyć tego pliku skryptu, aby losowo ustawiać wartości telemetryczne wysyłane przez urządzenie.

Aby dowiedzieć się więcej na temat pliku JavaScript, która aktualizuje obiekt stanu urządzenia, zobacz [zrozumieć zachowania modelu urządzenia](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

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
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

Uruchamia usługę symulacji **Chłodnica-01-state.js** pliku co pięć sekund, aby zaktualizować stan urządzenia. Widać plików JavaScript dla urządzeń domyślne symulowane w [folder skryptów](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) w witrynie GitHub. Zgodnie z Konwencją, te pliki JavaScript mają sufiks **— stan** aby odróżnić je od plików, które implementują metody zachowania.

## <a name="properties"></a>Właściwości

`Properties` Sekcja schemat definiuje wartości właściwości urządzenie raportuje do rozwiązania. Na przykład:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

Po uruchomieniu rozwiązania, wysyła zapytanie symulowanych urządzeń w celu utworzenia listy `Type` wartości mają być używane w interfejsie użytkownika. Rozwiązanie używa `Latitude` i `Longitude` właściwości, aby dodać lokalizację urządzenia do mapy na pulpicie nawigacyjnym.

## <a name="telemetry"></a>Telemetria

`Telemetry` Tablicy zawiera listę wszystkich typów danych telemetrycznych symulowane urządzenie wysyła do rozwiązania.

Poniższy przykład wysyła komunikat telemetrii JSON co 10 sekund przy użyciu `floor`, `vibration`, i `temperature` danych z czujników elevator:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate": "{\"floor\":${floor},\"vibration\":${vibration},\"vibration_unit\":\"${vibration_unit}\",\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
    "MessageSchema": {
      "Name": "elevator-sensors;v1",
      "Format": "JSON",
      "Fields": {
        "floor": "integer",
        "vibration": "double",
        "vibration_unit": "text",
        "temperature": "double",
        "temperature_unit": "text"
      }
    }
  }
]
```

`MessageTemplate` definiuje strukturę komunikat JSON wysyłane przez urządzenie symulowane. Symbole zastępcze w wywołaniach `MessageTemplate` należy użyć składni `${NAME}` gdzie `NAME` jest kluczem z [obiektu stanu urządzenia](#simulation). Ciągi powinny być ujmowane w cudzysłów, nie powinien liczb.

`MessageSchema` Definiuje schemat wiadomości wysyłane przez urządzenie symulowane. Schemat wiadomości jest również opublikowany w Centrum IoT, aby umożliwić aplikacji wewnętrznej bazy danych i ponowne użycie informacji do interpretacji przychodzących danych telemetrycznych.

Obecnie można używać tylko schematów komunikatów JSON. Pola wymienione w schemacie można się z następujących typów:

* Obiekt - zserializowanym przy użyciu formatu JSON
* Plik binarny - serializacji przy użyciu base64
* Text
* Boolean
* Integer
* Double
* DateTime

Aby wysyłać komunikaty telemetryczne w różnych interwałach, należy dodać wiele typów danych telemetrycznych do `Telemetry` tablicy. Poniższy przykład wysyła dane temperatury i wilgotności co 10 sekund, a stan światło na minutę:

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
  },
  {
    "Interval": "00:01:00",
    "MessageTemplate": "{\"lights\":${lights_on}}",
    "MessageSchema": {
      "Name": "RoomLights;v1",
      "Format": "JSON",
      "Fields": {
        "lights": "boolean"
      }
    }
  }
],
```

## <a name="cloudtodevicemethods"></a>CloudToDeviceMethods

Symulowane urządzenie może odpowiadać na metody chmury do urządzenia, wywoływana z usługi IoT hub. `CloudToDeviceMethods` Sekcji w pliku schematu modelu urządzenia:

* Definiuje metody, które może odpowiedzieć urządzenie symulowane.
* Określa plik JavaScript, który zawiera logikę do wykonania.

Symulowane urządzenie wysyła listę obsługiwanych metodach do usługi IoT hub, który jest połączony.

Aby dowiedzieć się więcej na temat pliku JavaScript, który implementuje zachowanie urządzenia, zobacz [zrozumieć zachowania modelu urządzenia](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

Poniższy przykład określa trzy metody obsługiwane i pliki JavaScript, które implementują tych metod:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
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

Widać plików JavaScript dla urządzeń domyślne symulowane w [folder skryptów](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) w witrynie GitHub. Zgodnie z Konwencją, te pliki JavaScript mają sufiks **— metoda** aby odróżnić je od plików, które implementują zachowania stanu.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano sposób tworzenia modelu niestandardowego symulowanego urządzenia. Ten artykuł pokazuje, jak do:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Zdefiniuj model symulowanego urządzenia przy użyciu pliku JSON
> * Określ właściwości symulowane urządzenie
> * Określ, które symulowane urządzenia wysyłają dane telemetryczne
> * Określają metody chmury do urządzenia, które odpowiada urządzenie

Teraz, kiedy znasz już o schematu JSON, sugerowane następnym krokiem jest Dowiedz się, jak [Implementowanie zachowania urządzenia symulowanego](iot-accelerators-remote-monitoring-device-behavior.md).

Aby uzyskać więcej informacji dla deweloperów o rozwiązaniu monitorowania zdalnego Zobacz:

* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Przewodnik po rozwiązywaniu problemów dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
