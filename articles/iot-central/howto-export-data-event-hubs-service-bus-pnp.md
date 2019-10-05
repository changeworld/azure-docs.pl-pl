---
title: Eksportuj dane do usługi Azure Event Hubs i Azure Service Bus | Microsoft Docs
description: Jak wyeksportować dane z aplikacji IoT Central platformy Azure do usługi Azure Event Hubs i Azure Service Bus
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: fed9c924274cb66671e233a7dc6d431d81e0dbfb
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973210"
---
# <a name="export-your-data-in-azure-iot-central-preview-features"></a>Eksportowanie danych do usługi Azure IoT Central (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Ten temat ma zastosowanie do administratorów.*

W tym artykule opisano sposób używania funkcji ciągłego eksportu danych w usłudze Azure IoT Central do eksportowania danych do własnych **Event Hubs platformy Azure**i **Azure Service Bus** wystąpień. Dane **telemetryczne**, **urządzenia**i **Szablony urządzeń** można eksportować do własnych miejsc docelowych w celu przeanalizowania i analizy ścieżki ciepłej. Obejmuje to wyzwalanie reguł niestandardowych w programie Azure Stream Analytics, wyzwalanie niestandardowych przepływów pracy w programie Azure Logic Apps lub przekazywanie ich przez Azure Functions do przetworzenia.

> [!Note]
> Po włączeniu ciągłego eksportowania danych w tym momencie otrzymujesz tylko dane z tego momentu. Obecnie nie można pobrać danych przez czas, gdy ciągły eksport danych jest wyłączony. Aby zachować bardziej historyczne dane, należy wczesnie włączyć ciągły eksport danych.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz być administratorem w aplikacji IoT Central

## <a name="set-up-export-destination"></a>Skonfiguruj miejsce docelowe eksportu

Jeśli nie masz istniejącego Event Hubs/Service Bus do eksportowania do programu, wykonaj następujące kroki, aby je utworzyć:

### <a name="create-event-hubs-namespace"></a>Tworzenie przestrzeni nazw Event Hubs

