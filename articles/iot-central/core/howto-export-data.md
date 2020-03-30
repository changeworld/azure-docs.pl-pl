---
title: Eksportowanie danych usługi Azure IoT Central | Dokumenty firmy Microsoft
description: Jak eksportować dane z aplikacji Azure IoT Central do usługi Azure Event Hubs, usługi Azure Service Bus i usługi Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 725c5acf961fffb1fd4cf9bc17e37a5940f871cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157912"
---
# <a name="export-iot-data-to-destinations-in-azure"></a>Eksportowanie danych IoT do miejsc docelowych na platformie Azure

*Ten temat dotyczy administratorów.*

W tym artykule opisano, jak używać funkcji ciągłego eksportowania danych w usłudze Azure IoT Central do eksportowania danych do **wystąpień usługi Azure Event Hubs,** **usługi Azure Service Bus**lub Azure **Blob storage.** Dane są eksportowane w formacie JSON i mogą zawierać dane telemetryczne, informacje o urządzeniu i informacje o szablonie urządzenia. Wyeksportowane dane można wykorzystać do:

- Ciepłe ścieżki spostrzeżenia i analizy. Ta opcja obejmuje wyzwalanie reguł niestandardowych w usłudze Azure Stream Analytics, wyzwalanie niestandardowych przepływów pracy w usłudze Azure Logic Apps lub przekazywanie ich za pośrednictwem usługi Azure Functions w celu przekształcenia.
- Analizy na zimno, takie jak modele szkoleniowe w usłudze Azure Machine Learning lub długoterminowa analiza trendów w usłudze Microsoft Power BI.

> [!Note]
> Po włączeniu ciągłego eksportowania danych od tego momentu otrzymasz tylko te dane. Obecnie danych nie można pobrać przez czas, gdy ciągły eksport danych był wyłączony. Aby zachować więcej danych historycznych, włącz ciągły eksport danych wcześniej.

## <a name="prerequisites"></a>Wymagania wstępne

Użytkownik musi być administratorem aplikacji IoT Central lub mieć uprawnienia do eksportowania danych.

## <a name="set-up-export-destination"></a>Konfigurowanie miejsca docelowego eksportu

Miejsce docelowe eksportu musi istnieć przed skonfigurowaniem ciągłego eksportowania danych.

### <a name="create-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Jeśli nie masz istniejącego obszaru nazw Centrum zdarzeń do wyeksportowania, wykonaj następujące kroki:

1. Utwórz [nowy obszar nazw Centrum zdarzeń w witrynie Azure portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Więcej informacji można uzyskać w [docs usługi Azure Event Hubs](../../event-hubs/event-hubs-create.md).

2. wybierz subskrypcję. Można wyeksportować dane do innych subskrypcji, które nie są w tej samej subskrypcji co aplikacja IoT Central. W tym przypadku można połączyć się przy użyciu ciągu połączenia.

3. Utwórz centrum zdarzeń w obszarze nazw Centrum zdarzeń. Przejdź do obszaru nazw i wybierz **+ Centrum zdarzeń** u góry, aby utworzyć wystąpienie centrum zdarzeń.

### <a name="create-service-bus-namespace"></a>Tworzenie obszaru nazw usługi Service Bus

Jeśli nie masz istniejącego obszaru nazw usługi Service Bus do wyeksportowania, wykonaj następujące kroki:

1. Utwórz [nowy obszar nazw usługi Service Bus w portalu Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Więcej informacji można uzyskać w [docs usługi Azure Service Bus](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. wybierz subskrypcję. Można wyeksportować dane do innych subskrypcji, które nie są w tej samej subskrypcji co aplikacja IoT Central. W tym przypadku można połączyć się przy użyciu ciągu połączenia.

3. Przejdź do obszaru nazw usługi Service Bus i wybierz **+ Kolejka** lub **+ Temat** u góry, aby utworzyć kolejkę lub temat do wyeksportowania.

Po wybraniu usługi Service Bus jako miejsca docelowego eksportu kolejki i tematy nie mogą mieć włączonych sesji ani wykrywania duplikatów. Jeśli którakolwiek z tych opcji jest włączona, niektóre wiadomości nie pojawią się w kolejce lub temacie.

### <a name="create-storage-account"></a>Tworzenie konta magazynu

Jeśli nie masz istniejącego konta usługi Azure Storage do wyeksportowania, wykonaj następujące kroki:

1. Utwórz [nowe konto magazynu w witrynie Azure portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Możesz dowiedzieć się więcej o tworzeniu nowych [kont usługi Azure Blob Storage](https://aka.ms/blobdocscreatestorageaccount) lub kont magazynu usługi Azure Data Lake Storage w wersji [2.](../../storage/blobs/data-lake-storage-quickstart-create-account.md) Eksport danych można zapisywać tylko dane do kont magazynu, które obsługują blokowe obiekty blob. Poniżej znajduje się lista znanych zgodnych typów kont magazynu: 

    |Warstwa wydajności|Typ konta|
    |-|-|
    |Standardowa|Ogólnego przeznaczenia V2|
    |Standardowa|Ogólnego przeznaczenia V1|
    |Standardowa|Blob Storage|
    |Premium|Magazyn bloków obiektów blob|

2. Utwórz kontener na koncie magazynu. Przejdź do swojego konta magazynu. W obszarze **Usługa obiektów blob**wybierz pozycję **Przeglądaj obiekty blob**. Wybierz **+ Kontener** u góry, aby utworzyć nowy kontener.

## <a name="set-up-continuous-data-export"></a>Konfigurowanie ciągłego eksportowania danych

Teraz, gdy masz miejsce docelowe do wyeksportowania danych, wykonaj następujące kroki, aby skonfigurować ciągły eksport danych.

1. Zaloguj się do aplikacji IoT Central.

2. W lewym okienku wybierz pozycję **Eksportowanie danych**.

    > [!Note]
    > Jeśli nie widzisz eksportu danych w lewym okienku, nie masz uprawnień do konfigurowania eksportu danych w aplikacji. Porozmawiaj z administratorem, aby skonfigurować eksport danych.

3. Wybierz przycisk **+ Nowy** w prawym górnym rogu. Wybierz jedną z **usługi Azure Event Hubs**, **usługa Azure Service Bus**lub magazyn **obiektów blob platformy Azure** jako miejsce docelowe eksportu. Maksymalna liczba wywozu na aplikację wynosi pięć.

    ![Tworzenie nowego ciągłego eksportowania danych](media/howto-export-data/new-export-definition.png)

4. W polu listy rozwijanej wybierz **obszar nazw Centrum zdarzeń, obszar**nazw usługi Service **Bus,** **obszar nazw konta magazynu**lub Wprowadź parametry **połączenia**.

    - Konta magazynu, obszary nazw centrów zdarzeń i przestrzenie nazw usługi Service Bus są widoczne tylko w tej samej subskrypcji co aplikacja IoT Central. Jeśli chcesz wyeksportować do miejsca docelowego poza tą **subskrypcją,** wybierz pozycję Wprowadź parametry połączenia i zobacz krok 5.
    - W przypadku aplikacji utworzonych przy użyciu bezpłatnego planu cenowego jedynym sposobem skonfigurowania ciągłego eksportowania danych jest ciąg połączenia. Aplikacje w planie bezpłatnych cen nie mają skojarzonej subskrypcji platformy Azure.

    ![Tworzenie nowego Centrum zdarzeń](media/howto-export-data/export-event-hub.png)

5. (Opcjonalnie) Jeśli wybrano **opcję Wprowadź parametry połączenia,** zostanie wyświetlene nowe pole do wklejenia ciągu połączenia. Aby uzyskać parametry połączenia dla:
    - Usługi Event Hubs lub Service Bus, przejdź do obszaru nazw w witrynie Azure portal.
        - W obszarze **Ustawienia**wybierz pozycję **Zasady dostępu udostępnionego**
        - Wybierz domyślny **rootmanageSharedAccessKey** lub utwórz nowy
        - Kopiowanie podstawowego lub pomocniczego ciągu połączenia
    - Konto magazynu, przejdź do konta magazynu w witrynie Azure portal:
        - W obszarze **Ustawienia**wybierz pozycję **Klawisze dostępu**
        - Kopiowanie ciągu połączenia key1 lub ciągu połączenia key2

6. Wybierz centrum zdarzeń, kolejkę, temat lub kontener z listy rozwijanej.

7. W obszarze **Dane do wyeksportowania**wybierz typy danych do wyeksportowania, ustawiając typ **Na**.

8. Aby włączyć ciągły eksport danych, upewnij się, że **przełącznik Włączone** jest **włączony**. Wybierz **pozycję Zapisz**.

9. Po kilku minutach dane pojawią się w wybranym miejscu docelowym.

## <a name="export-contents-and-format"></a>Eksportowanie zawartości i formatu

Wyeksportowane dane telemetryczne zawierają całą wiadomość, którą urządzenia zostały wysłane do usługi IoT Central, a nie tylko same wartości telemetryczne. Eksportowane dane urządzeń zawierają zmiany właściwości i metadanych wszystkich urządzeń, a eksportowane szablony urządzeń zawierają zmiany we wszystkich szablonach urządzeń.

W przypadku centrów zdarzeń i usługi Service Bus dane są eksportowane w czasie zbliżonym do rzeczywistego. Dane znajdują się we właściwości treści i jest w formacie JSON (patrz poniżej przykłady).

W przypadku magazynu obiektów Blob dane są eksportowane raz na minutę, przy czym każdy plik zawiera partię zmian od czasu ostatniego wyeksportowanego pliku. Wyeksportowane dane są umieszczane w trzech folderach w formacie JSON. Domyślne ścieżki na koncie magazynu to:

- Dane telemetryczne: _{container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{nazwa pliku}_
- Urządzenia: _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{nazwa pliku}_
- Szablony urządzeń: _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{nazwa pliku}_

Eksportowane pliki można przeglądać w portalu Azure, przechodząc do pliku i wybierając kartę **Edytuj obiekt blob.**


## <a name="telemetry"></a>Telemetria

W przypadku centrów zdarzeń i usługi Service Bus nowa wiadomość jest eksportowana szybko po odebraniu wiadomości przez usługę IoT Central z urządzenia, a każda wyeksportowana wiadomość zawiera pełny komunikat urządzenia wysłany w właściwości treści w formacie JSON.

W przypadku magazynu obiektów Blob wiadomości są wsadowe i eksportowane raz na minutę. Eksportowane pliki używają tego samego formatu co pliki wiadomości eksportowane przez [routing wiadomości usługi IoT Hub](../../iot-hub/tutorial-routing.md) do magazynu obiektów blob. 

> [!NOTE]
> W przypadku magazynu obiektów Blob upewnij się, `contentEncoding:utf-8` że `utf-16` `utf-32`urządzenia wysyłają wiadomości, które mają `contentType: application/JSON` i (lub , ). Zobacz [dokumentację usługi IoT Hub](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) na przykład.

Urządzenie, które wysłało dane telemetryczne, jest reprezentowane przez identyfikator urządzenia (zobacz poniższe sekcje). Aby uzyskać nazwy urządzeń, eksportuj dane urządzenia i skoreluj każdą wiadomość przy użyciu **identyfikatora connectionDeviceId,** który pasuje do **identyfikatora urządzenia** komunikatu urządzenia.

Jest to przykładowy komunikat odebrany w centrum zdarzeń lub kolejce lub temacie usługi Service Bus.

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

Jest to przykładowy rekord eksportowany do magazynu obiektów blob:

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

Każdy komunikat lub rekord w migawce reprezentuje jedną lub więcej zmian na urządzeniu i jego właściwości urządzenia i chmury od ostatniej wiadomości eksportowane. Obejmuje to:

- `id`urządzenia w IoT Central
- `displayName`urządzenia
- Identyfikator szablonu urządzenia w`instanceOf`
- `simulated`flaga, prawda, jeśli urządzenie jest symulowanym urządzeniem
- `provisioned`flaga, prawda, jeśli urządzenie zostało aprowiedzone
- `approved`flaga, prawda, jeśli urządzenie zostało zatwierdzone do wysyłania danych
- Wartości właściwości
- `properties`w tym wartości właściwości urządzenia i chmury

Usunięte urządzenia nie są eksportowane. Obecnie nie ma żadnych wskaźników w eksportowanych wiadomościach dla usuniętych urządzeń.

W przypadku centrów zdarzeń i usługi Service Bus wiadomości zawierające dane urządzenia są wysyłane do centrum zdarzeń lub kolejki lub tematu usługi Service Bus w czasie zbliżonym do rzeczywistego, jak ma to się odczasu ioT Central. 

W przypadku magazynu obiektów Blob nowa migawka zawierająca wszystkie zmiany od czasu ostatniego zapisanego jest eksportowana raz na minutę.

Jest to przykładowy komunikat dotyczący urządzeń i danych właściwości w Centrum zdarzeń lub w kolejce lub temacie usługi Service Bus:

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

Jest to przykładowa migawka zawierająca urządzenia i właściwości danych w magazynie obiektów blob. Eksportowane pliki zawierają jeden wiersz na rekord.

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

Każda wiadomość lub rekord migawki reprezentuje jedną lub więcej zmian w opublikowanym szablonie urządzenia od czasu ostatniej wyeksportowanego komunikatu. Informacje wysyłane w każdej wiadomości lub rekordzie obejmują:

- `id`szablonu urządzenia, który `instanceOf` pasuje do strumienia urządzeń powyżej
- `displayName`szablonu urządzenia
- Urządzenie `capabilityModel` wraz `interfaces`z definicjami jego , oraz telemetrią, właściwościami i poleceniami
- `cloudProperties`Definicje
- Zastąpienia i wartości początkowe, zgodne z`capabilityModel`

Usunięte szablony urządzeń nie są eksportowane. Obecnie nie ma żadnych wskaźników w eksportowanych wiadomościach dla usuniętych szablonów urządzeń.

W przypadku centrów zdarzeń i usługi Service Bus wiadomości zawierające dane szablonu urządzenia są wysyłane do centrum zdarzeń lub kolejki lub tematu usługi Service Bus w czasie zbliżonym do rzeczywistego, jak ma to się w centrum IoT. 

W przypadku magazynu obiektów Blob nowa migawka zawierająca wszystkie zmiany od czasu ostatniego zapisanego jest eksportowana raz na minutę.

Jest to przykładowy komunikat dotyczący danych szablonów urządzeń w Centrum zdarzeń lub w kolejce lub temacie usługi Service Bus:

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

Jest to przykładowa migawka zawierająca urządzenia i właściwości danych w magazynie obiektów blob. Eksportowane pliki zawierają jeden wiersz na rekord.

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
> Ta zmiana nie ma wpływu na format danych strumienia telemetryczego. Dotyczy to tylko urządzeń i szablonów urządzeń strumieni danych.

Jeśli w aplikacji w wersji zapoznawczej masz już istniejące dane z włączonymi strumieniami *szablonów urządzenia* i *urządzenia,* należy zaktualizować eksport do **30 czerwca 2020 r.** Dotyczy to eksportu do usługi Azure Blob Storage, Usługi Azure Event Hubs i usługi Azure Service Bus.

Od 3 lutego 2020 r. wszystkie nowe eksporty w aplikacjach z włączonymi szablonami urządzeń i urządzeń będą miały format danych opisany powyżej. Wszystkie eksporty utworzone wcześniej pozostaną w starym formacie danych do 30 czerwca 2020 r., po czym eksport zostanie automatycznie przeniesiony do nowego formatu danych. Nowy format danych jest zgodny z [urządzeniem,](https://docs.microsoft.com/rest/api/iotcentral/devices/get) [właściwością urządzenia,](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties) [właściwością chmury urządzenia](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties) i obiektami [szablonów urządzeń](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) w publicznym interfejsie API IoT Central. 
 
W przypadku **urządzeń**istotne różnice między starym formatem danych a nowym formatem danych obejmują:
- `@id`urządzenia, `deviceId` zmieniono jego nazwę na`id` 
- `provisioned`zostanie dodana flaga opisująca stan inicjowania obsługi administracyjnej urządzenia
- `approved`zostanie dodana flaga opisująca stan homologacji urządzenia
- `properties`w tym właściwości urządzenia i chmury, dopasowuje jednostki w publicznym interfejsie API

W przypadku **szablonów urządzeń**istotne różnice między starym formatem danych a nowym formatem danych obejmują:

- `@id`nazwa szablonu urządzenia została zmieniona na`id`
- `@type`nazwa szablonu urządzenia została `types`zmieniona na , a teraz jest tablicą

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Urządzenia (format przestarzały od 3 lutego 2020 r.)
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

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Szablony urządzeń (format przestarzały od 3 lutego 2020 r.)
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

Teraz, gdy wiesz, jak wyeksportować dane do usługi Azure Event Hubs, usługi Azure Service Bus i usługi Azure Blob Storage, przejdź do następnego kroku:

> [!div class="nextstepaction"]
> [Jak tworzyć elementów webhook](./howto-create-webhooks.md)
