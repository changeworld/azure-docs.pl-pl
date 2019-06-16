---
title: Azure Twins cyfrowego zdefiniowanych przez użytkownika funkcje dokumentacja biblioteki klienckiej | Dokumentacja firmy Microsoft
description: Azure Twins cyfrowego zdefiniowanych przez użytkownika funkcji informacje o bibliotece klienta.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 06/06/2019
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: be05cec8e3d755f1b04e5ecc5ec7c740053a74d4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073033"
---
# <a name="user-defined-functions-client-library-reference"></a>Dokumentacja biblioteki klienckiej funkcje zdefiniowane przez użytkownika

Ten dokument zawiera informacje dotyczące biblioteki klienckiej funkcje zdefiniowane przez użytkownika Twins cyfrowych platformy Azure.

## <a name="helper-methods"></a>Metody pomocnicze

Biblioteka klienta definiuje metody pomocnicze dla typowych operacji.

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
| *dataType*  | `string` | Typ danych czujników |

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
| *dataType* | `string` | Typ danych czujników |

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
| *deviceId* | `guid` | Identyfikator urządzenia |

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
| *deviceId* | `guid` | Identyfikator urządzenia |
| *propertyName* | `string` | Nazwa właściwości urządzenia |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue (sensorId, typ danych, wartość)

Ta funkcja umożliwia ustawienie wartości w obiekcie czujników przy użyciu danego typu danych.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identyfikator czujnika |
| *dataType*  | `string` | Typ danych czujników |
| *value*  | `string` | Wartość |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue (spaceId, typ danych, wartość)

Ta funkcja umożliwia ustawienie wartości w obiekcie miejsca przy użyciu danego typu danych.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator miejsca |
| *dataType* | `string` | Typ danych |
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
| *payload*  | `string` | Ładunek JSON do wysłania powiadomień wysyłanych. |

## <a name="return-types"></a>Typy zwracane

Modele odpowiedzi zwrócony z metody pomocnika odwołań klienta zostały opisane poniżej.

### <a name="space"></a>Przestrzeń kosmiczna

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "Space",
  "friendlyName": "Conference Room",
  "typeId": 0,
  "parentSpaceId": "00000000-0000-0000-0000-000000000001",
  "subtypeId": 0
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
| *payload* | `string` | Ładunek JSON, które mają zostać objęte powiadomienia |

### <a name="device"></a>Urządzenie

```JSON
{
  "id": "00000000-0000-0000-0000-000000000002",
  "name": "Device",
  "friendlyName": "Temperature Sensing Device",
  "description": "This device contains a sensor that captures temperature readings.",
  "type": "None",
  "subtype": "None",
  "typeId": 0,
  "subtypeId": 0,
  "hardwareId": "ABC123",
  "gatewayId": "ABC",
  "spaceId": "00000000-0000-0000-0000-000000000000"
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
| *payload* | `string` | Ładunek JSON, które mają zostać objęte powiadomienia |

### <a name="sensor"></a>Czujnik

```JSON
{
  "id": "00000000-0000-0000-0000-000000000003",
  "port": "30",
  "pollRate": 3600,
  "dataType": "Temperature",
  "dataSubtype": "None",
  "type": "Classic",
  "portType": "None",
  "dataUnitType": "FahrenheitTemperature",
  "spaceId": "00000000-0000-0000-0000-000000000000",
  "deviceId": "00000000-0000-0000-0000-000000000001",
  "portTypeId": 0,
  "dataUnitTypeId": 0,
  "dataTypeId": 0,
  "dataSubtypeId": 0,
  "typeId": 0  
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
| *payload* | `string` | Ładunek JSON, które mają zostać objęte powiadomienia |

### <a name="value"></a>Wartość

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>Właściwość rozszerzona

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [funkcje zdefiniowane przez użytkownika Twins cyfrowego Azure](./concepts-user-defined-functions.md).

- Dowiedz się, [jak tworzyć funkcje zdefiniowane przez użytkownika](./how-to-user-defined-functions.md).

- Dowiedz się, [jak debugować funkcje zdefiniowane przez użytkownika](./how-to-diagnose-user-defined-functions.md).
