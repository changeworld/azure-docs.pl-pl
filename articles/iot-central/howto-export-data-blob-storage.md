---
title: Eksportuj dane do usługi Azure Blob Storage | Dokumentacja firmy Microsoft
description: Sposób eksportowania danych z usługi Azure IoT Central aplikacji do usługi Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: ae1e71170952a2f05e371de68b519eba522e3298
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318723"
---
# <a name="export-your-data-to-azure-blob-storage"></a>Eksportuj dane do usługi Azure Blob Storage

*W tym temacie mają zastosowanie do administratorów.*

Ten artykuł omawia bardziej jak używać funkcji eksportu ciągłymi danymi w usłudze Azure IoT Central, aby okresowo eksportowały dane do swojej **konta usługi Azure Blob storage**. Możesz wyeksportować **pomiarów**, **urządzeń**, i **szablonów urządzeń** do plików w formacie Apache Avro. Wyeksportowane dane, może służyć do ścieżki nieaktywnej analizy, takich jak szkolenia modeli w usłudze Azure Machine Learning lub długoterminowej analizy trendu w usłudze Microsoft Power BI.

> [!Note]
> Jeszcze raz po włączeniu ciągły Eksport danych otrzymasz tylko dane od tej pory wartości. Obecnie nie można pobrać danych w czasie, gdy ciągły Eksport danych zostało wyłączone. Do przechowania większej ilości danych historycznych, należy włączyć funkcję ciągły Eksport danych z wcześniej.


## <a name="prerequisites"></a>Wymagania wstępne

- Musisz być administratorem w Twojej aplikacji IoT Central

## <a name="export-to-azure-blob-storage"></a>Eksportowanie do usługi Azure Blob Storage

