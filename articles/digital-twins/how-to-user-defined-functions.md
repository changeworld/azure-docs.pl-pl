---
title: Jak utworzyć funkcje zdefiniowane przez użytkownika w reprezentacji urządzeń cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Jak tworzyć funkcje zdefiniowane przez użytkownika, dopasowujące jednostki i przypisań ról w reprezentacji urządzeń cyfrowych platformy Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: 7208f96d99127247b51510e0c43c1733bb327dfb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60921886"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Jak utworzyć funkcje zdefiniowane przez użytkownika w reprezentacji urządzeń cyfrowych platformy Azure

[Funkcje zdefiniowane przez użytkownika](./concepts-user-defined-functions.md) umożliwiają użytkownikom konfigurowanie logikę niestandardową do wykonania z komunikatów przychodzących danych telemetrycznych i przestrzennych grafu metadanych. Użytkownicy mogą również wysyłać zdarzenia do wstępnie zdefiniowanych [punktów końcowych](./how-to-egress-endpoints.md).

Ten przewodnik przeprowadzi uzyskać przykład pokazujący, jak wykrywać i alert dotyczący dowolnego odczytu, które przekraczają niektórych temperatury, temperatury odebranych zdarzeń.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Dokumentacja biblioteki klienckiej

Funkcje dostępne jako metody pomocnicze w środowisku uruchomieniowym funkcje zdefiniowane przez użytkownika są widoczne w [informacje o bibliotece klienta](./reference-user-defined-functions-client-library.md) dokumentu.

## <a name="create-a-matcher"></a>Utwórz dopasowywania

Dopasowujące jednostki są obiektami programu graph, określających, funkcje zdefiniowane przez użytkownika Uruchom telemetrii danego komunikatu.

- Nieprawidłowa dopasowywania warunek porównania:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Nieprawidłowa dopasowywania warunek cele:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

Następujące dopasowywania przykład zwraca wartość true na wszelkie zdarzenia telemetrii czujników z `"Temperature"` jako wartość typu danych. Możesz utworzyć wiele dopasowujące jednostki w funkcji zdefiniowanej przez użytkownika, wprowadzając uwierzytelnionego żądania HTTP POST do:

```plaintext
YOUR_MANAGEMENT_API_URL/matchers
```

Za pomocą treść kodu JSON:

```JSON
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| Wartość | Zamień na |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Region serwera, w którym jest hostowane używane wystąpienie |

## <a name="create-a-user-defined-function"></a>Tworzenie funkcji zdefiniowanej przez użytkownika

Tworzenie funkcji zdefiniowanej przez użytkownika polega na utworzeniu wieloczęściowego żądania HTTP do cyfrowego Twins zarządzania interfejsów API usługi Azure.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

Po utworzeniu dopasowujące jednostki, Przekaż fragment kodu funkcji z następujących uwierzytelnionego wieloczęściowego żądanie HTTP POST do:

```plaintext
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Użyj następujących treści:

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
| USER_DEFINED_BOUNDARY | Nazwa granice zawartości wieloczęściowej wiadomości |
| YOUR_SPACE_IDENTIFIER | Identyfikator miejsca  |
| YOUR_MATCHER_IDENTIFIER | Identyfikator dopasowywania, którego chcesz użyć |

1. Sprawdź, czy nagłówki obejmują: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Sprawdź, czy treści wieloczęściowej wiadomości:

   - Pierwsza część zawiera metadane wymagane funkcji zdefiniowanej przez użytkownika.
   - Druga część zawiera logikę obliczeń języka JavaScript.

1. W **USER_DEFINED_BOUNDARY** sekcji i Zastąp **spaceId** (`YOUR_SPACE_IDENTIFIER`) i **dopasowujące jednostki** (`YOUR_MATCHER_IDENTIFIER`) wartości.
1. Sprawdź, czy funkcja zdefiniowana przez użytkownika języka JavaScript jest dostarczany jako `Content-Type: text/javascript`.

### <a name="example-functions"></a>Przykład funkcji

Ustaw telemetrii czujnik odczytu bezpośrednio dla czujnika z typem danych **temperatury**, czyli `sensor.DataType`:

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

**Telemetrii** udostępnia parametru **SensorId** i **komunikat** atrybutów, odpowiadający wiadomością wysłaną przez czujnik. **Kontekście wykonywania** parametru uwidacznia następujące atrybuty:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

W następnym przykładzie mamy dziennika komunikat w przypadku odczytywanie danych telemetrycznych czujnik przekracza wstępnie zdefiniowany próg. Jeśli ustawienia diagnostyki są włączone w wystąpieniu Twins cyfrowych platformy Azure, zostają przekazane dzienniki z funkcjami zdefiniowanymi przez użytkownika:

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

