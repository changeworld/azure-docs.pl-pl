---
title: Eksportowanie danych w usłudze Azure IoT Central | Dokumentacja firmy Microsoft
description: Sposób eksportowania danych z aplikacji usługi Azure IoT Central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/3/2018
ms.topic: article
ms.prod: azure-iot-central
manager: peterpr
ms.openlocfilehash: 5b9564dfe40f292d289ee9ed680e816771d0b0ed
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282878"
---
# <a name="export-your-data-in-azure-iot-central"></a>Eksportowanie danych w usłudze Azure IoT Central

Użyj ciągłego eksportu danych, aby okresowo wyeksportować dane do swojego konta usługi Azure Blob Storage. Można eksportować **pomiarów**, **urządzeń**, i **szablonów urządzeń** w plikach [Apache AVRO](https://avro.apache.org/docs/current/index.html) formatu. Wyeksportowane dane na użytek ścieżki nieaktywnej analiz, takich jak szkolenia modeli w usłudze Azure Machine Learning lub długoterminowej analizy trendów w usłudze Power BI.

> [!Note]
> Po włączeniu ciągły Eksport danych otrzymasz tylko dane, które pochodzą od tej pory i nowszych wersjach. Obecnie nie ma możliwości do pobierania danych z podczas ciągłego eksportu danych zostało wyłączone. Włączanie ciągłego eksportu danych przed terminem do przechowania większej ilości danych historycznych!

## <a name="prerequisites"></a>Wymagania wstępne

- Rozszerzone aplikacji 30-dniowej wersji próbnej lub płatnej aplikacji
- Konto platformy Azure z subskrypcją platformy Azure
- Tego samego konta platformy Azure jest administratorem w swojej aplikacji IoT Central
- Tego samego konta platformy Azure ma uprawnienia do tworzenia konta magazynu ani uzyskać dostępu do istniejącego konta magazynu w tej samej subskrypcji platformy Azure

## <a name="types-of-data-to-export"></a>Typy danych do wyeksportowania

### <a name="measurements"></a>Miary

Pomiary, które urządzenia wysyłają wyeksportowane w konto magazynu. Pomiary dotyczące prawdziwych danych są eksportowane około raz na minutę, zawierający wszystkie nowe wiadomości, które zostały odebrane przez IoT Central ze wszystkich urządzeń, w tym przedziale czasu. Wyeksportowane pliki AVRO znajdują się w tym samym formacie co pliki wiadomości wyeksportowany przez [routing komunikatów usługi IoT Hub](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) do magazynu obiektów blob.

> [!NOTE]
> Urządzenia, które są wysyłane pomiary są reprezentowane przez identyfikatory urządzeń (patrz poniżej). Aby uzyskać nazwy urządzenia, należy wyeksportować urządzeń migawki zbyt. Można skorelować poszczególnych rekordów komunikatów za pomocą connectionDeviceId, który pasuje do identyfikatora urządzenia.

Jest to przykład rekordu w pliku zdekodowany AVRO.

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "2383d8ba-c98c-403a-b4d5-8963859643bb",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "636614021491644195",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Urządzenia

Po pierwszym włączeniu ciągły Eksport danych pojedynczego migawkę zawierającą wszystkie urządzenia są eksportowane. Obejmuje to:
- Identyfikatory urządzeń
- Nazwy urządzenia
- Urządzenie identyfikatorów szablonów.
- Wartości właściwości
- Wartości ustawień

Około raz na minutę nową migawkę jest napisanych zawierające:

- Nowe urządzenia, które zostały dodane od ostatniego migawki
- Urządzenia, których wartości właściwości i ustawienia zmienione od czasu ostatniego migawki

> [!NOTE]
> Urządzenia, które zostały usunięte, ponieważ ostatnia migawka nie są eksportowane. W migawek dla urządzeń, które zostały usunięte w tej chwili nie ma żadnych wskaźników.

> [!NOTE]
> Każde urządzenie należy do szablonu urządzenia jest reprezentowany przez urządzenie identyfikatora szablonu. Aby uzyskać nazwę szablonu urządzenia, należy wyeksportować urządzenia szablonu migawki zbyt.

Każdy rekord w plik zdekodowany AVRO wygląda następująco:

```json
{
    "id": "2383d8ba-c98c-403a-b4d5-8963859643bb",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceTemplate": {
        "id": "c318d580-39fc-4aca-b995-843719821049",
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

Po pierwszym włączeniu ciągły Eksport danych pojedynczego migawkę zawierającą wszystkie szablony urządzenia są eksportowane. Obejmuje to: 
- Urządzenie identyfikatorów szablonów.
- Typy danych pomiaru i minimalnej/maksymalnej wartości
- Typy danych właściwości i wartości domyślne
- Ustawienia typów danych i wartości domyślne

Około raz na minutę nową migawkę jest napisanych zawierające:

- Nowe szablony urządzenia, które zostały dodane od ostatniego migawki
- Szablony urządzenia, które posiada pomiarów, właściwości i definicje ustawień, które zmieniło ostatnia migawka

> [!NOTE]
> Szablony urządzenia, które zostały usunięte, ponieważ ostatnia migawka nie są eksportowane. W migawek dla szablonów urządzeń, które zostały usunięte w tej chwili nie jest wskaźnik.

Każdy rekord w plik zdekodowany AVRO wygląda następująco:

```json
{
    "id": "c318d580-39fc-4aca-b995-843719821049",
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

## <a name="how-to-set-up-data-export"></a>Jak skonfigurować Eksport danych

1. Jeśli nie masz jeszcze jeden, Utwórz konto usługi Azure Storage **w subskrypcji platformy Azure, że Twoja aplikacja jest w**. [Kliknij tutaj,](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) przejście do witryny Azure portal do utworzenia nowego konta usługi Azure Storage.

    - Wybierz *ogólnego przeznaczenia* lub *magazynu obiektów Blob* rodzaju konta
    - Wybierz subskrypcję, której znajduje się aplikacja IoT Central. Jeśli nie widzisz subskrypcji może być konieczne Zaloguj się do innego konta platformy Azure lub poproś o dostęp do subskrypcji.
    - Można wybrać istniejącą grupę zasobów lub Utwórz nową. Dowiedz się więcej o [sposób tworzenia nowego konta magazynu.](https://aka.ms/blobdocscreatestorageaccount)

2. Utwórz kontener na koncie magazynu, aby wyeksportować dane IoT Central. Przejdź do swojego konta magazynu -> Przeglądaj obiekty BLOB, a następnie utwórz nowy kontener. ![Tworzenie obrazu kontenera](media/howto-export-data/createcontainer.png)

3. Zaloguj się do aplikacji przy użyciu tego samego konta usługi Azure IoT Central.
1. Przejdź do pozycji Administracja -> ciągły Eksport danych.
![IoT Central CRP](media/howto-export-data/continuousdataexport.PNG)
1. Korzystając z list rozwijanych, wybierz konto magazynu i kontener. Następnie użyj przełączników, aby włączyć lub wyłączyć różne rodzaje danych do wyeksportowania.
1. Na koniec Włącz danych eksportu ciągłego przy użyciu przełącznika i trafień "Zapisz".
1. Poczekaj kilka minut, a powinien zostać wyświetlony danych pojawiają się na koncie magazynu. Możesz przejść do konta magazynu, zaznacz Przeglądaj obiekty BLOB, wybierz kontener, a zostaną wyświetlone trzy foldery. Dostępne są następujące ścieżki domyślnej dla plików AVRO zawierające różne rodzaje danych:
- Komunikaty: **{container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- Urządzenia: **{container}/devices/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- Szablony urządzenia: **{container}/deviceTemplates/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**

## <a name="how-to-read-exported-avro-files"></a>Jak odczytać wyeksportowane pliki AVRO

AVRO jest format binarny, więc nie można odczytać plików w ich stanie raw. Mogą one zostać zdekodowane do formatu JSON. Poniższe przykłady pokazują, jak można przeanalizować pomiarów, urządzeń i szablonów urządzeń pliki AVRO przy użyciu powyższych przykładach.

## <a name="python"></a>Python

### <a name="install-pandas-and-the-pandaavro-package"></a>Zainstaluj Pandas oraz pakiet PandaAvro:

```python
pip install pandas
pip install pandavro
```
### <a name="parse-measurements-avro-file"></a>Analizowanie plików AVRO pomiarów

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    measurements = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary with the device id
    # located under the "connectionDeviceId" key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of utf-8 bytes that is stringified and parsed
    # as json. In this example, we pull the "humidity" property off of each one
    # to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, we print the new DataFrame with our device ids and humidities
    print(transformed)

```
### <a name="parse-devices-avro-file"></a>Analizowanie plików AVRO urządzeń

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    devices = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device id is available directly in the "id" column
    transformed["device_id"] = devices["id"]

    # The template id and version are present in a dictionary under the
    # deviceTemplate column
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)

```

### <a name="parse-device-templates-avro-file"></a>Analizowanie pliku AVRO szablony urządzenia

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    templates = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available directly in the "id" and "version"
    # columns, respectively
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)
```

## <a name="c"></a>C#

### <a name="install-microsofthadoopavro"></a>Zainstaluj Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

### <a name="parse-measurements-avro-file"></a>Analizowanie plików AVRO pomiarów

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
            // For one Avro Container, it may contain multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the fields
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

### <a name="parse-devices-avro-file"></a>Analizowanie plików AVRO urządzeń

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var deviceId = record.GetField<string>("id");

                    // The device template information is stored in a sub-record
                    // under the "deviceTemplate" field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
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
### <a name="parse-device-templates-avro-file"></a>Analizowanie pliku AVRO szablony urządzenia

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
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

## <a name="javascript"></a>Javascript

### <a name="install-avsc"></a>Zainstaluj avsc

```javascript
npm install avsc
```

### <a name="parse-measurements-avro-file"></a>Analizowanie plików AVRO pomiarów

```javascript
const avro = require('avsc');

// Read the avro file and parse the device id and humidity from each record
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the system properties
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the Body from a Buffer to a string and parse it
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property off of the body
        const humidity = body.humidity;

        // Log the device id and humidity we found
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-devices-avro-file"></a>Analizowanie plików AVRO urządzeń

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the id property
        const deviceId = record.id;

        // Fetch the tempalte id and version from the deviceTemplate property
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`ID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-device-templates-avro-file"></a>Analizowanie pliku AVRO szablony urządzenia

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template id and version from the id and verison properties
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak eksportować dane, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Jak wizualizować dane w usłudze Power BI](howto-connect-powerbi.md)