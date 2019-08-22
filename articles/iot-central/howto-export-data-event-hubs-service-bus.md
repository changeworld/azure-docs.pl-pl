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
ms.openlocfilehash: e6df6a1f751106f62cdfecc3a7b5efb0fe4c63bf
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875995"
---
# <a name="export-your-data-in-azure-iot-central"></a>Eksportowanie danych do usługi Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

*Ten temat ma zastosowanie do administratorów.*

W tym artykule opisano sposób używania funkcji ciągłego eksportu danych w usłudze Azure IoT Central do eksportowania danych do własnych **Event Hubs platformy Azure**i **Azure Service Bus** wystąpień. **Pomiary**, **urządzenia**i **Szablony urządzeń** można eksportować do własnych miejsc docelowych w celu przeanalizowania i analizy ścieżki ciepłej. Obejmuje to wyzwalanie reguł niestandardowych w Azure Stream Analytics, wyzwalanie niestandardowych przepływów pracy w programie Azure Logic Apps lub przekształcanie danych i przekazywanie ich przez Azure Functions. 

> [!Note]
> Po włączeniu ciągłego eksportowania danych w tym momencie otrzymujesz tylko dane z tego momentu. Obecnie nie można pobrać danych przez czas, gdy ciągły eksport danych jest wyłączony. Aby zachować bardziej historyczne dane, należy wczesnie włączyć ciągły eksport danych.


## <a name="prerequisites"></a>Wymagania wstępne

- Musisz być administratorem w aplikacji IoT Central

## <a name="set-up-export-destination"></a>Skonfiguruj miejsce docelowe eksportu

Jeśli nie masz istniejącego Event Hubs/Service Bus do eksportowania do programu, wykonaj następujące kroki:

## <a name="create-event-hubs-namespace"></a>Tworzenie przestrzeni nazw Event Hubs

