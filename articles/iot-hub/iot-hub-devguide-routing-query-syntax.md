---
title: Zapytanie dotyczące routingu wiadomości usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Dowiedz się więcej o języku zapytań routingu wiadomości usługi IoT Hub, którego można użyć do zastosowania zapytań rozszerzonych do wiadomości w celu odbierania danych, które są dla Ciebie ważne.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: b76ef431e4c0ad63929378c1f48c6ab06776cb25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271111"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Składnia zapytania dotyczącego routingu komunikatów usługi IoT Hub

Routing wiadomości umożliwia użytkownikom kierowanie różnych typów danych, a mianowicie komunikatów telemetrycznych urządzenia, zdarzeń cyklu życia urządzenia i bliźniaczej reprezentacji urządzenia zmieniać zdarzenia do różnych punktów końcowych. Można również zastosować zapytania rozszerzone do tych danych przed przekierowaniem go do odbierania danych, które są dla Ciebie ważne. W tym artykule opisano język kwerendy routingu wiadomości usługi IoT Hub i zawiera kilka typowych wzorców zapytań.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Routing wiadomości umożliwia wykonywanie zapytań dotyczących właściwości wiadomości i treści wiadomości, a także tagów bliźniaczej reprezentacji urządzenia i właściwości bliźniaczej reprezentacji urządzenia. Jeśli treść wiadomości nie jest JSON, routing wiadomości nadal można rozkiesyskierować wiadomości, ale kwerendy nie mogą być stosowane do treści wiadomości.  Zapytania są opisane jako wyrażenia logiczne, w których wartość true logiczna sprawia, że kwerenda kończy się powodzeniem, które trasy wszystkich danych przychodzących, a wartość false logiczna kończy kwerendę i żadne dane nie są kierowane. Jeśli wyrażenie ma wartość null lub undefined, jest traktowane jako false i błąd zostanie wygenerowany w dziennikach diagnostycznych w przypadku awarii. Składnia kwerendy musi być poprawna, aby trasa została zapisana i oceniona.  

## <a name="message-routing-query-based-on-message-properties"></a>Kwerenda routingu wiadomości na podstawie właściwości wiadomości 

Centrum IoT hub definiuje [wspólny format](iot-hub-devguide-messages-construct.md) dla wszystkich wiadomości z urządzenia do chmury dla współdziałania między protokołami. Komunikat Usługi IoT Hub zakłada następującą reprezentację JSON wiadomości. Właściwości systemu są dodawane dla wszystkich użytkowników i identyfikują zawartość wiadomości. Użytkownicy mogą selektywnie dodawać właściwości aplikacji do wiadomości. Zaleca się używanie unikatowych nazw właściwości, ponieważ usługa IoT Hub— obsługa wiadomości z urządzenia do chmury nie jest rozróżniana. Na przykład jeśli masz wiele właściwości o tej samej nazwie, Centrum IoT wyśle tylko jedną z właściwości.  

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

Właściwości systemu pomagają zidentyfikować zawartość i źródło wiadomości. 

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Contenttype | ciąg | Użytkownik określa typ zawartości wiadomości. Aby zezwolić na kwerendę w treści wiadomości, ta wartość powinna być ustawiona application/JSON. |
| contentEncoding | ciąg | Użytkownik określa typ kodowania wiadomości. Dozwolone wartości to UTF-8, UTF-16, UTF-32, jeśli contentType jest ustawiony na application/JSON. |
| iothub-connection-device-id | ciąg | Ta wartość jest ustawiana przez centrum IoT Hub i identyfikuje identyfikator urządzenia. Aby kwerendy, należy użyć `$connectionDeviceId`. |
| iothub-enqueuedtime | ciąg | Ta wartość jest ustawiana przez Centrum IoT i reprezentuje rzeczywisty czas wyłudzania wiadomości w czasie UTC. Aby kwerendy, należy użyć `enqueuedTime`. |
| nazwa interfejsu iothub | ciąg | Ta wartość jest ustawiana przez użytkownika i reprezentuje nazwę interfejsu bliźniaczej reprezentacji cyfrowej, który implementuje komunikat telemetryczny. Aby kwerendy, należy użyć `$interfaceName`. Ta funkcja jest dostępna w ramach [publicznej wersji zapoznawczej IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md). |

Zgodnie z opisem w [komunikatach Centrum IoT](iot-hub-devguide-messages-construct.md)istnieją dodatkowe właściwości systemu w komunikacie. Oprócz **contentType**, **contentEncoding**, i **enqueuedTime**, **connectionDeviceId** i **connectionModuleId** można również zbadać.

### <a name="application-properties"></a>Właściwości aplikacji

