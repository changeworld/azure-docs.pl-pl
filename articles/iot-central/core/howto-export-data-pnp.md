---
title: Eksportowanie danych usługi Azure IoT Central | Microsoft Docs
description: Jak wyeksportować dane z aplikacji IoT Central platformy Azure do usługi Azure Event Hubs, Azure Service Bus i Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/15/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 118d2b42bc14a943aa3fa60b34aa1c151d5dea4c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176307"
---
# <a name="export-your-azure-iot-central-datapreview-features"></a>Eksportowanie danych usługi Azure IoT Central (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Ten temat ma zastosowanie do administratorów.*

W tym artykule opisano sposób używania funkcji ciągłego eksportu danych w usłudze Azure IoT Central do eksportowania danych do wystąpień usługi Azure Event Hubs, Azure Service Bus lub Azure Blob Storage. Dane są eksportowane w formacie JSON i mogą zawierać informacje telemetryczne, informacje o urządzeniu i szablon urządzenia. Użyj wyeksportowanych danych dla:

- Szczegółowe informacje i analiza z ciepłą ścieżką. Ta opcja obejmuje wyzwalanie reguł niestandardowych w programie Azure Stream Analytics, wyzwalanie niestandardowych przepływów pracy w programie Azure Logic Apps lub przekazywanie ich przez Azure Functions do przetworzenia.
- Analiza zimnej ścieżki, na przykład modele szkoleniowe, w Azure Machine Learning lub długoterminowej analizie trendów w programie Microsoft Power BI.

> [!Note]
> Po włączeniu ciągłego eksportowania danych otrzymujesz tylko dane z tego momentu. Obecnie nie można pobrać danych przez czas, gdy ciągły eksport danych jest wyłączony. Aby zachować bardziej historyczne dane, należy wczesnie włączyć ciągły eksport danych.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz być administratorem w aplikacji IoT Central

## <a name="set-up-export-destination"></a>Skonfiguruj miejsce docelowe eksportu

Miejsce docelowe eksportu musi istnieć przed skonfigurowaniem ciągłego eksportowania danych.

### <a name="create-event-hubs-namespace"></a>Tworzenie przestrzeni nazw Event Hubs

Jeśli nie masz istniejącej przestrzeni nazw Event Hubs do eksportowania do programu, wykonaj następujące kroki:

1. Utwórz [nową przestrzeń nazw Event Hubs w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Więcej informacji można znaleźć w witrynie [Azure Event Hubs docs](../../event-hubs/event-hubs-create.md).

2. wybierz subskrypcję. Możesz wyeksportować dane do innych subskrypcji, które nie znajdują się w tej samej subskrypcji, co w przypadku aplikacji z opcją płatność zgodnie z rzeczywistym użyciem IoT Central. W tym przypadku łączysz się przy użyciu parametrów połączenia.

3. Utwórz centrum zdarzeń w przestrzeni nazw Event Hubs. Przejdź do obszaru nazw, a następnie wybierz pozycję **+ centrum zdarzeń** u góry, aby utworzyć wystąpienie centrum zdarzeń.

### <a name="create-service-bus-namespace"></a>Tworzenie przestrzeni nazw Service Bus

Jeśli nie masz istniejącej przestrzeni nazw Service Bus do eksportowania do programu, wykonaj następujące kroki:

1. Utwórz [nową przestrzeń nazw Service Bus w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Więcej informacji można znaleźć w dokumentacji [Azure Service Bus](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. wybierz subskrypcję. Możesz wyeksportować dane do innych subskrypcji, które nie znajdują się w tej samej subskrypcji, co w przypadku aplikacji z opcją płatność zgodnie z rzeczywistym użyciem IoT Central. W tym przypadku łączysz się przy użyciu parametrów połączenia.

3. Przejdź do przestrzeni nazw Service Bus, a następnie wybierz pozycję **+ Kolejka** lub **+ temat** u góry, aby utworzyć kolejkę lub temat do wyeksportowania.

W przypadku wybrania Service Bus jako miejsca docelowego eksportu kolejki i tematy nie mogą mieć włączonej sesji lub Wykrywanie duplikatów. Jeśli jedna z tych opcji jest włączona, niektóre komunikaty nie docierają do kolejki lub tematu.

### <a name="create-storage-account"></a>Tworzenie konta magazynu

Jeśli nie masz istniejącego konta usługi Azure Storage do eksportowania, wykonaj następujące czynności:

1. Utwórz [nowe konto magazynu w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Możesz dowiedzieć się więcej na temat tworzenia nowych [kont usługi Azure Blob Storage](https://aka.ms/blobdocscreatestorageaccount) lub [kont magazynu Azure Data Lake Storage v2](../../storage/blobs/data-lake-storage-quickstart-create-account.md).

    - W przypadku wybrania opcji eksportowania danych do konta magazynu Azure Data Lake Storage v2 należy wybrać **BlobStorage** jako **rodzaj konta**.
    - Dane można eksportować do kont magazynu w ramach subskrypcji innych niż te dla aplikacji z opcją płatność zgodnie z rzeczywistym użyciem IoT Central. W tym przypadku zostanie nawiązane połączenie przy użyciu parametrów połączenia.

2. Utwórz kontener na koncie magazynu. Przejdź do swojego konta magazynu. W obszarze **BLOB Service**wybierz pozycję **Przeglądaj obiekty blob**. Wybierz pozycję **+ kontener** u góry, aby utworzyć nowy kontener.

## <a name="set-up-continuous-data-export"></a>Konfigurowanie ciągłego eksportu danych

Teraz, gdy masz miejsce docelowe eksportu danych do programu, wykonaj następujące kroki, aby skonfigurować ciągły eksport danych.

1. Zaloguj się do aplikacji IoT Central.

2. W lewym okienku wybierz pozycję **eksport danych**.

    > [!Note]
    > Jeśli nie widzisz eksportu danych w okienku po lewej stronie, nie jesteś administratorem w swojej aplikacji. Skontaktuj się z administratorem, aby skonfigurować eksportowanie danych.

3. Wybierz przycisk **+ Nowy** w prawym górnym rogu. Wybierz jedną z **Event Hubs platformy Azure**, **Azure Service Bus**lub **Azure Blob Storage** jako lokalizację docelową eksportu. Maksymalna liczba eksportów dla aplikacji wynosi pięć.

    ![Utwórz nowy ciągły eksport danych](media/howto-export-data-pnp/export-new2.png)

4. W polu listy rozwijanej wybierz **przestrzeń nazw Event Hubs**, **Service Bus przestrzeń nazw**, **przestrzeń nazw konta magazynu**lub **wprowadź parametry połączenia**.

    - Widoczne są tylko konta magazynu, Event Hubs przestrzenie nazw i Service Bus przestrzenie nazw w ramach tej samej subskrypcji, w której znajduje się aplikacja IoT Central. Jeśli chcesz wyeksportować do lokalizacji docelowej poza tą subskrypcją, wybierz pozycję **wprowadź parametry połączenia** i zobacz krok 5.
    - W przypadku siedmiu-dniowych wersji próbnych aplikacji jedynym sposobem skonfigurowania ciągłego eksportu danych jest użycie parametrów połączenia. Siedem-dniowe aplikacje próbne nie mają skojarzonej subskrypcji platformy Azure.

    ![Utwórz nowe centrum zdarzeń](media/howto-export-data-pnp/export-eh.png)

5. Obowiązkowe W przypadku wybrania opcji **wprowadź parametry połączenia**pojawi się nowe pole umożliwiające wklejenie parametrów połączenia. Aby uzyskać parametry połączenia dla:
    - Event Hubs lub Service Bus, przejdź do przestrzeni nazw w Azure Portal.
        - W obszarze **Ustawienia**wybierz pozycję **zasady dostępu współdzielonego** .
        - Wybierz domyślną **RootManageSharedAccessKey** lub Utwórz nową
        - Skopiuj podstawowe lub pomocnicze parametry połączenia
    - Konto magazynu przejdź do konta magazynu w Azure Portal:
        - W obszarze **Ustawienia**wybierz pozycję **klucze dostępu** .
        - Skopiuj parametry połączenia Klucz1 lub parametry połączenia klucz2

6. W polu listy rozwijanej wybierz centrum zdarzeń, kolejkę, temat lub kontener.

7. W obszarze **dane do wyeksportowania**wybierz typy danych do wyeksportowania, ustawiając typ na wartość **włączone**.

8. Aby włączyć ciągły eksport danych, upewnij się, że przełącznik **eksportu danych** jest **włączony**. Wybierz pozycję **Zapisz**.

9. Po kilku minutach dane zostaną wyświetlone w wybranym miejscu docelowym.

## <a name="data-format"></a>Format danych

Dane są eksportowane do centrum zdarzeń lub kolejki Service Bus lub tematu w niemal czasie rzeczywistym.

Dane są eksportowane do konta magazynu raz na minutę, przy czym każdy plik zawierający partię zmian od ostatniego wyeksportowanego pliku. Eksportowane dane są umieszczane w trzech folderach w formacie JSON. Domyślne ścieżki na koncie magazynu:

- Dane telemetryczne: _{Container}/{App-ID}/Telemetry/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Urządzenia: _{Container}/{App-ID}/Devices/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Szablony urządzeń: _{Container}/{App-ID}/deviceTemplates/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

Wyeksportowane pliki można przeglądać w Azure Portal, przechodząc do pliku i wybierając kartę **Edytuj obiekt BLOB** .

Wyeksportowane dane telemetryczne zawierają w całości komunikat wysyłany przez urządzenia do IoT Central, a nie tylko wartości telemetrii. Dane eksportowanych urządzeń zawierają zmiany właściwości i metadanych wszystkich urządzeń, a wyeksportowane szablony urządzeń zawierają zmiany we wszystkich szablonach urządzeń. Eksportowane dane są we właściwości Body i są w formacie JSON.

### <a name="telemetry"></a>Telemetria

Nowy komunikat jest eksportowany szybko po IoT Central otrzyma komunikat z urządzenia.

- Każdy wyeksportowany komunikat w Event Hubs i Service Bus zawiera pełną wiadomość wysłaną przez urządzenie we właściwości Body w formacie JSON.
- Wyeksportowane pliki w usłudze BLOB Storage używają tego samego formatu co pliki komunikatów wyeksportowane przez [IoT Hub Routing komunikatów](../../iot-hub/iot-hub-csharp-csharp-process-d2c.md) do magazynu obiektów BLOB. Upewnij się, że urządzenia wysyłają komunikaty z `contentType: application/JSON` i `contentEncoding:utf-8` (lub `utf-16``utf-32`). Przykład można znaleźć w [dokumentacji IoT Hub](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) .

Urządzenia wysyłające dane telemetryczne są reprezentowane przez identyfikatory urządzeń (zobacz następujące sekcje). Aby uzyskać nazwy urządzeń, wyeksportuj dane urządzenia i skorelowania poszczególnych komunikatów przy użyciu **connectionDeviceId** , który odpowiada identyfikatorowi **deviceId** komunikatu urządzenia.

Poniższy przykład przedstawia komunikat dotyczący danych telemetrycznych odebranych w centrum zdarzeń lub Service Bus kolejki lub tematu.

```json
{
  "body":{
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

Poniższy przykład przedstawia rekord w formacie JSON w usłudze BLOB Storage:

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

### <a name="devices"></a>Urządzenia

Komunikaty zawierające dane urządzenia są wysyłane do centrum zdarzeń lub kolejki Service Bus lub tematu co kilka minut. Nowa migawka jest zapisywana do magazynu obiektów BLOB raz na minutę. Każdy komunikat lub migawka zawiera dane dotyczące:

- Nowe urządzenia, które zostały dodane
- Urządzenia ze zmienionymi wartościami właściwości

Każdy komunikat lub rekord w migawce reprezentuje co najmniej jedną zmianę urządzenia od ostatniego wyeksportowanego komunikatu. Informacje obejmują:

- `@id` urządzenia w IoT Central
- `name` urządzenia
- `deviceId` z [usługi Device Provisioning](/azure/iot-central/core/howto-connect-nodejs)
- Informacje o szablonie urządzenia
- Wartości właściwości

Urządzenia usunięte od ostatniej partii nie są eksportowane. Obecnie nie ma żadnych wskaźników w wyeksportowanych komunikatach dotyczących usuniętych urządzeń.

Szablon urządzenia, do którego należy każde urządzenie, jest reprezentowany przez identyfikator szablonu urządzenia. Aby uzyskać nazwę szablonu urządzenia, należy również wyeksportować dane szablonu urządzenia.

Poniższy przykład przedstawia komunikat dotyczący danych urządzenia w centrum zdarzeń lub Service Bus kolejki lub tematu:

```json
{
  "body":{
    "@id":"<id>",
    "@type":"Device",
    "displayName":"Airbox - 266d30aedn5",
    "data":{
      "$cloudProperties":{
        "Color":"blue"
      },
      "EnvironmentalSensor":{
        "thsensormodel":{
          "reported":{
            "value":"A1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "pm25sensormodel":{
          "reported":{
            "value":"P1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      },
      "urn_azureiot_DeviceManagement_DeviceInformation":{
        "totalStorage":{
          "reported":{
            "value":3088.1959855710156,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "totalMemory":{
          "reported":{
            "value":16005.703586477555,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      }
    },
    "instanceOf":"<templateId>",
    "deviceId":"<deviceId>",
    "simulated":true
  },
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2019-10-02T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Eksportowane pliki zawierają jeden wiersz dla każdego rekordu. Poniższy przykład przedstawia rekord w formacie JSON.

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates"></a>Szablony urządzeń

Komunikaty zawierające szablony urządzeń są wysyłane do centrum zdarzeń lub kolejki Service Bus lub tematu co kilka minut. Nowa migawka jest zapisywana raz na minutę do magazynu obiektów BLOB. W związku z tym, co kilka minut, partia komunikatów dociera do danych:

Komunikaty zawierające dane szablonu urządzenia są wysyłane do centrum zdarzeń lub kolejki Service Bus lub tematu co kilka minut. Nowa migawka jest zapisywana do magazynu obiektów BLOB raz na minutę. Każdy komunikat lub migawka zawiera dane dotyczące:

- Nowe szablony urządzeń dodane lub z wersjami
- Szablony urządzeń ze zmienionymi modelami możliwości, właściwościami chmury, zastąpieniami i wartościami początkowymi

Każdy rekord wiadomości lub migawki reprezentuje co najmniej jedną zmianę w szablonie urządzenia od ostatniego wyeksportowanego komunikatu. Informacje wysyłane w każdym komunikacie lub rekordzie obejmują:

- `@id` szablonu urządzenia
- `name` szablonu urządzenia
- `version` szablonu urządzenia
- Urządzenie `capabilityModel`, w tym `interfaces`, oraz dane telemetryczne, właściwości i poleceń
- definicje `cloudProperties`
- Przesłonięcia i początkowe wartości, wbudowane przy użyciu `capabilityModel`

Szablony urządzeń usunięte od momentu ostatniej partii nie są eksportowane. Obecnie nie ma żadnych wskaźników w wyeksportowanych komunikatach dla usuniętych szablonów urządzeń.

Poniższy przykład przedstawia komunikat szablonu urządzenia w centrum zdarzeń lub Service Bus kolejki lub tematu:

```json
{
  "body":{
    "@id":"<template-id>",
    "@type":"DeviceModelDefinition",
    "displayName":"Airbox",
    "capabilityModel":{
      "@id":"<id>",
      "@type":"CapabilityModel",
      "implements":[
        {
          "@id":"<id>",
          "@type":"InterfaceInstance",
          "name":"EnvironmentalSensor",
          "schema":{
            "@id":"<id>",
            "@type":"Interface",
            "comment":"Requires temperature and humidity sensors.",
            "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
            "displayName":"Environmental Sensor",
            "contents":[
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current temperature on the device",
                "displayName":"Temperature",
                "name":"temp",
                "schema":"double",
                "unit":"Units/Temperature/celsius",
                "valueDetail":{
                  "@id":"<id>",
                  "@type":"ValueDetail/NumberValueDetail",
                  "minValue":{
                    "@value":"50"
                  }
                },
                "visualizationDetail":{
                  "@id":"<id>",
                  "@type":"VisualizationDetail"
                }
              },
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current humidity on the device",
                "displayName":"Humidity",
                "name":"humid",
                "schema":"integer"
              },
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current PM2.5 on the device",
                "displayName":"PM2.5",
                "name":"pm25",
                "schema":"integer"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "description":"T&H Sensor Model Name",
                "displayName":"T&H Sensor Model",
                "name":"thsensormodel",
                "schema":"string"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "description":"PM2.5 Sensor Model Name",
                "displayName":"PM2.5 Sensor Model",
                "name":"pm25sensormodel",
                "schema":"string"
              }
            ]
          }
        },
        {
          "@id":"<id>",
          "@type":"InterfaceInstance",
          "name":"urn_azureiot_DeviceManagement_DeviceInformation",
          "schema":{
            "@id":"<id>",
            "@type":"Interface",
            "displayName":"Device information",
            "contents":[
              {
                "@id":"<id>",
                "@type":"Property",
                "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
                "displayName":"Total storage",
                "name":"totalStorage",
                "displayUnit":"kilobytes",
                "schema":"long"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
                "displayName":"Total memory",
                "name":"totalMemory",
                "displayUnit":"kilobytes",
                "schema":"long"
              }
            ]
          }
        }
      ],
      "displayName":"AAEONAirbox52"
    },
    "solutionModel":{
      "@id":"<id>",
      "@type":"SolutionModel",
      "cloudProperties":[
        {
          "@id":"<id>",
          "@type":"CloudProperty",
          "displayName":"Color",
          "name":"Color",
          "schema":"string",
          "valueDetail":{
            "@id":"<id>",
            "@type":"ValueDetail/StringValueDetail"
          },
          "visualizationDetail":{
            "@id":"<id>",
            "@type":"VisualizationDetail"
          }
        }
      ]
    },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Eksportowane pliki zawierają jeden wiersz dla każdego rekordu. Poniższy przykład przedstawia rekord w formacie JSON.

```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wyeksportować dane do usługi Azure Event Hubs i Azure Service Bus, przejdź do następnego kroku:

> [!div class="nextstepaction"]
> [Jak wyzwolić Azure Functions](howto-trigger-azure-functions.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
