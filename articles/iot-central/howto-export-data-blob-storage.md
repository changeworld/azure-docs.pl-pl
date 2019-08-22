---
title: Eksportowanie danych do usługi Azure Blob Storage | Microsoft Docs
description: Jak wyeksportować dane z aplikacji IoT Central platformy Azure do platformy Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/08/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 7366072dbf6b000981899a56ca1c8cfe6af6f04a
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876046"
---
# <a name="export-your-data-to-azure-blob-storage"></a>Eksportowanie danych do usługi Azure Blob Storage

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

*Ten temat ma zastosowanie do administratorów.*

W tym artykule opisano sposób korzystania z funkcji ciągłego eksportu danych w usłudze Azure IoT Central w celu okresowego eksportowania danych do **konta usługi Azure Blob Storage**. **Pomiary**, **urządzenia**i **Szablony urządzeń** można eksportować do plików w formacie Apache Avro. Wyeksportowane dane mogą służyć do analizy ścieżki zimnej, takiej jak modele szkoleniowe w Azure Machine Learning lub długoterminowej analizie trendów w programie Microsoft Power BI.

> [!Note]
> Po włączeniu ciągłego eksportowania danych w tym momencie otrzymujesz tylko dane z tego momentu. Obecnie nie można pobrać danych przez czas, gdy ciągły eksport danych jest wyłączony. Aby zachować bardziej historyczne dane, należy wczesnie włączyć ciągły eksport danych.


## <a name="prerequisites"></a>Wymagania wstępne

- Musisz być administratorem w aplikacji IoT Central


## <a name="set-up-export-destination"></a>Skonfiguruj miejsce docelowe eksportu

Jeśli nie masz istniejącego magazynu do eksportowania, wykonaj następujące kroki:

## <a name="create-storage-account"></a>Utwórz konto magazynu