Dla bardziej złożonego przykładu kodu funkcji zdefiniowanej przez użytkownika, zobacz [Szybki Start zajętość](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Utwórz przypisanie roli

Utwórz przypisanie roli, funkcji zdefiniowanych przez użytkownika, uruchamiany w kontekście. Jeśli nie przypisania roli istnieje funkcja zdefiniowana przez użytkownika, nie będzie mieć odpowiednie uprawnienia do interakcji z interfejsem API zarządzania, albo mieć dostęp do wykonywania akcji względem obiektów grafu. Akcje, które może wykonywać funkcji zdefiniowanej przez użytkownika są określone i zdefiniowanych za pomocą kontroli dostępu opartej na rolach w ramach cyfrowej Twins zarządzania interfejsów API usługi Azure. Na przykład funkcje zdefiniowane przez użytkownika można ograniczyć, w zakresie, określając określone role lub niektóre ścieżki kontroli dostępu. Aby uzyskać więcej informacji, zobacz [kontroli dostępu opartej na rolach](./security-role-based-access-control.md) dokumentacji.

1. [Zapytanie interfejsu API systemu](./security-create-manage-role-assignments.md#all) dla wszystkich ról uzyskać identyfikator roli, którą chcesz przypisać do funkcji zdefiniowanych przez użytkownika. To zrobić, wprowadzając uwierzytelnionego żądania HTTP GET do:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Zachowaj identyfikator odpowiednią rolę. Zostaną przekazane jako atrybut treść JSON **roleId** (`YOUR_DESIRED_ROLE_IDENTIFIER`) poniżej.

1. **Identyfikator obiektu** (`YOUR_USER_DEFINED_FUNCTION_ID`) będzie identyfikator funkcji zdefiniowanej przez użytkownika, który został utworzony wcześniej.
1. Znajdź wartość **ścieżki** (`YOUR_ACCESS_CONTROL_PATH`), badając Twojego miejsca do magazynowania przy użyciu `fullpath`.
1. Skopiuj zwracanego `spacePaths` wartość. Użyjesz, poniżej. Wprowadź uwierzytelnionego żądania HTTP GET do:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Wartość | Zamień na |
    | --- | --- |
    | YOUR_SPACE_NAME | Nazwa miejsca, do których chcesz używać |

1. Wklej zwrócony `spacePaths` wartością do **ścieżki** można utworzyć przypisania roli funkcji zdefiniowanej przez użytkownika, wprowadzając uwierzytelnionego żądania HTTP POST do:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    Za pomocą treść kodu JSON:

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
    | YOUR_DESIRED_ROLE_IDENTIFIER | Identyfikator odpowiednią rolę |
    | YOUR_USER_DEFINED_FUNCTION_ID | Identyfikator funkcji zdefiniowanej przez użytkownika, którego chcesz użyć |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | Identyfikator określający typ funkcji zdefiniowanej przez użytkownika |
    | YOUR_ACCESS_CONTROL_PATH | Ścieżka kontroli dostępu |

>[!TIP]
> Przeczytaj artykuł [jak tworzyć i zarządzać przypisaniami ról](./security-create-manage-role-assignments.md) Aby uzyskać więcej informacji na temat funkcji zdefiniowanej przez użytkownika operacji interfejsu API usługi zarządzania i punktów końcowych.

## <a name="send-telemetry-to-be-processed"></a>Wysyłanie danych telemetrycznych do przetworzenia

Czujnik zdefiniowane na wykresie analizy przestrzennej wysyła dane telemetryczne. Z kolei telemetrii wyzwala wykonanie funkcji zdefiniowanej przez użytkownika, który został przekazany. Procesor danych przejmuje dane telemetryczne. Plan wykonania jest tworzona dla wywołania funkcji zdefiniowanej przez użytkownika.

1. Pobierz dopasowujące jednostki dla odczytu została wygenerowana z czujnika.
1. W zależności od tego, jakie dopasowujące jednostki zostały spełnione należy pobrać skojarzone funkcje zdefiniowane przez użytkownika.
1. Wykonywanie poszczególnych funkcji zdefiniowanej przez użytkownika.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzyć punkty końcowe Azure cyfrowego bliźniaczych reprezentacji](./how-to-egress-endpoints.md) do wysyłania zdarzeń do.

- Aby uzyskać więcej szczegółów na temat routingu w reprezentacji urządzeń cyfrowych platformy Azure, przeczytaj [Routing zdarzeń i komunikatów](./concepts-events-routing.md).

- Przegląd [klienta biblioteki dokumentacji](./reference-user-defined-functions-client-library.md).
