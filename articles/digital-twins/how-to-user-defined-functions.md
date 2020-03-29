---
title: Jak tworzyć funkcje zdefiniowane przez użytkownika — w usłudze Azure Digital Twins | Dokumenty firmy Microsoft
description: Jak utworzyć funkcje zdefiniowane przez użytkownika, matchery i przypisania ról w usłudze Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: 232d85789c25e905873286eba6fda32c327a6e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276935"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Jak utworzyć funkcje zdefiniowane przez użytkownika w usłudze Azure Digital Twins

[Funkcje zdefiniowane przez użytkownika](./concepts-user-defined-functions.md) umożliwiają użytkownikom konfigurowanie niestandardowej logiki do wykonywania z przychodzących komunikatów telemetrycznych i metadanych wykresu przestrzennego. Użytkownicy mogą również wysyłać zdarzenia do wstępnie [zdefiniowanych punktów końcowych](./how-to-egress-endpoints.md).

W tym przewodniku przedstawiamy przykład pokazujący, jak wykrywać i ostrzegać o każdym czytaniu przekraczającym określoną temperaturę z odebranych zdarzeń temperatury.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Odwołanie do biblioteki klienta

Funkcje dostępne jako metody pomocnicze w czasie wykonywania funkcji zdefiniowanych przez użytkownika są wymienione w dokumencie [referencyjnym biblioteki klienta.](./reference-user-defined-functions-client-library.md)

## <a name="create-a-matcher"></a>Tworzenie zapałka

Matchers są obiekty wykresu, które określają, jakie funkcje zdefiniowane przez użytkownika są uruchamiane dla danego komunikatu telemetryczne.

- Prawidłowe porównania warunków matcher:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Prawidłowe cele warunku matcher:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

Poniższy przykład matcher ocenia true na każdym `"Temperature"` zdarzeniu telemetrycznym czujnika z jako wartość typu danych. W funkcji zdefiniowanej przez użytkownika można utworzyć wiele zapałek, wysyłając uwierzytelnione żądanie HTTP POST na adres:

```URL
YOUR_MANAGEMENT_API_URL/matchers
```

Z korpusem JSON:

```JSON
{
  "id": "3626464-f39b-46c0-d9b0c-436aysj55",
  "name": "Temperature Matcher",
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "conditions": [
    {
      "id": "ag7gq35cfu3-e15a-4e9c-6437-sj6w68sy44s",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ]
}
```

| Wartość | Zamień na |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Region serwera, w którym jest hostowane używane wystąpienie |

## <a name="create-a-user-defined-function"></a>Tworzenie funkcji zdefiniowanej przez użytkownika

Tworzenie funkcji zdefiniowanej przez użytkownika polega na utworzeniu wieloczęściowego żądania HTTP do interfejsów API zarządzania cyfrowymi obrazkami usługi Azure.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

Po utworzeniu matcherów prześlij fragment funkcji z następującym uwierzytelnionym wieloczęściowym żądaniem HTTP POST do:

```URL
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Użyj następującego obiektu:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "name": "User Defined Function",
  "description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| Wartość | Zamień na |
| --- | --- |
| USER_DEFINED_BOUNDARY | Wieloczęściowa nazwa granicy zawartości |
| YOUR_SPACE_IDENTIFIER | Identyfikator przestrzeni  |
| YOUR_MATCHER_IDENTIFIER | Identyfikator matchera, którego chcesz użyć |

1. Sprawdź, czy nagłówki `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`zawierają: .
1. Sprawdź, czy treść jest wieloczęściowa:

   - Pierwsza część zawiera wymagane metadane funkcji zdefiniowane przez użytkownika.
   - Druga część zawiera logikę obliczeń JavaScript.

1. W sekcji **USER_DEFINED_BOUNDARY** zastąp wartości`YOUR_SPACE_IDENTIFIER` **spaceId** (`YOUR_MATCHER_IDENTIFIER`) i **matchers** ( ).
1. Sprawdź, czy funkcja zdefiniowana przez `Content-Type: text/javascript`użytkownika JavaScript jest dostarczana jako .

### <a name="example-functions"></a>Przykładowe funkcje

Ustaw odczyt telemetryczny czujnika bezpośrednio dla czujnika `sensor.DataType`z typem danych **Temperatura,** która jest:

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

Parametr **telemetrii** udostępnia **sensorid** i **message** atrybuty, odpowiadające wiadomości wysyłanej przez czujnik. Parametr **executionContext** udostępnia następujące atrybuty:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

W następnym przykładzie rejestrujemy komunikat, jeśli odczyt telemetrii czujnika przekracza wstępnie zdefiniowany próg. Jeśli ustawienia diagnostyczne są włączone w wystąpieniu usługi Azure Digital Twins, dzienniki z funkcji zdefiniowanych przez użytkownika są również przekazywane dalej:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

Poniższy kod wyzwala powiadomienie, jeśli poziom temperatury wzrośnie powyżej wstępnie zdefiniowanej stałej:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

Aby uzyskać bardziej złożoną przykładową funkcję zdefiniowaną przez użytkownika, przeczytaj [przewodnik Szybki start obłożenia](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Tworzenie przypisania roli

Utwórz przypisanie roli dla funkcji zdefiniowanej przez użytkownika, która ma być uruchamiana w obszarze. Jeśli dla funkcji zdefiniowanej przez użytkownika nie istnieje żadne przypisanie roli, nie będzie ona miała odpowiednich uprawnień do interakcji z interfejsem API zarządzania ani dostępu do wykonywania akcji na obiektach wykresu. Akcje, które może wykonywać funkcja zdefiniowana przez użytkownika są określone i zdefiniowane za pomocą kontroli dostępu opartej na rolach w interfejsach API zarządzania cyfrowymi obrazkami azure. Na przykład funkcje zdefiniowane przez użytkownika mogą być ograniczone w zakresie, określając niektóre role lub niektóre ścieżki kontroli dostępu. Aby uzyskać więcej informacji, przeczytaj dokumentację [kontroli dostępu opartą na rolach.](./security-role-based-access-control.md)

1. [Kwerenda systemowy interfejs API](./security-create-manage-role-assignments.md#retrieve-all-roles) dla wszystkich ról, aby uzyskać identyfikator roli, który chcesz przypisać do funkcji zdefiniowanej przez użytkownika. Zrób to, składając uwierzytelnione żądanie HTTP GET na adres:

    ```URL
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Zachowaj żądany identyfikator roli. Zostanie on przekazany jako **roleid** atrybutu`YOUR_DESIRED_ROLE_IDENTIFIER`treści JSON ( ) poniżej.

