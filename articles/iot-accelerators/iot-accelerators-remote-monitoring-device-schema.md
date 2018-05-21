---
title: Schemat urządzenia w rozwiązaniu monitorowania zdalnego - Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano schematu JSON, który definiuje symulowane urządzenie zdalne rozwiązanie monitorowania.
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
ms.openlocfilehash: 186eaee952435573a861d144195c3165e4940cc1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="understand-the-device-model-schema"></a>Zrozumienie schematu modelu urządzenia

Symulowane urządzeń w zdalnego rozwiązanie monitorowania służy do testowania jego zachowanie. Podczas wdrażania zdalnego rozwiązanie monitorowania, automatycznie zostanie zainicjowana kolekcji urządzeń symulowane. Można dostosować istniejących urządzeń symulowane lub Utwórz swój własny.

W tym artykule opisano schematu modelu urządzenia, która określa zachowanie symulowane urządzenie. Model urządzenia jest przechowywany w pliku JSON.

Następujące artykuły odnoszą się do bieżącego artykułu:

* [Implementuje zachowanie modelu urządzenia](../iot-suite/iot-suite-remote-monitoring-device-behavior.md) opisuje pliki JavaScript przy użyciu zachowanie symulowane urządzenie.
* [Utwórz nowy symulowane urządzenie](iot-accelerators-remote-monitoring-test.md) umieszcza je na wszystkich elementów i pokazuje, jak wdrożyć nowy typ symulowane urządzenie do rozwiązania.

W tym artykule omówiono sposób wykonywania następujących zadań:

>[!div class="checklist"]
> * Zdefiniuj modelu symulowane urządzenie przy użyciu pliku JSON
> * Określ właściwości symulowane urządzenie
> * Określ dane telemetryczne, które wysyła symulowane urządzenie
> * Określ metody chmury do urządzenia, które odpowiada urządzenia

## <a name="the-parts-of-the-device-model-schema"></a>Części schematu modelu urządzenia

Każdy model urządzenia, takie jak Chłodnica lub ciężarówka, definiuje typ symulowane urządzenie, aby nawiązać połączenie zdalne rozwiązanie monitorowania. Każdy model urządzenia są przechowywane w pliku JSON z następującego schematu najwyższego poziomu:

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

Pliki schematu dla urządzeń domyślne symulowane w można wyświetlać [folderu devicemodels](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) w witrynie GitHub.

W poniższej tabeli opisano wpisy schematu najwyższego poziomu:

| Schemat wejścia | Opis |
| -- | --- |
| `SchemaVersion` | Wersja schematu jest zawsze `1.0.0` i jest specyficzna dla format tego pliku. |
| `Id` | Unikatowy identyfikator dla tego modelu urządzenia. |
| `Version` | Określa wersję modelu urządzenia. |
| `Name` | Przyjazna nazwa dla modelu urządzenia. |
| `Description` | Opis modelu urządzenia. |
| `Protocol` | Protokół połączenia urządzenia używa. Może być jednym z `AMQP`, `MQTT`, i `HTTP`. |

W poniższych sekcjach opisano w innych sekcjach schematu JSON:

## <a name="simulation"></a>Symulacja

W `Simulation` sekcji, zdefiniuj wewnętrzny stan klasy symulowane urządzenie. Wartości danych telemetrycznych wysłanych przez urządzenie musi być częścią tego stanu urządzenia.

Definicja stan urządzenia ma dwa elementy:

* `InitialState` Definiuje wartości początkowe dla wszystkich właściwości obiektu stanu urządzenia.
* `Script` Określa plik JavaScript, że działa zgodnie z harmonogramem, aby zaktualizować stan urządzenia. Do wartości danych telemetrycznych wysłanych przez urządzenie losowe, można użyć tego pliku skryptu.

Aby dowiedzieć się więcej na temat plik JavaScript, która aktualizuje obiekt stanu urządzenia, zobacz [zrozumienie zachowania modelu urządzenia](iot-accelerators-remote-monitoring-device-behavior.md).

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
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

