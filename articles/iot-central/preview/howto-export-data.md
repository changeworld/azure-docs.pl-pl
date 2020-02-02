---
title: Eksportowanie danych usługi Azure IoT Central | Microsoft Docs
description: Jak wyeksportować dane z aplikacji IoT Central platformy Azure do usługi Azure Event Hubs, Azure Service Bus i Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 612db9963b02e905c3a48d61a4f7a7ed6f832fba
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939013"
---
# <a name="export-your-azure-iot-central-data-preview-features"></a>Eksportowanie danych usługi Azure IoT Central (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Ten temat ma zastosowanie do administratorów.*

W tym artykule opisano sposób używania funkcji ciągłego eksportu danych w usłudze Azure IoT Central do eksportowania danych do wystąpień **usługi azure Event Hubs**, **Azure Service Bus**lub **Azure Blob Storage** . Dane są eksportowane w formacie JSON i mogą zawierać informacje telemetryczne, informacje o urządzeniu i szablon urządzenia. Użyj wyeksportowanych danych dla:

- Szczegółowe informacje i analiza z ciepłą ścieżką. Ta opcja obejmuje wyzwalanie reguł niestandardowych w programie Azure Stream Analytics, wyzwalanie niestandardowych przepływów pracy w programie Azure Logic Apps lub przekazywanie ich przez Azure Functions do przetworzenia.
- Analiza zimnej ścieżki, na przykład modele szkoleniowe, w Azure Machine Learning lub długoterminowej analizie trendów w programie Microsoft Power BI.

> [!Note]
> Po włączeniu ciągłego eksportowania danych otrzymujesz tylko dane z tego momentu. Obecnie nie można pobrać danych przez czas, gdy ciągły eksport danych jest wyłączony. Aby zachować bardziej historyczne dane, należy wczesnie włączyć ciągły eksport danych.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz być administratorem w aplikacji IoT Central lub mieć uprawnienia do eksportowania danych.

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
    > Jeśli nie widzisz eksportu danych w okienku po lewej stronie, nie masz uprawnień do konfigurowania eksportu danych w aplikacji. Skontaktuj się z administratorem, aby skonfigurować eksportowanie danych.

3. Wybierz przycisk **+ Nowy** w prawym górnym rogu. Wybierz jedną z **Event Hubs platformy Azure**, **Azure Service Bus**lub **Azure Blob Storage** jako lokalizację docelową eksportu. Maksymalna liczba eksportów dla aplikacji wynosi pięć.

    ![Utwórz nowy ciągły eksport danych](media/howto-export-data/export-new2.png)

4. W polu listy rozwijanej wybierz **przestrzeń nazw Event Hubs**, **Service Bus przestrzeń nazw**, **przestrzeń nazw konta magazynu**lub **wprowadź parametry połączenia**.

    - Widoczne są tylko konta magazynu, Event Hubs przestrzenie nazw i Service Bus przestrzenie nazw w ramach tej samej subskrypcji, w której znajduje się aplikacja IoT Central. Jeśli chcesz wyeksportować do lokalizacji docelowej poza tą subskrypcją, wybierz pozycję **wprowadź parametry połączenia** i zobacz krok 5.
    - W przypadku siedmiu-dniowych wersji próbnych aplikacji jedynym sposobem skonfigurowania ciągłego eksportu danych jest użycie parametrów połączenia. Siedem-dniowe aplikacje próbne nie mają skojarzonej subskrypcji platformy Azure.

    ![Utwórz nowe centrum zdarzeń](media/howto-export-data/export-eh.png)

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

8. Aby włączyć funkcję ciągłego eksportowania danych, upewnij się, że **włączony** przełącznik **jest włączony**. Wybierz pozycję **Zapisz**.

9. Po kilku minutach dane zostaną wyświetlone w wybranym miejscu docelowym.

## <a name="export-contents-and-format"></a>Eksportuj zawartość i format

Wyeksportowane dane telemetryczne zawierają w całości komunikat wysyłany przez urządzenia do IoT Central, a nie tylko wartości telemetrii. Dane eksportowanych urządzeń zawierają zmiany właściwości i metadanych wszystkich urządzeń, a wyeksportowane szablony urządzeń zawierają zmiany we wszystkich szablonach urządzeń.