Pomiary, urządzeń i danych szablonów urządzeń zostaną wyeksportowane do swojego konta magazynu, raz na minutę, z każdego pliku, zawierającego partii zmiany od ostatniego wyeksportowany plik. Wyeksportowane dane znajdują się w [Apache Avro](https://avro.apache.org/docs/current/index.html) formatowania i mają zostać wyeksportowane w trzy foldery. Ścieżki domyślnej w ramach konta magazynu są:
    - Komunikaty: {container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
    - Urządzenia: {container}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
    - Szablony urządzenia: {container}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro

### <a name="measurements"></a>Miary

Dane pomiarów wyeksportowanego mają nowe komunikaty odbierane przez IoT Central ze wszystkich urządzeń, w tym czasie. Wyeksportowane pliki pełnić plików wiadomości wyeksportowany przez ten sam format [routing komunikatów usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) do magazynu obiektów Blob.

> [!NOTE]
> Urządzenia, które wysyłać pomiary są reprezentowane przez identyfikatory urządzeń (patrz poniżej). Aby uzyskać nazwy urządzenia, należy wyeksportować migawki urządzenia. Korelowanie poszczególnych rekordów komunikatów za pomocą **connectionDeviceId** odpowiadający **deviceId** rekordu urządzenia.

Poniższy przykład przedstawia rekord w pliku Avro zdekodowany:

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "<connectionDeviceId>",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "<generationId>",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Urządzenia

Po pierwszym włączeniu ciągły Eksport danych migawki jednego ze wszystkimi urządzeniami są eksportowane. Każde urządzenie obejmuje:
- `id` z urządzeń IoT Central
- `name` urządzenia
- `deviceId` z [usługi Device Provisioning](https://aka.ms/iotcentraldocsdps)
- Informacje o szablonie urządzenia
- Wartości właściwości
- Ustawianie wartości

Nowa migawka są zapisywane jeden raz na minutę. Migawka zawiera:

- Dodane od czasu ostatniego migawki nowych urządzeń.
- Urządzenia z właściwością zmienione i ustawienia wartości, ponieważ ostatnia migawka.

> [!NOTE]
> Urządzenia został usunięty, ponieważ ostatnia migawka nie są eksportowane. Obecnie migawek nie mają wskaźników dla usuniętego urządzenia.
>
> Każde urządzenie należy do szablonu urządzenia jest reprezentowany przez urządzenie identyfikatora szablonu. Aby uzyskać nazwę szablonu urządzenia, należy wyeksportować migawek szablonu urządzenia.

Rekord w plik zdekodowany Avro może wyglądać następująco:

```json
{
    "id": "<id>",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
        "id": "<template id>",
        "version": "1.0.0"
    },
    "properties": {
        "cloud": {
            "location": "New York",
            "maintCon": true,
            "tempThresh": 20
        },
        "device": {
            "lastReboot": "2018-02-09T22:22:47.156Z"
        }
    },
    "settings": {
        "device": {
            "fanSpeed": 0
        }
    }
}
```

### <a name="device-templates"></a>Szablony urządzenia

Po pierwszym włączeniu ciągły Eksport danych pojedynczego migawki przy użyciu wszystkich szablonów urządzenia są eksportowane. Każdy szablon urządzenia obejmują:
- `id` szablonu urządzenia
- `name` szablonu urządzenia
- `version` szablonu urządzenia
- Typy danych pomiaru i minimalnej/maksymalnej wartości.
- Typy danych właściwości i wartości domyślne.
- Ustawianie typów danych i wartości domyślne.

Nowa migawka są zapisywane jeden raz na minutę. Migawka zawiera:

- Nowe szablony urządzenie dodane od czasu ostatniego migawki.
- Szablony urządzenia przy użyciu zmienionego pomiarów, właściwości i ustawienia definicji, ponieważ ostatnia migawka.

> [!NOTE]
> Szablony urządzenia usunięty od czasu ostatniego migawki nie są eksportowane. Obecnie migawek nie mają wskaźników dla szablonów usuniętego urządzenia.

Rekord w plik zdekodowany Avro może wyglądać następująco:

```json
{
    "id": "<id>",
    "name": "Refrigerated Vending Machine",
    "version": "1.0.0",
    "measurements": {
        "telemetry": {
            "humidity": {
                "dataType": "double",
                "name": "Humidity"
            },
            "magnetometerX": {
                "dataType": "double",
                "name": "Magnetometer X"
            },
            "magnetometerY": {
                "dataType": "double",
                "name": "Magnetometer Y"
            },
            "magnetometerZ": {
                "dataType": "double",
                "name": "Magnetometer Z"
            }
        },
        "states": {
            "connectivity": {
                "dataType": "enum",
                "name": "Connectivity"
            }
        },
        "events": {
            "opened": {
                "name": "Door Opened",
                "category": "informational"
            }
        }
    },
    "settings": {
        "device": {
            "fanSpeed": {
                "dataType": "double",
                "name": "Fan Speed",
                "initialValue": 0
            }
        }
    },
    "properties": {
        "cloud": {
            "location": {
                "dataType": "string",
                "name": "Location",
                "initialValue": "Seattle"
            },
            "maintCon": {
                "dataType": "boolean",
                "name": "Maintenance Contract",
                "initialValue": true
            },
            "tempThresh": {
                "dataType": "double",
                "name": "Temperature Alert Threshold",
                "initialValue": 30
            }
        },
        "device": {
            "lastReboot": {
                "dataType": "dateTime",
                "name": "Last Reboot"
            }
        }
    }
}
```

## <a name="read-exported-avro-files"></a>Odczyt wyeksportowane pliki Avro

Avro jest format binarny, więc nie można odczytać plików w ich stanie raw. Może zostać odczytany plików do formatu JSON. Poniższe przykłady pokazują, jak można przeanalizować pomiarów, urządzeń i szablony urządzenia pliki Avro. Przykłady odpowiadają przykłady opisanego w poprzedniej sekcji.

### <a name="read-avro-files-by-using-python"></a>Odczytuj pliki Avro przy użyciu języka Python

#### <a name="install-pandas-and-the-pandavro-package"></a>Zainstalować pandas i pakiet pandavro

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Analizowanie plików Avro pomiarów

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)

```

#### <a name="parse-a-devices-avro-file"></a>Analizowanie plików Avro urządzeń

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["deviceId"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)

```

#### <a name="parse-a-device-templates-avro-file"></a>Przeanalizować plik Avro szablony urządzenia

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    templates = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available in the id and version columns.
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Pliki Avro odczytu przy użyciuC#

#### <a name="install-the-microsofthadoopavro-package"></a>Zainstaluj pakiet Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Analizowanie plików Avro pomiarów

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

#### <a name="parse-a-devices-avro-file"></a>Analizowanie plików Avro urządzeń

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("deviceId");

                    // The device template information is stored in a sub-record
                    // under the deviceTemplate field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "Device ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```

#### <a name="parse-a-device-templates-avro-file"></a>Przeanalizować plik Avro szablony urządzenia

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

### <a name="read-avro-files-by-using-javascript"></a>Odczytuj pliki Avro przy użyciu języka Javascript

#### <a name="install-the-avsc-package"></a>Zainstaluj pakiet avsc

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Analizowanie plików Avro pomiarów

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device ID and humidity from each record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the system properties.
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the body from a buffer to a string and parse it.
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property from the body.
        const humidity = body.humidity;

        // Log the retrieved device ID and humidity.
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-devices-avro-file"></a>Analizowanie plików Avro urządzeń

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the deviceId property.
        const deviceId = record.deviceId;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`deviceID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-device-templates-avro-file"></a>Przeanalizować plik Avro szablony urządzenia

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and verison properties.
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device id and humidity.
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Kolejne kroki

Teraz gdy wiesz, jak wyeksportować dane, przejdź do następnego kroku:

> [!div class="nextstepaction"]
> [Jak wizualizować dane w usłudze Power BI](howto-connect-powerbi.md)