Właściwości aplikacji są ciągami zdefiniowanymi przez użytkownika, które można dodać do wiadomości. Te pola są opcjonalne.  

### <a name="query-expressions"></a>Wyrażenia kwerendy

Kwerenda na właściwości systemu wiadomości musi być `$` poprzedzony symbolem. Zapytania dotyczące właściwości aplikacji są dostępne z ich nazwą i nie `$`powinny być poprzedzone symbolem. Jeśli nazwa właściwości aplikacji `$`zaczyna się od , a następnie Centrum IoT będzie wyszukiwać go we właściwościach systemu, a nie zostanie znaleziony, a następnie będzie wyglądać we właściwościach aplikacji. Przykład: 

Aby zbadać zawartość właściwości systemuOdkodowywania 

```sql
$contentEncoding = 'UTF-8'
```

Aby zbadać na processing właściwości aplikacjiPath:

```sql
processingPath = 'hot'
```

Aby połączyć te zapytania, można użyć wyrażeń logicznych i funkcji:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Pełna lista obsługiwanych operatorów i funkcji jest wyświetlana w [wyrażenia i warunkach](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="message-routing-query-based-on-message-body"></a>Kwerenda routingu wiadomości na podstawie treści wiadomości

Aby włączyć wykonywanie zapytań w treści wiadomości, komunikat powinien znajdować się w JSON zakodowane w UTF-8, UTF-16 lub UTF-32. Musi `contentType` być ustawiona do `application/JSON` i `contentEncoding` do jednego z obsługiwanych kodowania UTF we właściwości systemu. Jeśli te właściwości nie są określone, Usługa IoT Hub nie oceni wyrażenia kwerendy w treści wiadomości. 

W poniższym przykładzie pokazano, jak utworzyć wiadomość z prawidłowo uformowaną i zakodowaną treścią JSON: 

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

> [!NOTE] 
> To pokazuje, jak obsługiwać kodowanie treści w javascript. Jeśli chcesz wyświetlić przykład w języku C#, pobierz [przykłady usługi Azure IoT C#.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Rozpaj plik master.zip. Rozwiązanie programu Visual Studio *SimulatedDevice*'s Program.cs plik pokazuje, jak kodować i przesyłać wiadomości do Centrum IoT Hub. Jest to ten sam przykład używany do testowania routingu wiadomości, jak wyjaśniono w [samouczku Routing wiadomości](tutorial-routing.md). W dolnej części Program.cs, ma również metodę odczytu w jednym z zakodowanych plików, dekodować go i zapisać go z powrotem jako ASCII, dzięki czemu można go odczytać. 


### <a name="query-expressions"></a>Wyrażenia kwerendy

Kwerenda w treści wiadomości musi być `$body`poprzedzony . W wyrażeniu kwerendy można użyć odwołania do treści, odwołania do tablicy treści lub wielu odwołań do treści. Wyrażenie kwerendy można również połączyć odwołanie do treści z właściwościami systemu wiadomości i odwołania właściwości aplikacji wiadomości. Na przykład wszystkie prawidłowe wyrażenia kwerendy są następujące: 

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

## <a name="message-routing-query-based-on-device-twin"></a>Kwerenda routingu wiadomości oparta na bliźniaczej reprezentacji urządzenia 

Routing wiadomości umożliwia wykonywanie zapytań w tagach i właściwościach [bliźniaczej reprezentacji urządzenia,](iot-hub-devguide-device-twins.md) które są obiektami JSON. Obsługiwane jest również wykonywanie zapytań dotyczących bliźniaczej reprezentacji modułu. Poniżej przedstawiono przykład tagów i właściwości bliźniaczej reprezentacji urządzenia.

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

### <a name="query-expressions"></a>Wyrażenia kwerendy

Kwerenda na bliźniaczej wiadomości musi `$twin`być poprzedzony . Wyrażenie kwerendy można również połączyć tag bliźniaczej reprezentacji lub odwołanie do właściwości z odwołaniem do treści, właściwości systemu wiadomości i odwołania właściwości aplikacji wiadomości. Zaleca się używanie unikatowych nazw w tagach i właściwościach, ponieważ kwerenda nie jest rozróżniana wielkość liter. Dotyczy to zarówno bliźniąt urządzeń, jak i bliźniąt modułów. Powstrzymaj się `twin` `$twin`również `body`od `$body`używania , , lub , jako nazwy właściwości. Na przykład wszystkie prawidłowe wyrażenia kwerendy są następujące: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

Kwerenda routingu na treści lub urządzenia bliźniaczej reprezentacji z kropką w nazwie ładunku lub właściwości nie jest obsługiwana.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [routingu wiadomości](iot-hub-devguide-messages-d2c.md).
* Wypróbuj [samouczek routingu wiadomości](tutorial-routing.md).