W przypadku Event Hubs i Service Bus dane są eksportowane niemal w czasie rzeczywistym. Dane znajdują się we właściwości Body i są w formacie JSON (poniżej przedstawiono przykłady).

W przypadku Blob Storage dane są eksportowane raz na minutę, przy czym każdy plik zawierający partię zmian od ostatniego wyeksportowanego pliku. Eksportowane dane są umieszczane w trzech folderach w formacie JSON. Domyślne ścieżki na koncie magazynu:

- Dane telemetryczne: _{Container}/{App-ID}/Telemetry/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Urządzenia: _{Container}/{App-ID}/Devices/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Szablony urządzeń: _{Container}/{App-ID}/deviceTemplates/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

Wyeksportowane pliki można przeglądać w Azure Portal, przechodząc do pliku i wybierając kartę **Edytuj obiekt BLOB** .


## <a name="telemetry"></a>Telemetria

W przypadku Event Hubs i Service Bus nowy komunikat jest eksportowany szybko po IoT Central otrzyma komunikat z urządzenia, a każdy wyeksportowany komunikat zawiera pełną wiadomość wysłaną przez urządzenie we właściwości Body w formacie JSON.

W przypadku Blob Storage komunikaty są wsadowe i eksportowane raz na minutę. Eksportowane pliki używają tego samego formatu co pliki komunikatów wyeksportowane przez [IoT Hub Routing komunikatów](../../iot-hub/iot-hub-csharp-csharp-process-d2c.md) do magazynu obiektów BLOB. 

> [!NOTE]
> W przypadku Blob Storage upewnij się, że urządzenia wysyłają komunikaty z `contentType: application/JSON` i `contentEncoding:utf-8` (lub `utf-16`, `utf-32`). Przykład można znaleźć w [dokumentacji IoT Hub](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) .

Urządzenie, które wysłało dane telemetryczne, jest reprezentowane przez identyfikator urządzenia (zobacz następujące sekcje). Aby uzyskać nazwy urządzeń, wyeksportuj dane urządzenia i skorelowania poszczególnych komunikatów przy użyciu **connectionDeviceId** , który odpowiada identyfikatorowi **deviceId** komunikatu urządzenia.

Jest to przykładowy komunikat odebrany w centrum zdarzeń lub Service Bus kolejki lub temacie.

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

To jest przykładowy rekord wyeksportowany do magazynu obiektów blob:

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

## <a name="devices"></a>Urządzenia

Każdy komunikat lub rekord w migawce reprezentuje jedną lub więcej zmian na urządzeniu oraz jego właściwości i urządzenia oraz w chmurze od ostatniego wyeksportowanego komunikatu. Obejmuje to:

- `id` urządzenia w IoT Central
- `displayName` urządzenia
- Identyfikator szablonu urządzenia w `instanceOf`
- Flaga `simulated`, prawda, jeśli urządzenie jest urządzeniem symulowanym
- Flaga `provisioned`, prawda, jeśli urządzenie zostało zainicjowane
- Flaga `approved`, prawda, jeśli urządzenie zostało zatwierdzone do wysyłania danych
- Wartości właściwości
- `properties` z uwzględnieniem wartości właściwości urządzenia i chmury

Usunięte urządzenia nie są eksportowane. Obecnie nie ma żadnych wskaźników w wyeksportowanych komunikatach dotyczących usuniętych urządzeń.

W przypadku Event Hubs i Service Bus komunikaty zawierające dane urządzenia są wysyłane do centrum zdarzeń lub kolejki Service Bus lub tematu niemal w czasie rzeczywistym, ponieważ pojawia się on w IoT Central. 

W przypadku Blob Storage Nowa migawka zawierająca wszystkie zmiany od ostatniego zapisu zostanie wyeksportowana raz na minutę.

Jest to przykładowy komunikat dotyczący urządzeń i danych właściwości w centrum zdarzeń lub Service Bus kolejki lub temacie:

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
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
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Jest to przykładowa migawka zawierająca dane dotyczące urządzeń i właściwości w Blob Storage. Eksportowane pliki zawierają jeden wiersz dla każdego rekordu.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>Szablony urządzeń

