---
title: Eksportowanie danych do usługi Azure Blob Storage | Microsoft Docs
description: Jak wyeksportować dane z aplikacji IoT Central platformy Azure do platformy Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 9ac650273a53da715b89e3233b30cf50710cfcfd
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973267"
---
# <a name="export-your-data-to-azure-blob-storage-preview-features"></a>Eksportowanie danych do usługi Azure Blob Storage (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Ten temat ma zastosowanie do administratorów.*

W tym artykule opisano sposób korzystania z funkcji ciągłego eksportu danych w usłudze Azure IoT Central w celu okresowego eksportowania danych do **konta usługi Azure Blob Storage** lub **konta magazynu Azure Data Lake Storage Gen2**. Dane **telemetryczne**, **urządzenia**i **Szablony urządzeń** można eksportować do plików w formacie JSON. Wyeksportowane dane mogą służyć do analizy ścieżki zimnej, takiej jak modele szkoleniowe w Azure Machine Learning lub długoterminowej analizie trendów w programie Microsoft Power BI.

> [!Note]
> Po włączeniu ciągłego eksportowania danych otrzymujesz tylko dane z tego momentu. Obecnie nie można pobrać danych przez czas, gdy ciągły eksport danych jest wyłączony. Aby zachować bardziej historyczne dane, należy wczesnie włączyć ciągły eksport danych.


## <a name="prerequisites"></a>Wymagania wstępne

- Musisz być administratorem w aplikacji IoT Central

## <a name="create-storage-account"></a>Tworzenie konta magazynu
Jeśli nie masz istniejącego magazynu do eksportowania, wykonaj następujące kroki:

1. Utwórz [nowe konto magazynu w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Możesz dowiedzieć się więcej na temat tworzenia nowych [kont usługi Azure Blob Storage](https://aka.ms/blobdocscreatestorageaccount) lub [kont magazynu Azure Data Lake Storage v2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

    > [!Note] 
    > Jeśli zdecydujesz się wyeksportować dane do konta magazynu ADLS v2, musisz wybrać **rodzaj konta** jako **BlobStorage**. 

    > [!Note] 
    > Dane można eksportować do kont magazynu w ramach subskrypcji innych niż te dla aplikacji z opcją płatność zgodnie z rzeczywistym użyciem IoT Central. W tym przypadku zostanie nawiązane połączenie przy użyciu parametrów połączenia.

2. Utwórz kontener na koncie magazynu. Przejdź do swojego konta magazynu. W obszarze **BLOB Service**wybierz pozycję **Przeglądaj obiekty blob**. Wybierz pozycję **+ kontener** u góry, aby utworzyć nowy kontener.


## <a name="set-up-continuous-data-export"></a>Konfigurowanie ciągłego eksportu danych

Teraz, gdy masz miejsce docelowe magazynu, do którego chcesz wyeksportować dane, wykonaj następujące kroki, aby skonfigurować ciągły eksport danych. 

1. Zaloguj się do aplikacji IoT Central.

2. W menu po lewej stronie wybierz pozycję **eksport danych**.

    > [!Note]
    > Jeśli nie widzisz eksportu danych w menu po lewej stronie, nie jesteś administratorem w swojej aplikacji. Skontaktuj się z administratorem, aby skonfigurować eksportowanie danych.

3. Wybierz przycisk **+ Nowy** w prawym górnym rogu. Wybierz pozycję **Azure Blob Storage** jako lokalizację docelową eksportu. 

    > [!NOTE] 
    > Maksymalna liczba eksportów na aplikację wynosi pięć. 

    ![Utwórz nowy ciągły eksport danych](media/howto-export-data-pnp/export-new2.png)

4. W polu listy rozwijanej wybierz **przestrzeń nazw konta magazynu**. Możesz również wybrać ostatnią opcję z listy, która jest **wprowadzeniem parametrów połączenia**. 

    > [!NOTE] 
    > W **tej samej subskrypcji,** w której znajduje się aplikacja IoT Central, zobaczysz tylko przestrzenie nazw kont magazynu. Jeśli chcesz wyeksportować do lokalizacji docelowej poza tą subskrypcją, wybierz pozycję **wprowadź parametry połączenia** i zobacz krok 5.

    > [!NOTE] 
    > W przypadku 7-dniowych aplikacji próbnych jedynym sposobem skonfigurowania ciągłego eksportu danych jest użycie parametrów połączenia. Wynika to z faktu, że 7-dniowe aplikacje próbne nie mają skojarzonej subskrypcji platformy Azure.

    ![Utwórz nowy eksport do obiektu BLOB](media/howto-export-data-pnp/export-create-blob2.png)

5. Obowiązkowe W przypadku wybrania opcji **wprowadź parametry połączenia**pojawi się nowe pole umożliwiające wklejenie parametrów połączenia. Aby uzyskać parametry połączenia dla konta magazynu, przejdź do konta magazynu w Azure Portal:
    - W obszarze **Ustawienia**wybierz pozycję **klucze dostępu** .
    - Skopiuj parametry połączenia Klucz1 lub parametry połączenia klucz2
 
6. Wybierz kontener z listy rozwijanej. Jeśli nie masz kontenera, przejdź do swojego konta magazynu w Azure Portal:
    - W obszarze **BLOB Service**wybierz pozycję **obiekty blob**. Kliknij pozycję **+ kontener** i nadaj kontenerowi nazwę. Wybierz publiczny poziom dostępu do danych (wszystkie będą współpracować z ciągłym eksportowaniem danych). Dowiedz się więcej z dokumentacji [usługi Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

7.  W obszarze **dane do eksportowania**Określ każdy typ danych do wyeksportowania, ustawiając typ na wartość **włączone**.
   
    > [!NOTE] 
    > Dane są eksportowane w formacie JSON.

8. Aby włączyć ciągły eksport danych, upewnij się, że przełącznik **eksportu danych** jest włączony. Wybierz pozycję **Zapisz**.

   ![Konfigurowanie ciągłego eksportowania danych](media/howto-export-data-pnp/export-list-blob2.png)

9. Po kilku minutach dane zostaną wyświetlone na koncie magazynu.


## <a name="path-structure"></a>Struktura ścieżki

Dane telemetryczne, urządzenia i szablony urządzeń są eksportowane do konta magazynu raz na minutę, przy czym każdy plik zawierający partię zmian od ostatniego wyeksportowanego pliku. Eksportowane dane są umieszczane w trzech folderach w formacie JSON. Domyślne ścieżki na koncie magazynu:
- Dane telemetryczne: {Container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Urządzenia: {Container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Szablony urządzeń: {Container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

Wyeksportowane pliki można przeglądać w Azure Portal, przechodząc do pliku i wybierając kartę **Edytuj obiekt BLOB** .

## <a name="data-format"></a>Format danych
### <a name="telemetry"></a>Telemetria

Wyeksportowane dane telemetryczne zawierają wszystkie nowe komunikaty odebrane przez IoT Central ze wszystkich urządzeń w tym czasie. Eksportowane pliki używają tego samego formatu co pliki komunikatów wyeksportowane przez [IoT Hub Routing komunikatów](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) do magazynu obiektów BLOB.

> [!NOTE]
> Upewnij się, że urządzenia wysyłają komunikaty z `contentType: application/JSON` i `contentEncoding:utf-8` (lub `utf-16`, `utf-32`). Przykład można znaleźć w [dokumentacji IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body) .

> [!NOTE]
> Urządzenia wysyłające dane telemetryczne są reprezentowane przez identyfikatory urządzeń (zobacz następujące sekcje). Aby uzyskać nazwy urządzeń, wyeksportuj migawki urządzeń. Należy skorelować każdy rekord komunikatu przy użyciu **connectionDeviceId** , który odpowiada identyfikatorowi **deviceId** rekordu urządzenia.

Poniższy przykład przedstawia rekord w formacie JSON.

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

Gdy ciągły eksport danych jest najpierw włączony, wyeksportowana jest pojedyncza migawka ze wszystkimi urządzeniami. Każde urządzenie obejmuje:
- `@id` urządzenia w IoT Central
- `name` urządzenia
- `deviceId` z [usługi Device Provisioning](https://aka.ms/iotcentraldocsdps)
- Informacje o szablonie urządzenia
- Wartości właściwości

Nowa migawka jest zapisywana raz na minutę. Migawka obejmuje następujące:

- Dodano nowe urządzenia od ostatniej migawki.
- Urządzenia ze zmienionymi wartościami właściwości od momentu ostatniej migawki.

> [!NOTE]
> Urządzenia usunięte od ostatniej migawki nie są eksportowane. Obecnie migawki nie mają wskaźników dla usuniętych urządzeń.
>
> Szablon urządzenia, do którego należy każde urządzenie, jest reprezentowany przez pole `instanceOf`. Aby uzyskać nazwę szablonu urządzenia, wyeksportuj migawki szablonów urządzeń.

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

Gdy ciągły eksport danych jest najpierw włączony, wyeksportowana jest pojedyncza migawka ze wszystkimi szablonami urządzeń. Każdy szablon urządzenia zawiera:
- `@id` szablonu urządzenia
- `name` szablonu urządzenia
- `version` szablonu urządzenia
- Urządzenie `capabilityModel`, w tym jego `interfaces` oraz definicje danych telemetrycznych, właściwości i poleceń
- definicje `cloudProperties`
- Przesłonięcia i początkowe wartości, wbudowane przy `capabilityModel`

Nowa migawka jest zapisywana raz na minutę. Migawka obejmuje następujące:

- Dodano nowe szablony urządzeń od ostatniej migawki. Obejmuje to nowe wersje szablonów urządzeń.
- Szablony urządzeń ze zmienionymi zastąpieniami, wartościami początkowymi i definicjami właściwości chmury od momentu ostatniej migawki.

> [!NOTE]
> Szablony urządzeń usunięte od momentu ostatniej migawki nie są eksportowane. Obecnie migawki nie mają wskaźników dla usuniętych szablonów urządzeń.

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

Teraz, gdy wiesz już, jak wyeksportować dane, przejdź do następnego kroku:

> [!div class="nextstepaction"]
> [Jak połączyć się z innymi chmurami IoT przy użyciu mostka urządzenia IoT Central](howto-build-iotc-device-bridge.md)
