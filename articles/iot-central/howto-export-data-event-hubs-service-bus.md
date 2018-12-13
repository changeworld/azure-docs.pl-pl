---
title: Eksportuj dane do usługi Azure Event Hubs i Azure Service Bus | Dokumentacja firmy Microsoft
description: Sposób eksportowania danych z usługi Azure IoT Central aplikacji do usługi Azure Event Hubs i Azure Service Bus
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 14b51f109ca76661ac10c99d42002dda45bc0500
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318715"
---
# <a name="export-your-data-in-azure-iot-central"></a>Eksportowanie danych w usłudze Azure IoT Central

*W tym temacie mają zastosowanie do administratorów.*

Ten artykuł omawia lepszy sposób używania funkcji eksportu ciągłego dane w usłudze Azure IoT Central Eksport danych w ramach swojej własnej **usługi Azure Event Hubs**, i **usługi Azure Service Bus** wystąpień. Możesz wyeksportować **pomiarów**, **urządzeń**, i **szablonów urządzeń** do docelowej dla analizy i szczegółowych informacji z ciepłej ścieżki. Obejmuje to wyzwolenie reguły niestandardowe w usłudze Azure Stream Analytics, wyzwalania niestandardowych przepływów pracy w usłudze Azure Logic Apps lub przekształcanie ich i przekazanie do niej za pomocą usługi Azure Functions. 

> [!Note]
> Jeszcze raz po włączeniu ciągły Eksport danych otrzymasz tylko dane od tej pory wartości. Obecnie nie można pobrać danych w czasie, gdy ciągły Eksport danych zostało wyłączone. Do przechowania większej ilości danych historycznych, należy włączyć funkcję ciągły Eksport danych z wcześniej.


## <a name="prerequisites"></a>Wymagania wstępne

- Musisz być administratorem w Twojej aplikacji IoT Central

## <a name="export-to-azure-event-hubs-and-azure-service-bus"></a>Eksportowanie do usługi Azure Event Hubs i usługi Azure Service Bus

Pomiary, urządzeń i danych szablonów urządzeń zostaną wyeksportowane do usługi Centrum zdarzeń lub kolejki usługi Service Bus lub temat w czasie zbliżonym do rzeczywistego. Pomiary wyeksportowane dane zawiera materiałami komunikat urządzenia wysyłane do IoT Central, nie tylko wartości pomiarów samodzielnie. Dane wyeksportowane urządzenia zawierają zmiany właściwości i ustawienia wszystkie urządzenia, a szablony wyeksportowane urządzenia zawiera zmiany do wszystkich szablonów urządzenia. Wyeksportowane dane jest we właściwości "treść" i jest w formacie JSON.

> [!NOTE]
> Wybierając jako miejsce docelowe eksportu, kolejek i tematów usługi Service Bus **nie może mieć sesji lub wykrywania duplikatów, włączone**. Jedną z tych opcji są włączone, niektóre wiadomość, nie pojawi się w Twojej kolejki lub tematu.

### <a name="measurements"></a>Miary

Nowa wiadomość zostanie wyeksportowany szybko po IoT Central odbiera komunikaty z urządzenia. Każdy komunikat wyeksportowany w usłudze Event Hubs i Service Bus zawiera cały komunikat urządzeń, wysyłane we właściwości "treść" w formacie JSON. 

> [!NOTE]
> Urządzenia, które wysyłać pomiary są reprezentowane przez identyfikatory urządzeń (patrz poniżej). Aby uzyskać nazwy urządzenia, wyeksportować dane urządzeń i korelowanie każdy komunikat przy użyciu **connectionDeviceId** odpowiadający **deviceId** komunikatu urządzenia.

W poniższym przykładzie przedstawiono wiadomość o danych pomiarów odebrana w Centrum zdarzeń lub kolejki usługi Service Bus lub temat.

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-12T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>Urządzenia