1. **objectId** `YOUR_USER_DEFINED_FUNCTION_ID`( ) będzie identyfikatorem funkcji zdefiniowanej przez użytkownika, który został utworzony wcześniej.
1. Znajdź wartość **ścieżki** `YOUR_ACCESS_CONTROL_PATH`( ) poprzez wykonywanie `fullpath`zapytań o spacje za pomocą pliku .
1. Skopiuj zwróconą `spacePaths` wartość. Użyjesz tego poniżej. Skonserwuj uwierzytelnione żądanie HTTP GET na adres:

    ```URL
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Wartość | Zamień na |
    | --- | --- |
    | YOUR_SPACE_NAME | Nazwa przestrzeni, której chcesz użyć |

1. Wklej zwróconą `spacePaths` wartość do **ścieżki,** aby utworzyć przypisanie roli funkcji zdefiniowanej przez użytkownika, wysyłając uwierzytelnione żądanie HTTP POST na adres:

    ```URL
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    Z korpusem JSON:

    ```JSON
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Wartość | Zamień na |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | Identyfikator żądanej roli |
    | YOUR_USER_DEFINED_FUNCTION_ID | Identyfikator funkcji zdefiniowanej przez użytkownika, której chcesz użyć |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | Identyfikator określający typ funkcji zdefiniowany`UserDefinedFunctionId`przez użytkownika ( ) |
    | YOUR_ACCESS_CONTROL_PATH | Ścieżka kontroli dostępu |

>[!TIP]
> Przeczytaj artykuł [Jak tworzyć przypisania ról i zarządzać nimi, aby](./security-create-manage-role-assignments.md) uzyskać więcej informacji na temat operacji interfejsu API zarządzania funkcjami zdefiniowanymi przez użytkownika i punktów końcowych.

## <a name="send-telemetry-to-be-processed"></a>Wysyłanie danych telemetrycznych do przetworzenia

Czujnik zdefiniowany na wykresie analizy przestrzennej wysyła dane telemetryczne. Z kolei dane telemetryczne wyzwala wykonanie funkcji zdefiniowanej przez użytkownika, która została przekazana. Procesor danych pobiera dane telemetryczne. Następnie tworzony jest plan wykonania dla wywołania funkcji zdefiniowanej przez użytkownika.

1. Pobierz matchery dla czujnika, z na podstawie który został wygenerowany odczyt.
1. W zależności od tego, jakie matchery zostały pomyślnie ocenione, pobierz skojarzone funkcje zdefiniowane przez użytkownika.
1. Wykonaj każdą funkcję zdefiniowaną przez użytkownika.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć punkty końcowe usługi Azure Digital Twins](./how-to-egress-endpoints.md) do wysyłania zdarzeń.

- Aby uzyskać więcej informacji na temat routingu w usłudze Azure Digital Twins, przeczytaj [temat Zdarzenia i wiadomości routingu](./concepts-events-routing.md).

- Przejrzyj [dokumentację referencyjną biblioteki klienta](./reference-user-defined-functions-client-library.md).