1. Utwórz [nowe konto magazynu w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Więcej informacji można znaleźć w dokumentacji [usługi Azure Storage](https://aka.ms/blobdocscreatestorageaccount).
2. W polu Typ konta wybierz pozycję **ogólnego przeznaczenia** lub **magazynu obiektów BLOB**.
3. wybierz subskrypcję. 

    > [!Note] 
    > Teraz możesz eksportować dane do innych subskrypcji, które **nie są takie same** jak dla aplikacji z opcją płatność zgodnie z rzeczywistym użyciem IoT Central. W tym przypadku zostanie nawiązane połączenie przy użyciu parametrów połączenia.

4. Utwórz kontener na koncie magazynu. Przejdź do swojego konta magazynu. W obszarze **BLOB Service**wybierz pozycję **Przeglądaj obiekty blob**. Wybierz pozycję **+ kontener** u góry, aby utworzyć nowy kontener


## <a name="set-up-continuous-data-export"></a>Konfigurowanie ciągłego eksportu danych

Teraz, gdy masz miejsce docelowe magazynu, do którego chcesz wyeksportować dane, wykonaj następujące kroki, aby skonfigurować ciągły eksport danych. 

1. Zaloguj się do aplikacji IoT Central.

2. W menu po lewej stronie wybierz pozycję **ciągły eksport danych**.

    > [!Note]
    > Jeśli nie widzisz ciągłego eksportu danych w menu po lewej stronie, nie jesteś administratorem w swojej aplikacji. Skontaktuj się z administratorem, aby skonfigurować eksportowanie danych.

    ![Utwórz nowe centrum zdarzeń CDE](media/howto-export-data/export_menu1.png)

3. Wybierz przycisk **+ Nowy** w prawym górnym rogu. Wybierz pozycję **Azure Blob Storage** jako lokalizację docelową eksportu. 

    > [!NOTE] 
    > Maksymalna liczba eksportów na aplikację wynosi pięć. 

    ![Utwórz nowy ciągły eksport danych](media/howto-export-data/export_new1.png)

4. W polu listy rozwijanej wybierz **przestrzeń nazw konta magazynu**. Możesz również wybrać ostatnią opcję z listy, która jest wprowadzeniem **parametrów połączenia**. 

    > [!NOTE] 
    > W **tej samej subskrypcji,** w której znajduje się aplikacja IoT Central, zobaczysz tylko przestrzenie nazw kont magazynu. Jeśli chcesz wyeksportować do lokalizacji docelowej poza tą subskrypcją, wybierz pozycję **wprowadź parametry połączenia** i zobacz krok 5.

    > [!NOTE] 
    > W przypadku 7-dniowych aplikacji próbnych jedynym sposobem skonfigurowania ciągłego eksportu danych jest użycie parametrów połączenia. Wynika to z faktu, że 7-dniowe aplikacje próbne nie mają skojarzonej subskrypcji platformy Azure.

    ![Utwórz nowe centrum zdarzeń CDE](media/howto-export-data/export-create-blob.png)

5. Obowiązkowe W przypadku wybrania opcji **wprowadź parametry połączenia**pojawi się nowe pole umożliwiające wklejenie parametrów połączenia. Aby uzyskać parametry połączenia dla:
    - Konto magazynu przejdź do konta magazynu w Azure Portal.
        - W obszarze **Ustawienia**wybierz pozycję **klucze dostępu** .
        - Skopiuj parametry połączenia Klucz1 lub parametry połączenia klucz2
 
6. Wybierz kontener z listy rozwijanej.

7. W obszarze **dane do eksportowania**Określ każdy typ danych do wyeksportowania, ustawiając typ na wartość **włączone**.

6. Aby włączyć funkcję ciągłego eksportowania danych, upewnij się, że **Eksportowanie danych** jest **włączone**. Wybierz pozycję **Zapisz**.

   ![Konfigurowanie ciągłego eksportowania danych](media/howto-export-data/export-list-blob.png)

7. Po kilku minutach dane zostaną wyświetlone w wybranym miejscu docelowym.


## <a name="export-to-azure-blob-storage"></a>Eksportuj do Blob Storage platformy Azure

Dane pomiarów, urządzeń i szablonów urządzeń są eksportowane do konta magazynu raz na minutę, przy czym każdy plik zawierający partię zmian od ostatniego wyeksportowanego pliku. Eksportowane dane są w formacie [Apache Avro](https://avro.apache.org/docs/current/index.html) i zostaną wyeksportowane do trzech folderów. Domyślne ścieżki na koncie magazynu:
- Komunikaty: {Container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
- Urządzenia: {Container}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
- Szablony urządzeń: {Container}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro

### <a name="measurements"></a>Miary

Eksportowane dane pomiarów zawierają wszystkie nowe komunikaty odebrane przez IoT Central ze wszystkich urządzeń w tym czasie. Eksportowane pliki używają tego samego formatu co pliki komunikatów wyeksportowane przez [IoT Hub Routing komunikatów](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) do magazynu obiektów BLOB.

> [!NOTE]
> Urządzenia, które wysyłają pomiary, są reprezentowane przez identyfikatory urządzeń (zobacz następujące sekcje). Aby uzyskać nazwy urządzeń, wyeksportuj migawki urządzeń. Należy skorelować każdy rekord komunikatu przy użyciu **connectionDeviceId** , który odpowiada identyfikatorowi **deviceId** rekordu urządzenia.

Poniższy przykład przedstawia rekord w zdekodowanym pliku Avro:

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

Gdy ciągły eksport danych jest najpierw włączony, wyeksportowana jest pojedyncza migawka ze wszystkimi urządzeniami. Każde urządzenie obejmuje:
- `id`urządzenia w IoT Central
- `name`urządzenia
- `deviceId`z [usługi Device](https://aka.ms/iotcentraldocsdps) Provisioning
- Informacje o szablonie urządzenia
- Wartości właściwości
- Ustawianie wartości

Nowa migawka jest zapisywana raz na minutę. Migawka obejmuje następujące:

- Dodano nowe urządzenia od ostatniej migawki.
- Urządzenia ze zmienioną właściwością i wartościami ustawień od momentu ostatniej migawki.

> [!NOTE]
> Urządzenia usunięte od ostatniej migawki nie są eksportowane. Obecnie migawki nie mają wskaźników dla usuniętych urządzeń.
>
> Szablon urządzenia, do którego należy każde urządzenie, jest reprezentowany przez identyfikator szablonu urządzenia. Aby uzyskać nazwę szablonu urządzenia, wyeksportuj migawki szablonów urządzeń.

Rekord w zdekodowanym pliku Avro może wyglądać następująco:

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

### <a name="device-templates"></a>Szablony urządzeń

Gdy ciągły eksport danych jest najpierw włączony, wyeksportowana jest pojedyncza migawka ze wszystkimi szablonami urządzeń. Każdy szablon urządzenia zawiera:
- `id`szablonu urządzenia
- `name`szablonu urządzenia
- `version`szablonu urządzenia
- Typy danych pomiarów oraz wartości minimalne/maksymalne.
- Typy danych właściwości i wartości domyślne.
- Ustawianie typów danych i wartości domyślnych.

Nowa migawka jest zapisywana raz na minutę. Migawka obejmuje następujące:

- Dodano nowe szablony urządzeń od ostatniej migawki.
- Szablony urządzeń ze zmienionymi pomiarami, właściwościami i definicjami ustawień od momentu ostatniej migawki.

> [!NOTE]
> Szablony urządzeń usunięte od momentu ostatniej migawki nie są eksportowane. Obecnie migawki nie mają wskaźników dla usuniętych szablonów urządzeń.

Rekord w zdekodowanym pliku Avro może wyglądać następująco:

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

## <a name="read-exported-avro-files"></a>Odczytaj wyeksportowane pliki Avro

Avro to format binarny, więc plików nie można odczytać w ich pierwotnym stanie. Pliki można zdekodować do formatu JSON. W poniższych przykładach pokazano, jak przeanalizować pomiary, urządzenia i Avro pliki szablonów urządzeń. Przykłady są zgodne z przykładami opisanymi w poprzedniej sekcji.

### <a name="read-avro-files-by-using-python"></a>Odczytywanie plików Avro przy użyciu języka Python

#### <a name="install-pandas-and-the-pandavro-package"></a>Zainstaluj Pandas i pakiet pandavro

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Analizowanie pliku pomiarów Avro

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
    transformed["device_id"] = measurements["SystemProperties"].apply(
        lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(
        lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)
```

#### <a name="parse-a-devices-avro-file"></a>Analizowanie pliku Avro urządzeń

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
    transformed["template_id"] = devices["deviceTemplate"].apply(
        lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(
        lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

#### <a name="parse-a-device-templates-avro-file"></a>Analizowanie pliku Avro szablonów urządzeń

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
    transformed["fan_speed"] = templates["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Odczytaj pliki Avro za pomocąC#

#### <a name="install-the-microsofthadoopavro-package"></a>Zainstaluj pakiet Microsoft. Hadoop. avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Analizowanie pliku pomiarów Avro

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

#### <a name="parse-a-devices-avro-file"></a>Analizowanie pliku Avro urządzeń

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

#### <a name="parse-a-device-templates-avro-file"></a>Analizowanie pliku Avro szablonów urządzeń

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

### <a name="read-avro-files-by-using-javascript"></a>Odczytywanie plików Avro przy użyciu języka JavaScript

#### <a name="install-the-avsc-package"></a>Instalowanie pakietu AVSC

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Analizowanie pliku pomiarów Avro

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

#### <a name="parse-a-devices-avro-file"></a>Analizowanie pliku Avro urządzeń

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

#### <a name="parse-a-device-templates-avro-file"></a>Analizowanie pliku Avro szablonów urządzeń

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

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wyeksportować dane, przejdź do następnego kroku:

> [!div class="nextstepaction"]
> [Jak wizualizować dane w Power BI](howto-connect-powerbi.md)
