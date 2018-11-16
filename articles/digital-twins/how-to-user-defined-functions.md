---
title: Jak używać funkcji zdefiniowanych przez użytkownika w reprezentacji urządzeń cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Wytyczne dotyczące sposobu tworzenia funkcje zdefiniowane przez użytkownika, dopasowujące jednostki i przypisań ról za pomocą Twins cyfrowych platformy Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: alinast
ms.openlocfilehash: 6a757dca48dc3ff41adfe6f8802fad40e7a4ca81
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636836"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Jak używać funkcji zdefiniowanych przez użytkownika w reprezentacji urządzeń cyfrowych platformy Azure

[Funkcje zdefiniowane przez użytkownika](./concepts-user-defined-functions.md) (UDF) umożliwiają użytkownikowi uruchamiać logikę niestandardową dla wiadomości przychodzących danych telemetrycznych i przestrzennych grafu metadanych. Następnie użytkownik może wysłać zdarzenia do wstępnie zdefiniowanych punktów końcowych. Ten przewodnik przeprowadzi przykład działania dotyczące temperatury zdarzeń do wykrywania i alert dotyczący dowolnego odczytu, które przekraczają niektórych temperatury.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Dokumentacja biblioteki klienckiej

Funkcje, które są dostępne jako metody pomocnicze w środowisku uruchomieniowym funkcje zdefiniowane przez użytkownika są wymienione w [odwołania do klienta](#Client-Reference) sekcji.

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

Następujące dopasowywania przykład zwraca wartość true na wszelkie zdarzenia telemetrii czujników z `"Temperature"` jako wartość typu danych. Możesz utworzyć wiele dopasowujące jednostki w funkcji zdefiniowanej przez użytkownika:

```plaintext
POST YOUR_MANAGEMENT_API_URL/matchers
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
| YOUR_SPACE_IDENTIFIER | Który region serwer wystąpienie usługi jest hostowana na |

## <a name="create-a-user-defined-function-udf"></a>Tworzenie funkcji zdefiniowanej przez użytkownika (UDF)

Po utworzeniu dopasowujące jednostki przekazywanie fragmentu kodu funkcji z następującymi **WPIS** wywołania:

> [!IMPORTANT]
> - W nagłówkach, ustaw następujące `Content-Type: multipart/form-data; boundary="userDefinedBoundary"`.
> - Treść jest wieloczęściowej wiadomości:
>   - Pierwsza część dotyczy metadane potrzebne dla funkcji zdefiniowanej przez użytkownika.
>   - Druga część jest logiki obliczeń JavaScript.
> - W **USER_DEFINED_BOUNDARY** sekcji i Zastąp **SpaceId** i **Machers** wartości.

```plaintext
POST YOUR_MANAGEMENT_API_URL/userdefinedfunctions with Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"
```

| Wartość parametru | Zamień na |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | Nazwa granice zawartości wieloczęściowej wiadomości |

### <a name="body"></a>Treść

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "YOUR_SPACE_IDENTIFIER",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["YOUR_MATCHER_IDENTIFIER"]
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
| YOUR_SPACE_IDENTIFIER | Identyfikator miejsca  |
| YOUR_MATCHER_IDENTIFIER | Identyfikator dopasowywania, którego chcesz użyć |

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

Dla bardziej złożonego przykładu kodu funkcji zdefiniowanej przez użytkownika [Sprawdź dostępne miejsca do magazynowania przy użyciu świeżym powietrzu funkcji zdefiniowanej przez użytkownika](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Utwórz przypisanie roli

Musimy utworzyć przypisania roli dla funkcji zdefiniowanej przez użytkownika działała. Jeśli firma Microsoft nie, nie będzie mieć odpowiednie uprawnienia do interakcji z interfejsem API zarządzania do wykonywania akcji względem obiektów grafu. Akcje, które wykonuje funkcję zdefiniowaną przez użytkownika nie są wykluczone z kontroli dostępu opartej na rolach w ramach cyfrowej Twins zarządzania interfejsów API usługi Azure. Mogą one ograniczone w zakresie, określając określone role lub niektóre ścieżki kontroli dostępu. Aby uzyskać więcej informacji, zobacz [kontroli dostępu opartej na rolach](./security-role-based-access-control.md) dokumentacji.

1. Zapytanie dla ról i Uzyskaj identyfikator roli którą chcesz przypisać do funkcji zdefiniowanej przez użytkownika. Przekazać go do **RoleId**:

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/system/roles
    ```

1. **Identyfikator obiektu** będzie identyfikator funkcji zdefiniowanej przez użytkownika, który został utworzony wcześniej.
1. Znajdź wartość **ścieżki** , badając Twojego miejsca do magazynowania przy użyciu `fullpath`.
1. Skopiuj zwracanego `spacePaths` wartość. Użyjesz, w poniższym kodzie:

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Wartość parametru | Zamień na |
    | --- | --- |
    | *YOUR_SPACE_NAME* | Nazwa miejsca, do których chcesz używać |

1. Wklej zwrócony `spacePaths` wartością do **ścieżki** można utworzyć przypisania roli funkcji zdefiniowanej przez użytkownika:

    ```plaintext
    POST YOUR_MANAGEMENT_API_URL/roleassignments
    {
      "RoleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "ObjectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "ObjectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "Path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Wartość | Zamień na |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | Identyfikator odpowiednią rolę |
    | YOUR_USER_DEFINED_FUNCTION_ID | Identyfikator funkcji zdefiniowanej przez użytkownika, którego chcesz użyć |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | Identyfikator określający typ funkcji zdefiniowanej przez użytkownika |
    | YOUR_ACCESS_CONTROL_PATH | Ścieżka kontroli dostępu |

## <a name="send-telemetry-to-be-processed"></a>Wysyłanie danych telemetrycznych do przetworzenia

Dane telemetryczne wygenerowane przez czujnik opisane na wykresie wyzwala działanie funkcji zdefiniowanej przez użytkownika, który został przekazany. Procesor danych przejmuje dane telemetryczne. Plan wykonywania jest tworzona dla wywołania funkcji zdefiniowanej przez użytkownika.

1. Pobierz dopasowujące jednostki dla odczytu została wygenerowana wylogować się z czujnika.
1. W zależności od tego, co dopasowujące jednostki spełnione należy pobrać skojarzone funkcje zdefiniowane przez użytkownika.
1. Uruchom każda funkcja zdefiniowana przez użytkownika.

## <a name="client-reference"></a>Odwołania do klienta

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Podany identyfikator miejsca, ta funkcja pobiera miejsce z wykresu.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Identyfikator miejsca |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Podany identyfikator czujnik, ta funkcja pobiera czujnika z wykresu.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Identyfikator czujnika |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Podany identyfikator urządzenia, ta funkcja pobiera urządzenia z wykresu.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Identyfikator urządzenia |

### <a name="getsensorvaluesensorid-datatype--value"></a>⇒ getSensorValue (sensorId, typ danych) `value`

Podany identyfikator czujników i jego typu danych, ta funkcja pobiera bieżącą wartość dla tego czujnika.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Identyfikator czujnika |
| *Typ danych*  | `string` | Typ danych czujników |

### <a name="getspacevaluespaceid-valuename--value"></a>⇒ getSpaceValue (spaceId, valueName) `value`

Podany identyfikator miejsca i wartość name, ta funkcja pobiera bieżącą wartość tej właściwości miejsca.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Identyfikator miejsca |
| *valueName* | `string` | Nazwa właściwości miejsca |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>⇒ getSensorHistoryValues (sensorId, typ danych) `value[]`

Podany identyfikator czujników i jego typu danych, ta funkcja pobiera historycznych wartości dla tego czujnika.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identyfikator czujnika |
| *Typ danych* | `string` | Typ danych czujników |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>⇒ getSpaceHistoryValues (spaceId, typ danych) `value[]`

Podany identyfikator miejsca i wartość name, ta funkcja pobiera historycznych wartości dla tej właściwości w miejscu.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator miejsca |
| *valueName* | `string` | Nazwa właściwości miejsca |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Podany identyfikator miejsca, ta funkcja pobiera spacje podrzędne dla nadrzędnej miejsca.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator miejsca |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Podany identyfikator miejsca, ta funkcja pobiera czujników podrzędne dla nadrzędnej miejsca.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator miejsca |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Podany identyfikator miejsca, ta funkcja pobiera urządzenia podrzędnych do tego miejsca nadrzędnego.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator miejsca |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Podany identyfikator urządzenia, ta funkcja pobiera czujników podrzędnych dla tego urządzenia nadrzędnego.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *Identyfikator urządzenia* | `guid` | Identyfikator urządzenia |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Podany identyfikator miejsca, ta funkcja pobiera jego przestrzeni nadrzędnej.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Identyfikator miejsca |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Podany identyfikator czujnik, ta funkcja pobiera jego przestrzeni nadrzędnej.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identyfikator czujnika |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Podany identyfikator urządzenia, ta funkcja pobiera jego przestrzeni nadrzędnej.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Identyfikator urządzenia |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Podany identyfikator czujnik, ta funkcja pobiera jego urządzenia nadrzędnego.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identyfikator czujnika |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>⇒ getSpaceExtendedProperty (spaceId, propertyName) `extendedProperty`

Podany identyfikator miejsca, ta funkcja pobiera właściwości i jego wartość z obszaru.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator miejsca |
| *propertyName* | `string` | Nazwa właściwości miejsca |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>⇒ getSensorExtendedProperty (sensorId, propertyName) `extendedProperty`

Podany identyfikator czujnik, ta funkcja pobiera właściwości i jego wartość z czujnika.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identyfikator czujnika |
| *propertyName* | `string` | Nazwa właściwości czujnika |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>⇒ getDeviceExtendedProperty (deviceId, propertyName) `extendedProperty`

Podany identyfikator urządzenia, ta funkcja pobiera właściwości i jego wartość z urządzenia.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *Identyfikator urządzenia* | `guid` | Identyfikator urządzenia |
| *propertyName* | `string` | Nazwa właściwości urządzenia |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue (sensorId, typ danych, wartość)

Ta funkcja umożliwia ustawienie wartości w obiekcie czujników przy użyciu danego typu danych.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identyfikator czujnika |
| *Typ danych*  | `string` | Typ danych czujników |
| *value*  | `string` | Wartość |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue (spaceId, typ danych, wartość)

Ta funkcja umożliwia ustawienie wartości w obiekcie miejsca przy użyciu danego typu danych.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator miejsca |
| *Typ danych* | `string` | Typ danych |
| *value* | `string` | Wartość |

### <a name="logmessage"></a>log(Message)

Ta funkcja rejestruje następujący komunikat w funkcji zdefiniowanej przez użytkownika.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *Komunikat* | `string` | komunikat do zarejestrowania |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification (topologyObjectId, topologyObjectType ładunku)

Ta funkcja wysyła niestandardowe powiadomienie do wysyłki.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Wykres identyfikatora obiektu. Przykłady to miejsce, czujników i identyfikator urządzenia.|
| *topologyObjectType*  | `string` | Przykładami są czujników i urządzeń.|
| *ładunek*  | `string` | Ładunek JSON do wysłania powiadomień wysyłanych. |

## <a name="return-types"></a>Typy zwracane

Następujące modele opisują obiektów zwrotnych z poprzednim odwołania do klienta.

### <a name="space"></a>Przestrzeń kosmiczna

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000000",
  "Name": "Space",
  "FriendlyName": "Conference Room",
  "TypeId": 0,
  "ParentSpaceId": "00000000-0000-0000-0000-000000000001",
  "SubtypeId": 0
}
```

### <a name="space-methods"></a>Metody miejsca

#### <a name="parent--space"></a>Parent() ⇒ `space`

Ta funkcja zwraca miejsce nadrzędny bieżącego miejsca.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Ta funkcja zwraca element podrzędny czujników bieżącego miejsca.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

Ta funkcja zwraca element podrzędny urządzeń bieżącego miejsca.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Ta funkcja zwraca właściwości rozszerzonej i ich wartości w bieżącej przestrzeni.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nazwa właściwości rozszerzonej |

#### <a name="valuevaluename--value"></a>Value(VALUENAME) ⇒ `value`

Ta funkcja zwraca wartość bieżącego miejsca.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nazwa wartości |

#### <a name="historyvaluename--value"></a>History(VALUENAME) ⇒ `value[]`

Ta funkcja zwraca wartościami historycznymi bieżącego miejsca.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nazwa wartości |

#### <a name="notifypayload"></a>Notify(Payload)

Ta funkcja wysyła powiadomienie przy użyciu określonego ładunku.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *ładunek* | `string` | Ładunek JSON, które mają zostać objęte powiadomienia |

### <a name="device"></a>Urządzenie

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000002",
  "Name": "Device",
  "FriendlyName": "Temperature Sensing Device",
  "Description": "This device contains a sensor that captures temperature readings.",
  "Type": "None",
  "Subtype": "None",
  "TypeId": 0,
  "SubtypeId": 0,
  "HardwareId": "ABC123",
  "GatewayId": "ABC",
  "SpaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>Metody urządzenia

#### <a name="parent--space"></a>Parent() ⇒ `space`

Ta funkcja zwraca przestrzeni nadrzędny bieżącego urządzenia.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Ta funkcja zwraca element podrzędny czujników bieżące urządzenie.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Ta funkcja zwraca właściwości rozszerzonej i jego wartość, aby uzyskać bieżące urządzenie.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nazwa właściwości rozszerzonej |

#### <a name="notifypayload"></a>Notify(Payload)

Ta funkcja wysyła powiadomienie przy użyciu określonego ładunku.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *ładunek* | `string` | Ładunek JSON, które mają zostać objęte powiadomienia |

### <a name="sensor"></a>Czujnik

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000003",
  "Port": "30",
  "PollRate": 3600,
  "DataType": "Temperature",
  "DataSubtype": "None",
  "Type": "Classic",
  "PortType": "None",
  "DataUnitType": "FahrenheitTemperature",
  "SpaceId": "00000000-0000-0000-0000-000000000000",
  "DeviceId": "00000000-0000-0000-0000-000000000001",
  "PortTypeId": 0,
  "DataUnitTypeId": 0,
  "DataTypeId": 0,
  "DataSubtypeId": 0,
  "TypeId": 0  
}
```

### <a name="sensor-methods"></a>Metody czujnika

#### <a name="space--space"></a>Space() ⇒ `space`

Ta funkcja zwraca miejsce nadrzędny bieżącego czujnika.

#### <a name="device--device"></a>Device() ⇒ `device`

Ta funkcja zwraca urządzenia nadrzędnego bieżącego czujnika.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Ta funkcja zwraca właściwości rozszerzonej i jego wartość dla bieżącego czujnika.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nazwa właściwości rozszerzonej |

#### <a name="value--value"></a>Value() ⇒ `value`

Ta funkcja zwraca wartość bieżącej czujnika.

#### <a name="history--value"></a>History() ⇒ `value[]`

Ta funkcja zwraca wartościami historycznymi bieżącego czujnika.

#### <a name="notifypayload"></a>Notify(Payload)

Ta funkcja wysyła powiadomienie przy użyciu określonego ładunku.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *ładunek* | `string` | Ładunek JSON, które mają zostać objęte powiadomienia |

### <a name="value"></a>Wartość

```JSON
{
  "DataType": "Temperature",
  "Value": "70",
  "CreatedTime": ""
}
```

### <a name="extended-property"></a>Właściwość rozszerzona

```JSON
{
  "Name": "OccupancyStatus",
  "Value": "Occupied"
}
```

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzyć punkty końcowe Azure cyfrowego bliźniaczych reprezentacji](how-to-egress-endpoints.md) do wysyłania zdarzeń do.

- Aby uzyskać szczegółowe informacje na temat punktów końcowych Twins cyfrowych platformy Azure, Dowiedz się, [więcej informacji na temat punktów końcowych](concepts-events-routing.md).