1. Utwórz [nową przestrzeń nazw Event Hubs w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Więcej informacji można znaleźć w witrynie [Azure Event Hubs docs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. wybierz subskrypcję. 

    > [!Note] 
    > Teraz możesz eksportować dane do innych subskrypcji, które **nie są takie same** jak dla aplikacji z opcją płatność zgodnie z rzeczywistym użyciem IoT Central. W tym przypadku zostanie nawiązane połączenie przy użyciu parametrów połączenia.
3. Utwórz centrum zdarzeń w przestrzeni nazw Event Hubs. Przejdź do obszaru nazw, a następnie wybierz pozycję **+ centrum zdarzeń** u góry, aby utworzyć wystąpienie centrum zdarzeń.

### <a name="create-service-bus-namespace"></a>Tworzenie przestrzeni nazw Service Bus

1. Utwórz [nową przestrzeń nazw Service Bus w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . Więcej informacji można znaleźć w dokumentacji [Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. wybierz subskrypcję. 

    > [!Note] 
    > Teraz możesz eksportować dane do innych subskrypcji, które **nie są takie same** jak dla aplikacji z opcją płatność zgodnie z rzeczywistym użyciem IoT Central. W tym przypadku zostanie nawiązane połączenie przy użyciu parametrów połączenia.

3. Przejdź do przestrzeni nazw Service Bus, a następnie wybierz pozycję **+ Kolejka** lub **+ temat** u góry, aby utworzyć kolejkę lub temat do wyeksportowania.


## <a name="set-up-continuous-data-export"></a>Konfigurowanie ciągłego eksportu danych

Teraz, gdy masz miejsce docelowe Event Hubs/Service Bus do eksportowania danych do programu, wykonaj następujące kroki, aby skonfigurować ciągły eksport danych. 

1. Zaloguj się do aplikacji IoT Central.

2. W menu po lewej stronie wybierz pozycję **eksport danych**.

    > [!Note]
    > Jeśli nie widzisz eksportu danych w menu po lewej stronie, nie jesteś administratorem w swojej aplikacji. Skontaktuj się z administratorem, aby skonfigurować eksportowanie danych.

3. Wybierz przycisk **+ Nowy** w prawym górnym rogu. Wybierz jedną z **Event Hubs platformy Azure** lub **Azure Service Bus** jako lokalizację docelową eksportu. 

    > [!NOTE] 
    > Maksymalna liczba eksportów na aplikację wynosi pięć. 

    ![Utwórz nowy ciągły eksport danych](media/howto-export-data-pnp/export-new2.png)

4. W polu listy rozwijanej wybierz **Event Hubs przestrzeń nazw/Service Bus przestrzeń**nazw. Możesz również wybrać ostatnią opcję z listy, która jest **wprowadzeniem parametrów połączenia**. 

    > [!NOTE] 
    > Będą widoczne tylko konta magazynu/Event Hubs przestrzenie nazw/Service Bus przestrzenie nazw w **tej samej subskrypcji,** w której znajduje się aplikacja IoT Central. Jeśli chcesz wyeksportować do lokalizacji docelowej poza tą subskrypcją, wybierz pozycję **wprowadź parametry połączenia** i zobacz krok 5.

    > [!NOTE] 
    > W przypadku 7-dniowych aplikacji próbnych jedynym sposobem skonfigurowania ciągłego eksportu danych jest użycie parametrów połączenia. Wynika to z faktu, że 7-dniowe aplikacje próbne nie mają skojarzonej subskrypcji platformy Azure.

    ![Utwórz nowe centrum zdarzeń CDE](media/howto-export-data-pnp/export-eh.png)

5. Obowiązkowe W przypadku wybrania opcji **wprowadź parametry połączenia**pojawi się nowe pole umożliwiające wklejenie parametrów połączenia. Aby uzyskać parametry połączenia dla:
    - Event Hubs lub Service Bus, przejdź do przestrzeni nazw w Azure Portal.
        - W obszarze **Ustawienia**wybierz pozycję **zasady dostępu współdzielonego** .
        - Wybierz domyślną **RootManageSharedAccessKey** lub Utwórz nową
        - Skopiuj podstawowe lub pomocnicze parametry połączenia

6. W polu listy rozwijanej wybierz centrum zdarzeń/kolejkę lub temat.

7. W obszarze **dane do eksportowania**Określ każdy typ danych do wyeksportowania, ustawiając typ na wartość **włączone**.

8. Aby włączyć ciągły eksport danych, upewnij się, że przełącznik **eksportu danych** jest **włączony**. Wybierz pozycję **Zapisz**.

9. Po kilku minutach dane zostaną wyświetlone w wybranym miejscu docelowym.


## <a name="data-format"></a>Format danych

Dane telemetryczne, urządzenia i szablony urządzeń są eksportowane do centrum zdarzeń lub kolejki Service Bus lub tematu w niemal czasie rzeczywistym. Wyeksportowane dane telemetryczne zawierają w całości komunikat wysyłany przez urządzenia do IoT Central, a nie tylko wartości telemetrii. Dane eksportowanych urządzeń zawierają zmiany właściwości i metadanych wszystkich urządzeń, a wyeksportowane szablony urządzeń zawierają zmiany we wszystkich szablonach urządzeń. Eksportowane dane są we właściwości "Body" i są w formacie JSON.

> [!NOTE]
> W przypadku wybrania Service Bus jako miejsca docelowego eksportu kolejki i tematy **nie mogą mieć włączonej sesji lub Wykrywanie duplikatów**. Jeśli jedna z tych opcji jest włączona, niektóre komunikaty nie docierają do kolejki lub tematu.

### <a name="telemetry"></a>Telemetria

Nowy komunikat jest eksportowany szybko po IoT Central otrzyma komunikat z urządzenia. Każdy wyeksportowany komunikat w Event Hubs i Service Bus zawiera pełną wiadomość wysłaną przez urządzenie we właściwości "Body" w formacie JSON. 

> [!NOTE]
> Urządzenia wysyłające dane telemetryczne są reprezentowane przez identyfikatory urządzeń (zobacz następujące sekcje). Aby uzyskać nazwy urządzeń, wyeksportuj dane urządzenia i skorelowania poszczególnych komunikatów przy użyciu **connectionDeviceId** , który odpowiada identyfikatorowi **deviceId** komunikatu urządzenia.

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

### <a name="devices"></a>Urządzenia

Komunikaty zawierające dane urządzenia są wysyłane do centrum zdarzeń lub kolejki Service Bus lub tematu co kilka minut. Oznacza to, że co kilka minut zostanie odebrana partia komunikatów z danymi dotyczącymi
- Nowe urządzenia, które zostały dodane
- Urządzenia ze zmienionymi wartościami właściwości

Każdy komunikat reprezentuje co najmniej jedną zmianę urządzenia od ostatniego wyeksportowanego komunikatu. Informacje wysyłane w poszczególnych wiadomościach obejmują:
- `@id` urządzenia w IoT Central
- `name` urządzenia
- `deviceId` z [usługi Device Provisioning](https://aka.ms/iotcentraldocsdps)
- Informacje o szablonie urządzenia
- Wartości właściwości

> [!NOTE]
> Urządzenia usunięte od ostatniej partii nie są eksportowane. Obecnie nie ma żadnych wskaźników w wyeksportowanych komunikatach dotyczących usuniętych urządzeń.
>
> Szablon urządzenia, do którego należy każde urządzenie, jest reprezentowany przez identyfikator szablonu urządzenia. Aby uzyskać nazwę szablonu urządzenia, należy również wyeksportować dane szablonu urządzenia.

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

### <a name="device-templates"></a>Szablony urządzeń

Komunikaty zawierające szablony urządzeń są wysyłane do centrum zdarzeń lub kolejki Service Bus lub tematu co kilka minut. Oznacza to, że co kilka minut zostanie odebrana partia komunikatów z danymi dotyczącymi
- Nowe szablony urządzeń dodane lub z wersjami
- Szablony urządzeń ze zmienionymi wartościami capabilityModels, cloudProperties, Overrides i Initials

Każdy komunikat reprezentuje co najmniej jedną zmianę w szablonie urządzenia od ostatniego wyeksportowanego komunikatu. Informacje wysyłane w poszczególnych wiadomościach obejmują:
- `@id` szablonu urządzenia
- `name` szablonu urządzenia
- `version` szablonu urządzenia
- Urządzenie `capabilityModel`, w tym jego `interfaces` oraz definicje danych telemetrycznych, właściwości i poleceń
- definicje `cloudProperties`
- Przesłonięcia i początkowe wartości, wbudowane przy `capabilityModel`

> [!NOTE]
> Szablony urządzeń usunięte od momentu ostatniej partii nie są eksportowane. Obecnie nie ma żadnych wskaźników w wyeksportowanych komunikatach dla usuniętych szablonów urządzeń.

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

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wyeksportować dane do usługi Azure Event Hubs i Azure Service Bus, przejdź do następnego kroku:

> [!div class="nextstepaction"]
> [Jak wyzwolić Azure Functions](howto-trigger-azure-functions.md)
