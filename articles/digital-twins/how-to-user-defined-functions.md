---
title: Jak używać funkcji zdefiniowanych przez użytkownika w reprezentacji urządzeń cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Wytyczne dotyczące sposobu tworzenia funkcje zdefiniowane przez użytkownika, dopasowujące jednostki i przypisań ról za pomocą Twins cyfrowych platformy Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: 8094965da5fb0a5fad0313fd96e2878f86d78aa7
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215501"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Jak używać funkcji zdefiniowanych przez użytkownika w reprezentacji urządzeń cyfrowych platformy Azure

[Funkcje zdefiniowane przez użytkownika](./concepts-user-defined-functions.md) zezwolić użytkownikom na uruchamianie logiki niestandardowej dla komunikatów przychodzących danych telemetrycznych i metadane przestrzenne programu graph, dzięki czemu użytkownik do wysyłania zdarzeń do wstępnie zdefiniowanych punktów końcowych. W tym przewodniku omówimy przykład działania dotyczące temperatury zdarzeń do wykrywania i alert dotyczący dowolnego odczytu, które przekraczają niektórych temperatury.

W poniższych przykładach `https://yourManagementApiUrl` odwołuje się do identyfikatora URI cyfrowego Twins interfejsów API:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management
```

| Nazwa atrybutu niestandardowego | Zamień |
| --- | --- |
| *yourInstanceName* | Nazwa wystąpienia usługi Azure cyfrowego bliźniaczych reprezentacji |
| *yourLocation* | Który region serwer wystąpienie usługi jest hostowana na |

## <a name="client-library-reference"></a>Dokumentacja biblioteki klienckiej

Funkcje, które są dostępne jako metody pomocnicze w środowisku uruchomieniowym funkcje zdefiniowane przez użytkownika są wyliczane w następującym [odwołania do klienta](#Client-Reference).

## <a name="create-a-matcher"></a>Utwórz dopasowywania

Dopasowujące jednostki są obiektów grafu, które określają, jakie funkcje zdefiniowane przez użytkownika, które zostaną wykonane telemetrii danego komunikatu.

Nieprawidłowa dopasowywania warunek porównania:

- `Equals`
- `NotEquals`
- `Contains`

Nieprawidłowa dopasowywania warunek cele:

- `Sensor`
- `SensorDevice`
- `SensorSpace`

Następujące dopasowywania przykład oceni na wartość true, wszystkie zdarzenia telemetrii czujników z `"Temperature"` jako wartość typu danych. Możesz utworzyć wiele dopasowujące jednostki w funkcji zdefiniowanej przez użytkownika.

```plaintext
POST https://yourManagementApiUrl/api/v1.0/matchers
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
  "SpaceId": "yourSpaceIdentifier"
}
```

| Nazwa atrybutu niestandardowego | Zamień |
| --- | --- |
| *yourManagementApiUrl* | Pełna ścieżka adresu URL dla interfejsu API zarządzania  |
| *yourSpaceIdentifier* | Który region serwer wystąpienie usługi jest hostowana na |

## <a name="create-a-user-defined-function-udf"></a>Tworzenie funkcji zdefiniowanej przez użytkownika (UDF)

Po dopasowujące jednostki zostały utworzone, a następnie przekaż fragment kodu funkcji z następujące wywołanie metody POST:

> [!IMPORTANT]
> - W nagłówkach, ustaw następujące `Content-Type: multipart/form-data; boundary="userDefinedBoundary"`.
> - Treść jest wieloczęściowy:
>   - Pierwsza część dotyczy metadane potrzebne dla funkcji zdefiniowanej przez użytkownika.
>   - Druga część jest logiki obliczeń javascript.
> - W powyższej ścieżce zastąpić `userDefinedBoundary` sekcji `SpaceId` i `Machers` identyfikatorów GUID.

```plaintext
POST https://yourManagementApiUrl/api/v1.0/userdefinedfunctions with Content-Type: multipart/form-data; boundary="userDefinedBoundary"
```

| Nazwa atrybutu niestandardowego | Zamień |
| --- | --- |
| *yourManagementApiUrl* | Pełna ścieżka adresu URL dla interfejsu API zarządzania  |

Treść:

```plaintext
--userDefinedBoundary
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "yourSpaceIdentifier",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["yourMatcherIdentifier"]
}
--userDefinedBoundary
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--userDefinedBoundary--
```

| Nazwa atrybutu niestandardowego | Zamień |
| --- | --- |
| *yourSpaceIdentifier* | Identyfikator miejsca  |
| *yourMatcherIdentifier* | Identyfikator dopasowywania, którego chcesz użyć |

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

*Telemetrii* udostępnia parametru **SensorId** i **komunikat** atrybutów (odpowiadający wiadomością wysłaną przez czujnik). *Kontekście wykonywania* parametru uwidacznia następujące atrybuty:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

W następnym przykładzie firma Microsoft zarejestruje komunikat Jeśli odczytywanie danych telemetrycznych czujnik przekracza wstępnie zdefiniowany próg. Jeśli ustawienia diagnostyki są włączone w wystąpieniu Twins cyfrowych, przekazywane są również dzienników z funkcjami zdefiniowanymi przez użytkownika:

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

Poniższy kod spowodują wygenerowanie powiadomienia, jeśli poziom temperatury wzrośnie powyżej wstępnie zdefiniowanej stałej.

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

Przykładowy kod bardziej złożonych funkcji zdefiniowanej przez użytkownika można znaleźć [Sprawdź dostępne miejsca do magazynowania przy użyciu świeżym powietrzu funkcji zdefiniowanej przez użytkownika](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)

## <a name="create-a-role-assignment"></a>Utwórz przypisanie roli

Musimy utworzyć przypisania roli, funkcji zdefiniowanych przez użytkownika do wykonania w obszarze. Jeśli nie, nie ma odpowiednich uprawnień do interakcji z interfejsem API zarządzania do wykonywania akcji względem obiektów grafu. Akcje, które wykonuje funkcję zdefiniowaną przez użytkownika nie są wykluczone z kontroli dostępu opartej na rolach w ramach cyfrowej interfejsów API zarządzania bliźniaczych reprezentacji. Mogą one ograniczone w zakresie, określając określone role lub niektóre ścieżki kontroli dostępu. Aby uzyskać więcej informacji, zobacz [kontroli dostępu opartej na rolach](./security-role-based-access-control.md) dokumentacji.

1. Zapytanie dla ról i uzyskać identyfikator roli którą chcesz przypisać do funkcji zdefiniowanej przez użytkownika; przekazać go do **RoleId** poniżej.

```plaintext
GET https://yourManagementApiUrl/api/v1.0/system/roles
```

| Nazwa atrybutu niestandardowego | Zamień |
| --- | --- |
| *yourManagementApiUrl* | Pełna ścieżka adresu URL dla interfejsu API zarządzania  |

2. **Identyfikator obiektu** będzie identyfikator funkcji zdefiniowanej przez użytkownika, który został utworzony wcześniej.
3. Znajdź wartość **ścieżki** , badając Twojego miejsca do magazynowania przy użyciu `fullpath`.
4. Skopiuj zwracanego `spacePaths` wartość. Który użyje poniżej.

```plaintext
GET https://yourManagementApiUrl/api/v1.0/spaces?name=yourSpaceName&includes=fullpath
```

| Nazwa atrybutu niestandardowego | Zamień |
| --- | --- |
| *yourManagementApiUrl* | Pełna ścieżka adresu URL dla interfejsu API zarządzania  |
| *yourSpaceName* | Nazwa miejsca, do których chcesz używać |

4. Teraz Wklej zwrócony `spacePaths` wartością do **ścieżki** można utworzyć przypisania roli funkcji zdefiniowanej przez użytkownika.

```plaintext
POST https://yourManagementApiUrl/api/v1.0/roleassignments
{
  "RoleId": "yourDesiredRoleIdentifier",
  "ObjectId": "yourUserDefinedFunctionId",
  "ObjectIdType": "UserDefinedFunctionId",
  "Path": "yourAccessControlPath"
}
```

| Nazwa atrybutu niestandardowego | Zamień |
| --- | --- |
| *yourManagementApiUrl* | Pełna ścieżka adresu URL dla interfejsu API zarządzania  |
| *yourDesiredRoleIdentifier* | Identyfikator odpowiednią rolę |
| *yourUserDefinedFunctionId* | Identyfikator funkcji zdefiniowanej przez użytkownika, którego chcesz użyć |
| *yourAccessControlPath* | Ścieżka kontroli dostępu |

## <a name="send-telemetry-to-be-processed"></a>Wysyłanie danych telemetrycznych do przetworzenia

Dane telemetryczne wygenerowane przez czujnik opisane na wykresie powinno spowodować wykonanie funkcji zdefiniowanej przez użytkownika, który został przekazany. Po dane telemetryczne są wykrywane przez przetwarzający dane, jest plan wykonywania jest tworzona dla wywołania funkcji zdefiniowanej przez użytkownika.

1. Pobierz dopasowujące jednostki dla odczytu została wygenerowana wylogować się z czujnika.
1. W zależności od tego, co dopasowujące jednostki spełnione należy pobrać skojarzone funkcje zdefiniowane przez użytkownika.
1. Wykonywanie poszczególnych funkcji zdefiniowanej przez użytkownika.

## <a name="client-reference"></a>Odwołania do klienta

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Podany identyfikator miejsca pobranie miejsce z wykresu.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *id*  | `guid` | Identyfikator miejsca |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Podany identyfikator czujnik pobiera czujnika z wykresu.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *id*  | `guid` | Identyfikator czujnika |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Podany identyfikator urządzenia pobiera urządzenia z wykresu.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Identyfikator urządzenia |

### <a name="getsensorvaluesensorid-datatype--value"></a>⇒ getSensorValue (sensorId, typ danych) `value`

Podany identyfikator czujników i jego typu danych, pobrać bieżącą wartość dla tego czujnika.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Identyfikator czujnika |
| *Typ danych*  | `string` | Typ danych czujników |

### <a name="getspacevaluespaceid-valuename--value"></a>⇒ getSpaceValue (spaceId, valueName) `value`

Podany identyfikator miejsca i wartość name, pobrać bieżącą wartość tej właściwości miejsca.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Identyfikator miejsca |
| *valueName* | `string` | Nazwa właściwości miejsca |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>⇒ getSensorHistoryValues (sensorId, typ danych) `value[]`

Podany identyfikator czujników i jego typu danych, pobrać historyczne wartości dla tego czujnika.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identyfikator czujnika |
| *Typ danych* | `string` | Typ danych czujników |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>⇒ getSpaceHistoryValues (spaceId, typ danych) `value[]`

Podany identyfikator miejsca i wartość name, pobrać historyczne wartości dla tej właściwości w miejscu.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator miejsca |
| *valueName* | `string` | Nazwa właściwości miejsca |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Podany identyfikator miejsca, pobrać spacje podrzędne dla nadrzędnej miejsca.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator miejsca |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Podany identyfikator miejsca, pobrać czujników podrzędne dla nadrzędnej miejsca.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator miejsca |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Podany identyfikator miejsca, pobrać urządzenia podrzędnych do tego miejsca nadrzędnego.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator miejsca |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Podany identyfikator urządzenia, pobrać czujników podrzędnych dla tego urządzenia nadrzędnego.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *Identyfikator urządzenia* | `guid` | Identyfikator urządzenia |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Podany identyfikator miejsca, pobrać jego przestrzeni nadrzędnej.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Identyfikator miejsca |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Podany identyfikator czujnik, pobrać jego przestrzeni nadrzędnej.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identyfikator czujnika |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Podany identyfikator urządzenia, pobrać jego przestrzeni nadrzędnej.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Identyfikator urządzenia |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Podany identyfikator czujnik, pobrać jego urządzenia nadrzędnego.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identyfikator czujnika |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>⇒ getSpaceExtendedProperty (spaceId, propertyName) `extendedProperty`

Podany identyfikator miejsca, pobrać właściwości i jego wartość z obszaru.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator miejsca |
| *propertyName* | `string` | Nazwa właściwości miejsca |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>⇒ getSensorExtendedProperty (sensorId, propertyName) `extendedProperty`

Podany identyfikator czujnik, pobieranie właściwości i jego wartość z czujnika.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identyfikator czujnika |
| *propertyName* | `string` | Nazwa właściwości czujnika |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>⇒ getDeviceExtendedProperty (deviceId, propertyName) `extendedProperty`

Podany identyfikator urządzenia, pobieranie właściwości i jego wartość z urządzenia.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *Identyfikator urządzenia* | `guid` | Identyfikator urządzenia |
| *propertyName* | `string` | Nazwa właściwości urządzenia |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue (sensorId, typ danych, wartość)

Ustawia wartość w obiekcie czujników przy użyciu danego typu danych.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identyfikator czujnika |
| *Typ danych*  | `string` | Typ danych czujników |
| *value*  | `string` | wartość |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue (spaceId, typ danych, wartość)

Ustawia wartość w obiekcie miejsca przy użyciu danego typu danych.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator miejsca |
| *Typ danych* | `string` | typ danych |
| *value* | `string` | wartość |

### <a name="logmessage"></a>log(Message)

Rejestruje komunikat w funkcji zdefiniowanej przez użytkownika.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *Komunikat* | `string` | komunikat do zarejestrowania |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification (topologyObjectId, topologyObjectType ładunku)

Wysyła niestandardowe powiadomienie do wysyłki.

**Rodzaj**: funkcja globalna

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Wykres identyfikatora obiektu (np.) miejsce / identyfikator /device czujnik)|
| *topologyObjectType*  | `string` | (np.) miejsce / czujnika / urządzenia)|
| *ładunek*  | `string` | ładunek JSON do wysłania z powiadomieniem |

## <a name="return-types"></a>Typy zwracane

Dostępne są następujące modele opisujące obiektów zwrotnych z powyższych odwołania do klienta.

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

Zwraca miejsce nadrzędny bieżącego miejsca.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Zwraca element podrzędny czujników bieżącego miejsca.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

Zwraca element podrzędny urządzeń bieżącego miejsca.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Zwraca wartość właściwości rozszerzonej i ich wartości w bieżącej przestrzeni.

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nazwa właściwości rozszerzonej |

#### <a name="valuevaluename--value"></a>Value(VALUENAME) ⇒ `value`

Zwraca wartość bieżącego miejsca.

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nazwa wartości |

#### <a name="historyvaluename--value"></a>History(VALUENAME) ⇒ `value[]`

Zwraca wartości historycznych bieżącego miejsca.

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nazwa wartości |

#### <a name="notifypayload"></a>Notify(Payload)

Wysyła powiadomienie przy użyciu określonego ładunku.

| Param  | Typ                | Opis  |
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

Zwraca przestrzeni nadrzędny bieżącego urządzenia.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Zwraca element podrzędny czujników bieżące urządzenie.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Zwraca wartość właściwości rozszerzonej i jego wartość, aby uzyskać bieżące urządzenie.

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nazwa właściwości rozszerzonej |

#### <a name="notifypayload"></a>Notify(Payload)

Wysyła powiadomienie przy użyciu określonego ładunku.

| Param  | Typ                | Opis  |
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

Zwraca miejsce nadrzędny bieżącego czujnika.

#### <a name="device--device"></a>Device() ⇒ `device`

Przywraca na urządzeniu nadrzędny bieżącego czujnika.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Zwraca wartość właściwości rozszerzonej i jego wartość dla bieżącego czujnika.

| Param  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nazwa właściwości rozszerzonej |

#### <a name="value--value"></a>Value() ⇒ `value`

Zwraca wartość bieżącej czujnika.

#### <a name="history--value"></a>History() ⇒ `value[]`

Zwraca wartości historycznych czujników bieżącego.

#### <a name="notifypayload"></a>Notify(Payload)

Wysyła powiadomienie przy użyciu określonego ładunku.

| Param  | Typ                | Opis  |
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

Aby dowiedzieć się, jak utworzyć reprezentacji urządzeń cyfrowych punkty końcowe, do wysyłania zdarzeń do, przeczytaj [punktów końcowych utworzyć reprezentacji urządzeń cyfrowych](how-to-egress-endpoints.md).

Aby uzyskać szczegółowe informacje na temat punktów końcowych cyfrowego bliźniaczych reprezentacji, przeczytaj [Dowiedz się więcej o punktach końcowych](concepts-events-routing.md).