Działa usługa symulacji **Chłodnica-01-state.js** pliku co pięć sekund, aby zaktualizować stan urządzenia. Pliki języka JavaScript dla urządzeń domyślne symulowane w można wyświetlić [folder skryptów](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) w witrynie GitHub. Konwencja, te pliki JavaScript mają sufiks **— stan** aby odróżnić je od plików, które implementuje metody zachowania.

## <a name="properties"></a>Właściwości

`Properties` Sekcji schemat definiuje wartości właściwości urządzenie raportuje do rozwiązania. Na przykład:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

Po uruchomieniu rozwiązania wysyła zapytanie wszystkie symulowanego urządzenia do tworzenia listy `Type` wartości do użycia w Interfejsie użytkownika. W tym rozwiązaniu zastosowano `Latitiude` i `Longitude` właściwości, aby dodać lokalizację urządzenia do mapy na pulpicie nawigacyjnym.

## <a name="telemetry"></a>Telemetria

`Telemetry` Tablicy zawiera listę wszystkich typów danych telemetrycznych symulowane urządzenie wysyła do rozwiązania.

Poniższy przykład wysyła komunikat telemetrii JSON co 10 sekund z `floor`, `vibration`, i `temperature` dane z czujników krótka:

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

`MessageTemplate` definiuje strukturę JSON wiadomości wysyłane przez symulowane urządzenie. Symbole zastępcze w `MessageTemplate` należy użyć składni `${NAME}` gdzie `NAME` to klucz z [obiektu stanu urządzenia](#simulation). Ciągi powinny być podawane, numery nie powinien.

`MessageSchema` Definiuje schemat komunikat wysłany przez symulowane urządzenie. Centrum IoT, aby umożliwić aplikacji wewnętrznej bazy danych ponownie wykorzystać informacje interpretować przychodzące dane telemetryczne również opublikowane schematu wiadomości.

Obecnie można używać tylko schematy komunikat JSON. Pola wymienione w schemacie mogą być następujące:

* Obiekt - zserializowanym przy użyciu JSON
* Serializacja binarna — wykonana za pomocą base64
* Tekst
* Wartość logiczna
* Liczba całkowita
* O podwójnej precyzji
* DateTime

Aby wysyłać dane telemetryczne w różnych odstępach czasu, Dodaj wiele typów danych telemetrycznych do `Telemetry` tablicy. Poniższy przykład wysyła dane temperatury i wilgotności co 10 sekund i stan światła co minutę:

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

Symulowane urządzenie może odpowiadać na chmury do urządzenia metody wywoływane z zdalnego rozwiązanie monitorowania. `CloudToDeviceMethods` Sekcji w pliku schematu modelu urządzenia:

* Definiuje metody, które może odpowiadać symulowane urządzenie.
* Określa plik JavaScript, który zawiera logikę do wykonania.

Symulowane urządzenie wysyła listę metod obsługiwanych do zdalnego rozwiązanie monitorowania.

Aby uzyskać więcej informacji na temat pliku JavaScript, który implementuje zachowanie danego urządzenia, zobacz [zrozumienie zachowania modelu urządzenia](iot-accelerators-remote-monitoring-device-behavior.md).

W poniższym przykładzie trzy obsługiwane metody i pliki JavaScript, które implementują tych metod:

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

Pliki języka JavaScript dla urządzeń domyślne symulowane w można wyświetlić [folder skryptów](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) w witrynie GitHub. Konwencja, te pliki JavaScript mają sufiks **-metoda** aby odróżnić je od plików, które implementuje zachowanie stanu.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano sposób tworzenia modelu niestandardowych symulowane urządzenie. W tym artykule pokazano należy jak do:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Zdefiniuj modelu symulowane urządzenie przy użyciu pliku JSON
> * Określ właściwości symulowane urządzenie
> * Określ dane telemetryczne, które wysyła symulowane urządzenie
> * Określ metody chmury do urządzenia, które odpowiada urządzenia

Teraz, kiedy znasz już o schematu JSON, sugerowane następnym krokiem jest Dowiedz się, jak [implementuje zachowanie symulowane urządzenie](iot-accelerators-remote-monitoring-device-behavior.md).

Aby uzyskać więcej informacji o deweloperów o zdalnego rozwiązanie monitorowania zobacz:

* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Przewodnik po rozwiązywaniu problemów dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->