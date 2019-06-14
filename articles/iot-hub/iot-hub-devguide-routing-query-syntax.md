---
title: Zapytanie dotyczące routing komunikatów usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — składnia zapytania do rozsyłania wiadomości w usłudze Azure IoT Hub.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 94d3599fe919cf648be7115be68002d2aa458ee3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60400647"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Kierowanie Składnia kwerendy komunikatów usługi IoT Hub

Routing komunikatów umożliwia użytkownikom trasy różne typy danych, to znaczy, komunikaty telemetryczne z urządzeń, zdarzenia cyklu życia urządzenia i bliźniaczej reprezentacji urządzenia zdarzenia zmian do różnych punktów końcowych. Za dotyczą zaawansowane zapytania i te dane przed przesłaniem jej do odbierania danych, które są ważne. W tym artykule opisano wiadomości na routingu język zapytań usługi IoT Hub i zawiera niektóre typowe wzorce zapytań.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Routing komunikatów umożliwia zapytania właściwości wiadomości oraz treść wiadomości, a także tagów bliźniaczych reprezentacji urządzeń i właściwości bliźniaczych reprezentacji urządzeń. Jeśli treść komunikatu nie jest JSON, routing komunikatów mogą nadal trasy wiadomości, ale zapytań nie można zastosować do treści wiadomości.  Zapytania są opisane jako wyrażenia logiczne, gdzie wartość logiczną PRAWDA sprawia, że zapytanie powiedzie się, który kieruje wszystkie dane przychodzące i wartość logiczną FAŁSZ zapytanie nie powiedzie się i żadne dane nie jest kierowany. Jeśli wyrażenie ma wartość null lub jest niezdefiniowany, jest ona traktowana jako wartość false, a następnie zostanie wygenerowany błąd w dziennikach diagnostycznych w razie awarii. Składnia zapytania musi być prawidłowy dla danej trasy, zapisać i oceniane.  

## <a name="message-routing-query-based-on-message-properties"></a>Routing zapytania o komunikat o, na podstawie właściwości wiadomości 