Każdy rekord wiadomości lub migawki reprezentuje co najmniej jedną zmianę w opublikowanym szablonie urządzenia od ostatniego wyeksportowanego komunikatu. Informacje wysyłane w każdym komunikacie lub rekordzie obejmują:

- `id` szablonu urządzenia, który odpowiada `instanceOf` powyżej strumienia urządzeń
- `displayName` szablonu urządzenia
- Urządzenie `capabilityModel`, w tym `interfaces`, oraz dane telemetryczne, właściwości i poleceń
- definicje `cloudProperties`
- Przesłonięcia i początkowe wartości, wbudowane przy użyciu `capabilityModel`

Usunięte szablony urządzeń nie są eksportowane. Obecnie nie ma żadnych wskaźników w wyeksportowanych komunikatach dla usuniętych szablonów urządzeń.

W przypadku Event Hubs i Service Bus komunikaty zawierające dane szablonu urządzenia są wysyłane do centrum zdarzeń lub kolejki Service Bus lub tematu niemal w czasie rzeczywistym, tak jak pojawiają się w IoT Central. 

W przypadku Blob Storage Nowa migawka zawierająca wszystkie zmiany od ostatniego zapisu zostanie wyeksportowana raz na minutę.

Jest to przykładowy komunikat dotyczący danych szablonów urządzeń w centrum zdarzeń lub Service Bus kolejki lub tematu:

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
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

Jest to przykładowa migawka zawierająca dane dotyczące urządzeń i właściwości w Blob Storage. Eksportowane pliki zawierają jeden wiersz dla każdego rekordu.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```
## <a name="data-format-change-notice"></a>Powiadomienie o zmianie formatu danych

> [!Note]
> Ta zmiana nie ma wpływ na format danych strumienia telemetrii. Dotyczy tylko strumieni danych urządzeń i urządzeń.

Jeśli masz istniejący eksport danych w aplikacji w wersji zapoznawczej przy włączonym strumieniu *urządzeń* i *szablonów urządzeń* , musisz zaktualizować eksport do **30 czerwca 2020**. Dotyczy to eksportów do Blob Storage platformy Azure, Event Hubs platformy Azure i Azure Service Bus.

Od 3 lutego 2020 wszystkie nowe Eksporty w aplikacjach z włączonymi urządzeniami i szablonami urządzeń będą miały format danych opisany powyżej. Wszystkie eksporty utworzone przed tą datą będą pozostawały w starym formacie danych do 30 czerwca 2020, po upływie tego czasu te eksporty zostaną automatycznie zmigrowane do nowego formatu danych. Nowy format danych pasuje do [urządzenia](https://docs.microsoft.com/rest/api/iotcentral/devices/get), [Właściwości urządzenia](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties), [właściwości chmury urządzenia](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties) i obiektów [szablonu urządzenia](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) w IoT Central publicznym interfejsie API. 
 
W przypadku **urządzeń**istotne różnice między starym formatem danych a nowym formatem danych obejmują:
- `@id` dla urządzenia zostanie usunięta, nazwa `deviceId` zostanie zmieniona na `id` 
- dodano flagę `provisioned` w celu opisania stanu aprowizacji urządzenia
- dodano flagę `approved` w celu opisania stanu zatwierdzenia urządzenia
- `properties`, w tym urządzenia i właściwości chmury, dopasowuje jednostki w publicznym interfejsie API

W przypadku **szablonów urządzeń**istotne różnice między starym formatem danych a nowym formatem danych obejmują:

- Nazwa `@id` szablonu urządzenia została zmieniona na `id`
- `@type` dla szablonu urządzenia zmieniono nazwę na `types`i jest teraz tablicą

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Urządzenia (format jest przestarzały z 3 lutego 2020)
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

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Szablony urządzeń (format jest przestarzały z 3 lutego 2020)
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

Teraz, gdy wiesz już, jak wyeksportować dane do usługi Azure Event Hubs, Azure Service Bus i Blob Storage platformy Azure, przejdź do następnego kroku:

> [!div class="nextstepaction"]
> [Jak wyzwolić Azure Functions](../core/howto-trigger-azure-functions.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
