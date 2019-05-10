---
title: Eksportuj dane do usługi Azure Event Hubs i Azure Service Bus | Dokumentacja firmy Microsoft
description: Sposób eksportowania danych z usługi Azure IoT Central aplikacji do usługi Azure Event Hubs i Azure Service Bus
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 78edeb0c418f5c426771d241464d389f8a632e96
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65463981"
---
# <a name="export-your-data-in-azure-iot-central"></a>Eksportowanie danych w usłudze Azure IoT Central

*W tym temacie mają zastosowanie do administratorów.*

W tym artykule opisano sposób używania funkcji eksportu ciągłego dane w usłudze Azure IoT Central Eksport danych w ramach swojej własnej **usługi Azure Event Hubs**, i **usługi Azure Service Bus** wystąpień. Możesz wyeksportować **pomiarów**, **urządzeń**, i **szablonów urządzeń** do docelowej dla analizy i szczegółowych informacji z ciepłej ścieżki. Obejmuje to wyzwolenie reguły niestandardowe w usłudze Azure Stream Analytics, wyzwalania niestandardowych przepływów pracy w usłudze Azure Logic Apps lub przekształcanie ich i przekazanie do niej za pomocą usługi Azure Functions. 

> [!Note]
> Jeszcze raz po włączeniu ciągły Eksport danych otrzymasz tylko dane od tej pory wartości. Obecnie nie można pobrać danych w czasie, gdy ciągły Eksport danych zostało wyłączone. Do przechowania większej ilości danych historycznych, należy włączyć funkcję ciągły Eksport danych z wcześniej.


## <a name="prerequisites"></a>Wymagania wstępne

- Musisz być administratorem w Twojej aplikacji IoT Central

## <a name="set-up-export-destination"></a>Skonfiguruj miejsce docelowe eksportu

Jeśli nie masz istniejących zdarzeń koncentratory/usługi Service Bus można wyeksportować do, wykonaj następujące kroki:

## <a name="create-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

1. Tworzenie [nowych nazw usługi Event Hubs w witrynie Azure portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Dowiedz się więcej w [dokumentacja usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Wybierz subskrypcję. 

    > [!Note] 
    > Teraz możesz wyeksportować dane do innych subskrypcji, które są **nie sam** co dla twojej aplikacji płatność za rzeczywiste użycie IoT Central. Połączysz się przy użyciu parametrów połączenia, w tym przypadku.
3. Tworzenie Centrum zdarzeń w przestrzeni nazw usługi Event Hubs. Przejdź do obszaru nazw, a następnie wybierz pozycję **+ Centrum zdarzeń** u góry, aby utworzyć wystąpienie Centrum zdarzeń.

## <a name="create-service-bus-namespace"></a>Tworzenie przestrzeni nazw usługi Service Bus

1. Tworzenie [nowej przestrzeni nazw magistrali usług w witrynie Azure portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . Dowiedz się więcej w [dokumentacja usługi Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Wybierz subskrypcję. 

    > [!Note] 
    > Teraz możesz wyeksportować dane do innych subskrypcji, które są **nie sam** co dla twojej aplikacji płatność za rzeczywiste użycie IoT Central. Połączysz się przy użyciu parametrów połączenia, w tym przypadku.

3. Przejdź do przestrzeni nazw usługi Service Bus i wybierz **+ kolejka** lub **+ temat** u góry, aby utworzyć kolejki lub tematu, aby wyeksportować do.


## <a name="set-up-continuous-data-export"></a>Konfigurowanie ciągły Eksport danych

Teraz, gdy docelowy usługi Event Hubs/Service Bus, aby wyeksportować dane, wykonaj następujące kroki, aby skonfigurować ciągły Eksport danych. 

1. Zaloguj się do aplikacji IoT Central.

2. W menu po lewej stronie wybierz **ciągły Eksport danych**.

    > [!Note]
    > Jeśli nie widzisz ciągły Eksport danych w menu po lewej stronie, nie jesteś administratorem w swojej aplikacji. Porozmawiaj z administratorem, aby skonfigurować Eksport danych.

    ![Utwórz nowy CRP Centrum zdarzeń](media/howto-export-data/export_menu1.png)

3. Wybierz **+ nowy** przycisk w prawym górnym rogu. Wybierz jedną z **usługi Azure Event Hubs** lub **usługi Azure Service Bus** jako miejsce docelowe eksportu. 

    > [!NOTE] 
    > Maksymalna liczba eksportów aplikacji wynosi pięć. 

    ![Utwórz nowy ciągły Eksport danych](media/howto-export-data/export_new1.png)

4. W polu listy rozwijanej wybierz swoje **przestrzeni nazw magistrali przestrzeń nazw/usługi Event Hubs**. Można również wybrać na liście, która jest ostatnia opcja **wprowadź parametry połączenia**. 

    > [!NOTE] 
    > Widoczne są tylko magazyn kont/Event Hubs przestrzenie nazw/usługi Service Bus przestrzeniach nazw **tej samej subskrypcji co aplikacja IoT Central**. Jeśli chcesz wyeksportować do miejsca docelowego spoza tej subskrypcji, wybierz opcję **wprowadź parametry połączenia** i zobacz krok 5.

    > [!NOTE] 
    > 7-dniowy, który eksportowania aplikacji w wersji próbnej, jedynym sposobem, aby skonfigurować ciągłe danych jest za pomocą parametrów połączenia. Jest to spowodowane 7-dniowy aplikacji w wersji próbnej nie mają skojarzonej subskrypcji platformy Azure.

    ![Utwórz nowy CRP Centrum zdarzeń](media/howto-export-data/export_create1.png)

5. (Opcjonalnie) Jeśli została wybrana opcja **wprowadź parametry połączenia**, nowe pole pojawia się należy wkleić parametry połączenia. Aby uzyskać parametry połączenia dla usługi:
    - Usługa Event Hubs lub usługi Service Bus, przejdź do przestrzeni nazw w witrynie Azure portal.
        - W obszarze **ustawienia**, wybierz opcję **współużytkowane zasady dostępu**
        - Wybierz domyślne **RootManageSharedAccessKey** lub Utwórz nową
        - Skopiuj parametry połączenia podstawowej lub dodatkowej
 
6. Wybierz Centrum zdarzeń/kolejki lub tematu w polu listy rozwijanej.

7. W obszarze **danych do wyeksportowania**, określ każdy rodzaj danych do wyeksportowania, ustawiając typ **na**.

6. Aby włączyć ciągły Eksport danych, upewnij się, **eksportu danych** jest **na**. Wybierz pozycję **Zapisz**.

    ![Konfigurowanie ciągły Eksport danych](media/howto-export-data/export_list1.png)

7. Po kilku minutach danych pojawi się w wybranej lokalizacji docelowej.


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