Komunikaty zawierające dane urządzenia są wysyłane do Centrum zdarzeń lub kolejki usługi Service Bus lub temat co kilka minut. Oznacza to, że co kilka minut, partię komunikatów zostaną dostarczone z danymi o
- Nowe urządzenia, które zostały dodane
- Urządzenia z właściwością zmienione i ustawianie wartości

Każdy komunikat reprezentuje co najmniej jedną zmianę na urządzeniu, od momentu ostatniego komunikatu wyeksportowany. Zawiera informacje, które będą wysyłane w każdej wiadomości:
- `id` z urządzeń IoT Central
- `name` urządzenia
- `deviceId` z [usługi Device Provisioning](https://aka.ms/iotcentraldocsdps)
- Informacje o szablonie urządzenia
- Wartości właściwości
- Ustawianie wartości

> [!NOTE]
> Urządzenia został usunięty, ponieważ nie są eksportowane ostatniej partii. Obecnie w nie ma żadnych wskaźników wyeksportowanego wiadomości dla usuniętego urządzenia.
>
> Każde urządzenie należy do szablonu urządzenia jest reprezentowany przez urządzenie identyfikatora szablonu. Aby uzyskać nazwę szablonu urządzenia, należy wyeksportować dane szablonu urządzeń zbyt.

Poniższy przykład przedstawia komunikat o danych urządzenia w Centrum zdarzeń lub kolejki usługi Service Bus lub temat:


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-11T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>Szablony urządzenia

Komunikaty zawierające dane szablony urządzenia są wysyłane do Centrum zdarzeń lub kolejki usługi Service Bus lub temat co kilka minut. Oznacza to, że co kilka minut, partię komunikatów zostaną dostarczone z danymi o
- Nowe szablony urządzenia, które zostały dodane
- Urządzenie szablonów przy użyciu zmienionego pomiarów, właściwości i ustawienia definicji

Każdy komunikat reprezentuje co najmniej jedną zmianę w szablonie urządzenia od czasu ostatniego komunikatu wyeksportowany. Zawiera informacje, które będą wysyłane w każdej wiadomości:
- `id` szablonu urządzenia
- `name` szablonu urządzenia
- `version` szablonu urządzenia
- Typy danych pomiaru i minimalnej/maksymalnej wartości
- Typy danych właściwości i wartości domyślne
- Ustawianie typów danych i wartości domyślne

> [!NOTE]
> Szablony urządzenia usunięty od czasu ostatniej partii nie są eksportowane. Obecnie istnieją nie wskaźników w komunikatach wyeksportowane szablony usuniętego urządzenia.

Poniższy przykład przedstawia komunikat o danych szablony urządzenia w Centrum zdarzeń lub kolejki usługi Service Bus lub temat:

```json
{
  "body": {
    "id": "<id>",
    "version": "1.0.0",
    "name": "<templateName>",
    "measurements": {
      "telemetry": {
        "humidity": {
          "dataType": "double",
          "name": "humidity"
        },
        "pressure": {
          "dataType": "double",
          "name": "pressure"
        },
        "temp": {
          "dataType": "double",
          "name": "temperature"
        }
      }
    },
    "properties": {
      "cloud": {
        "location": {
          "dataType": "string",
          "name": "Location"
        }
      },
      "device": {
        "dieNumber": {
          "dataType": "double",
          "name": "Die Number"
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
    }
  },
  "annotations": {
    "iotcentral-message-source": "deviceTemplates",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 25315,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274985085
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 25315,
  "enqueuedTimeUtc": "2018-10-11T16:23:05.085Z",
  "offset": "<offset>",
}
```

## <a name="next-steps"></a>Kolejne kroki

Teraz gdy wiesz, jak wyeksportować dane do usługi Azure Event Hubs i Azure Service Bus, przejdź do następnego kroku:

> [!div class="nextstepaction"]
> [Porady: wyzwalanie usługi Azure Functions](howto-trigger-azure-functions.md)