1. Utwórz [nową przestrzeń nazw Event Hubs w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Więcej informacji można znaleźć w witrynie [Azure Event Hubs docs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. wybierz subskrypcję. 

    > [!Note] 
    > Teraz możesz eksportować dane do innych subskrypcji, które **nie są takie same** jak dla aplikacji z opcją płatność zgodnie z rzeczywistym użyciem IoT Central. W tym przypadku zostanie nawiązane połączenie przy użyciu parametrów połączenia.
3. Utwórz centrum zdarzeń w przestrzeni nazw Event Hubs. Przejdź do obszaru nazw, a następnie wybierz pozycję **+ centrum zdarzeń** u góry, aby utworzyć wystąpienie centrum zdarzeń.

## <a name="create-service-bus-namespace"></a>Tworzenie przestrzeni nazw Service Bus

1. Utwórz [nową przestrzeń nazw Service Bus w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . Więcej informacji można znaleźć w dokumentacji [Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. wybierz subskrypcję. 

    > [!Note] 
    > Teraz możesz eksportować dane do innych subskrypcji, które **nie są takie same** jak dla aplikacji z opcją płatność zgodnie z rzeczywistym użyciem IoT Central. W tym przypadku zostanie nawiązane połączenie przy użyciu parametrów połączenia.

3. Przejdź do przestrzeni nazw Service Bus, a następnie wybierz pozycję **+ Kolejka** lub **+ temat** u góry, aby utworzyć kolejkę lub temat do wyeksportowania.


## <a name="set-up-continuous-data-export"></a>Konfigurowanie ciągłego eksportu danych

Teraz, gdy masz miejsce docelowe Event Hubs/Service Bus do eksportowania danych do programu, wykonaj następujące kroki, aby skonfigurować ciągły eksport danych. 

1. Zaloguj się do aplikacji IoT Central.

2. W menu po lewej stronie wybierz pozycję **ciągły eksport danych**.

    > [!Note]
    > Jeśli nie widzisz ciągłego eksportu danych w menu po lewej stronie, nie jesteś administratorem w swojej aplikacji. Skontaktuj się z administratorem, aby skonfigurować eksportowanie danych.

    ![Utwórz nowe centrum zdarzeń CDE](media/howto-export-data/export_menu1.png)

3. Wybierz przycisk **+ Nowy** w prawym górnym rogu. Wybierz jedną z **Event Hubs platformy Azure** lub **Azure Service Bus** jako lokalizację docelową eksportu. 

    > [!NOTE] 
    > Maksymalna liczba eksportów na aplikację wynosi pięć. 

    ![Utwórz nowy ciągły eksport danych](media/howto-export-data/export_new1.png)

4. W polu listy rozwijanej wybierz **Event Hubs przestrzeń nazw/Service Bus przestrzeń**nazw. Możesz również wybrać ostatnią opcję z listy, która jest wprowadzeniem **parametrów połączenia**. 

    > [!NOTE] 
    > Będą widoczne tylko konta magazynu/Event Hubs przestrzenie nazw/Service Bus przestrzenie nazw w **tej samej subskrypcji,** w której znajduje się aplikacja IoT Central. Jeśli chcesz wyeksportować do lokalizacji docelowej poza tą subskrypcją, wybierz pozycję **wprowadź parametry połączenia** i zobacz krok 5.

    > [!NOTE] 
    > W przypadku 7-dniowych aplikacji próbnych jedynym sposobem skonfigurowania ciągłego eksportu danych jest użycie parametrów połączenia. Wynika to z faktu, że 7-dniowe aplikacje próbne nie mają skojarzonej subskrypcji platformy Azure.

    ![Utwórz nowe centrum zdarzeń CDE](media/howto-export-data/export_create1.png)

5. Obowiązkowe W przypadku wybrania opcji **wprowadź parametry połączenia**pojawi się nowe pole umożliwiające wklejenie parametrów połączenia. Aby uzyskać parametry połączenia dla:
    - Event Hubs lub Service Bus, przejdź do przestrzeni nazw w Azure Portal.
        - W obszarze **Ustawienia**wybierz pozycję **zasady dostępu** współdzielonego.
        - Wybierz domyślną **RootManageSharedAccessKey** lub Utwórz nową
        - Skopiuj podstawowe lub pomocnicze parametry połączenia
 
6. W polu listy rozwijanej wybierz centrum zdarzeń/kolejkę lub temat.

7. W obszarze **dane do eksportowania**Określ każdy typ danych do wyeksportowania, ustawiając typ na wartość **włączone**.

6. Aby włączyć funkcję ciągłego eksportowania danych, upewnij się, że **Eksportowanie danych** jest **włączone**. Wybierz pozycję **Zapisz**.

    ![Konfigurowanie ciągłego eksportowania danych](media/howto-export-data/export_list1.png)

7. Po kilku minutach dane zostaną wyświetlone w wybranym miejscu docelowym.


## <a name="export-to-azure-event-hubs-and-azure-service-bus"></a>Eksportuj do usługi Azure Event Hubs i Azure Service Bus

Dane pomiarów, urządzeń i szablonów urządzeń są eksportowane do centrum zdarzeń lub kolejki Service Bus lub tematu w niemal czasie rzeczywistym. Eksportowane dane pomiarów zawierają część komunikatów wysyłanych przez urządzenia do IoT Central, a nie tylko wartości pomiarów. Dane wyeksportowanych urządzeń zawierają zmiany właściwości i ustawień wszystkich urządzeń, a wyeksportowane szablony urządzeń zawierają zmiany we wszystkich szablonach urządzeń. Eksportowane dane są we właściwości "Body" i są w formacie JSON.

> [!NOTE]
> W przypadku wybrania Service Bus jako miejsca docelowego eksportu kolejki i tematy **nie mogą mieć włączonej sesji lub Wykrywanie duplikatów**. Jeśli jedna z tych opcji jest włączona, niektóre komunikaty nie docierają do kolejki lub tematu.

### <a name="measurements"></a>Miary

Nowy komunikat jest eksportowany szybko po IoT Central otrzyma komunikat z urządzenia. Każdy wyeksportowany komunikat w Event Hubs i Service Bus zawiera pełną wiadomość wysłaną przez urządzenie we właściwości "Body" w formacie JSON. 

> [!NOTE]
> Urządzenia, które wysyłają pomiary, są reprezentowane przez identyfikatory urządzeń (zobacz następujące sekcje). Aby uzyskać nazwy urządzeń, wyeksportuj dane urządzenia i skorelowania poszczególnych komunikatów przy użyciu **connectionDeviceId** , który odpowiada identyfikatorowi **deviceId** komunikatu urządzenia.

Poniższy przykład przedstawia komunikat dotyczący danych pomiarów odebranych w centrum zdarzeń lub Service Bus kolejki lub tematu.

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

Komunikaty zawierające dane urządzenia są wysyłane do centrum zdarzeń lub kolejki Service Bus lub tematu co kilka minut. Oznacza to, że co kilka minut zostanie odebrana partia komunikatów z danymi dotyczącymi
- Nowe urządzenia, które zostały dodane
- Urządzenia ze zmienionymi wartościami właściwości i ustawień

Każdy komunikat reprezentuje co najmniej jedną zmianę urządzenia od ostatniego wyeksportowanego komunikatu. Informacje wysyłane w poszczególnych wiadomościach obejmują:
- `id`urządzenia w IoT Central
- `name`urządzenia
- `deviceId`z [usługi Device](https://aka.ms/iotcentraldocsdps) Provisioning
- Informacje o szablonie urządzenia
- Wartości właściwości
- Ustawianie wartości

> [!NOTE]
> Urządzenia usunięte od ostatniej partii nie są eksportowane. Obecnie nie ma żadnych wskaźników w wyeksportowanych komunikatach dotyczących usuniętych urządzeń.
>
> Szablon urządzenia, do którego należy każde urządzenie, jest reprezentowany przez identyfikator szablonu urządzenia. Aby uzyskać nazwę szablonu urządzenia, należy również wyeksportować dane szablonu urządzenia.

Poniższy przykład przedstawia komunikat dotyczący danych urządzenia w centrum zdarzeń lub Service Bus kolejki lub tematu:


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

### <a name="device-templates"></a>Szablony urządzeń

Komunikaty zawierające szablony urządzeń są wysyłane do centrum zdarzeń lub kolejki Service Bus lub tematu co kilka minut. Oznacza to, że co kilka minut zostanie odebrana partia komunikatów z danymi dotyczącymi
- Dodano nowe szablony urządzeń
- Szablony urządzeń ze zmienionymi pomiarymi, właściwościami i definicjami ustawień

Każdy komunikat reprezentuje co najmniej jedną zmianę w szablonie urządzenia od ostatniego wyeksportowanego komunikatu. Informacje wysyłane w poszczególnych wiadomościach obejmują:
- `id`szablonu urządzenia
- `name`szablonu urządzenia
- `version`szablonu urządzenia
- Typy danych pomiarowych i wartości minimalne/maksymalne
- Typy danych właściwości i wartości domyślne
- Ustawianie typów danych i wartości domyślnych

> [!NOTE]
> Szablony urządzeń usunięte od momentu ostatniej partii nie są eksportowane. Obecnie nie ma żadnych wskaźników w wyeksportowanych komunikatach dla usuniętych szablonów urządzeń.

Poniższy przykład przedstawia komunikat dotyczący danych szablonów urządzeń w centrum zdarzeń lub Service Bus kolejki lub tematu:

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

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wyeksportować dane do usługi Azure Event Hubs i Azure Service Bus, przejdź do następnego kroku:

> [!div class="nextstepaction"]
> [Jak wyzwolić Azure Functions](howto-trigger-azure-functions.md)