Definiuje usługę IoT Hub [typowego formatu](iot-hub-devguide-messages-construct.md) dla wszystkich urządzeń z chmurą messaging współdziałania w ramach różnych protokołów. Komunikat usługi IoT Hub zakłada następującą reprezentację JSON w wiadomości. Właściwości systemu są dodawane dla wszystkich użytkowników i Identyfikuj zawartość wiadomości. Selektywnie użytkownicy mogą dodawać właściwości aplikacji, do wiadomości. Zalecamy używanie nazw unikatowych właściwości komunikatów urządzenia do chmury usługi IoT Hub nie jest rozróżniana wielkość liter. Na przykład jeśli masz wiele właściwości o takiej samej nazwie, usługi IoT Hub wysyła jedna z właściwości.  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```

### <a name="system-properties"></a>Właściwości systemu

Właściwości systemu pomagać w identyfikacji zawartości i źródła wiadomości. 

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| contentType | string | Użytkownik określa typ zawartości komunikatu. Aby zezwolić na zapytania w treści wiadomości, ta wartość musi być ustawiona application/JSON. |
| contentEncoding | string | Użytkownik określa typ kodowania komunikatu. Dozwolone wartości to UTF-8, UTF-16 i UTF-32, jeśli ustawiono typ zawartości application/JSON. |
| iothub-connection-device-id | string | Ta wartość jest ustawiana przez usługę IoT Hub i identyfikuje identyfikator urządzenia. Aby wysłać zapytanie, użyj `$connectionDeviceId`. |
| iothub enqueuedtime | string | Ta wartość jest ustawiana przez usługę IoT Hub i reprezentuje rzeczywisty czas enqueuing komunikat w formacie UTC. Aby wysłać zapytanie, użyj `enqueuedTime`. |

Zgodnie z opisem w [IoT Hub komunikatów](iot-hub-devguide-messages-construct.md), istnieją dodatkowe systemu właściwości w komunikacie. Oprócz **contentType**, **contentEncoding**, i **enqueuedTime**, **connectionDeviceId** i  **connectionModuleId** również mogą być przeszukiwane.

### <a name="application-properties"></a>Właściwości aplikacji

Właściwości aplikacji są zdefiniowane przez użytkownika ciągów, które można dodać do wiadomości. Te pola są opcjonalne.  

### <a name="query-expressions"></a>Wyrażenia zapytań

Zapytanie dotyczące właściwości systemu wiadomości musi być poprzedzony `$` symboli. Zapytań dotyczących właściwości aplikacji są dostępne przy użyciu nazwy i nie powinien być poprzedzony `$`symboli. Jeśli nazwa właściwości aplikacji, który rozpoczyna się od `$`, usługi IoT Hub będzie wyszukaj go w oknie Właściwości systemu i nie zostanie znaleziony, a następnie będzie wyglądać we właściwościach aplikacji. Na przykład: 

Aby wysłać zapytanie o contentEncoding właściwości systemu 

```sql
$contentEncoding = 'UTF-8'
```

Aby wykonać zapytanie na processingPath właściwości aplikacji:

```sql
processingPath = 'hot'
```

Aby połączyć te zapytania, można użyć wyrażenia logiczne i funkcje:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Pełną listę obsługiwanych operatory i funkcje są widoczne w [wyrażeń i warunków](iot-hub-devguide-query-language.md#expressions-and-conditions)

## <a name="message-routing-query-based-on-message-body"></a>Routing zapytania o komunikat o, oparte na treść komunikatu 

Umożliwiające wykonywanie zapytań w treści komunikatu powinna mieć komunikat JSON zakodowane w formacie UTF-8, UTF-16 lub UTF-32. `contentType` Musi być równa `application/JSON` i `contentEncoding` do jednego z obsługiwanych kodowań UTF we właściwości systemu. Jeśli te właściwości nie są określone, usługi IoT Hub nie będą oceniać wyrażenia zapytania w treści wiadomości. 

Poniższy przykład pokazuje, jak utworzyć wiadomości z poprawnie sformułowane i zakodowany treść kodu JSON: 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

### <a name="query-expressions"></a>Wyrażenia zapytań

Zapytania w treści wiadomości musi być poprzedzony `$body`. Odwołanie do jednostki, odwołanie do tablicy treści lub wiele odwołań treści można użyć w wyrażeniu zapytania. Wyrażenie zapytania, można także połączyć odwołanie treści za pomocą właściwości systemu komunikat i odwołanie do właściwości aplikacji wiadomości. Na przykład poniżej przedstawiono wszystkie wyrażenia prawidłowe zapytanie: 

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```

```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```

```sql
length($body.Weather.Location.State) = 2 
```

```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

## <a name="message-routing-query-based-on-device-twin"></a>Routing zapytania o komunikat o, oparte na bliźniaczej reprezentacji urządzenia 

Routing komunikatów umożliwia zapytania [bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md) tagów i właściwości, które są obiektami JSON. Należy pamiętać, że wykonywanie zapytań w bliźniaczej reprezentacji modułu nie jest obsługiwany. Poniżej przedstawiono przykładowe bliźniaczej reprezentacji urządzenia tagi i właściwości.

```JSON
{
    "tags": { 
        "deploymentLocation": { 
            "building": "43", 
            "floor": "1" 
        } 
    }, 
    "properties": { 
        "desired": { 
            "telemetryConfig": { 
                "sendFrequency": "5m" 
            }, 
            "$metadata" : {...}, 
            "$version": 1 
        }, 
        "reported": { 
            "telemetryConfig": { 
                "sendFrequency": "5m", 
                "status": "success" 
            },
            "batteryLevel": 55, 
            "$metadata" : {...}, 
            "$version": 4 
        } 
    } 
} 
```

### <a name="query-expressions"></a>Wyrażenia zapytań

Zapytania w treści wiadomości musi być poprzedzony `$twin`. Wyrażenie zapytania, można także połączyć odwołanie do bliźniaczej reprezentacji znacznika lub właściwości z odwołaniem do treści, wiadomości właściwości systemu i odwołanie do właściwości aplikacji wiadomości. Zalecamy używanie unikatowe nazwy w tagów i właściwości, ponieważ zapytanie nie jest rozróżniana wielkość liter. Również punktowanych za pomocą `twin`, `$twin`, `body`, lub `$body`, jako nazwy właściwości. Na przykład poniżej przedstawiono wszystkie wyrażenia prawidłowe zapytanie: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [routing komunikatów](iot-hub-devguide-messages-d2c.md).
* Spróbuj [wiadomości routingu samouczek](tutorial-routing.md).