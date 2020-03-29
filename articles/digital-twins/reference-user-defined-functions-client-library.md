---
title: Odwołanie do biblioteki klienta funkcji zdefiniowanych przez użytkownika — Usługi Azure Digital Twins | Dokumenty firmy Microsoft
description: Dokumentacja referencyjna biblioteki klienta zdefiniowanych przez usługę Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: bd6095daca51ddca0cfb4b34ca86e763df9a3d02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276812"
---
# <a name="user-defined-functions-client-library-reference"></a>Odwołanie do biblioteki klienta funkcji zdefiniowanych przez użytkownika

Ten dokument zawiera informacje referencyjne dla biblioteki klienta funkcji zdefiniowanych przez użytkownika usługi Azure Digital Twins.

## <a name="helper-methods"></a>Metody pomocnicze

Biblioteka klienta definiuje metody pomocnicze dla często używanych operacji.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒`space`

Biorąc pod uwagę identyfikator spacji, ta funkcja pobiera spację z wykresu.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ---------- | ------------------- | ------------ |
| *Identyfikator*  | `guid` | Identyfikator miejsca |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒`sensor`

Biorąc pod uwagę identyfikator czujnika, funkcja ta pobiera czujnik z wykresu.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ---------- | ------------------- | ------------ |
| *Identyfikator*  | `guid` | Identyfikator czujnika |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒`device`

Biorąc pod uwagę identyfikator urządzenia, ta funkcja pobiera urządzenie z wykresu.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *Identyfikator* | `guid` | Identyfikator urządzenia |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒`value`

Biorąc pod uwagę identyfikator czujnika i jego typ danych, ta funkcja pobiera bieżącą wartość dla tego czujnika.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Identyfikator czujnika |
| *Datatype*  | `string` | Typ danych czujnika |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒`value`

Biorąc pod uwagę identyfikator spacji i nazwę wartości, ta funkcja pobiera bieżącą wartość dla tej właściwości spacji.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId (ida przestrzeni)*  | `guid` | Identyfikator miejsca |
| *valueName* | `string` | Nazwa właściwości spacji |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒`value[]`

Biorąc pod uwagę identyfikator czujnika i jego typ danych, ta funkcja pobiera wartości historyczne dla tego czujnika.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identyfikator czujnika |
| *Datatype* | `string` | Typ danych czujnika |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒`value[]`

Biorąc pod uwagę identyfikator spacji i nazwę wartości, ta funkcja pobiera wartości historyczne dla tej właściwości w spacji.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId (ida przestrzeni)* | `guid` | Identyfikator miejsca |
| *valueName* | `string` | Nazwa właściwości spacji |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒`space[]`

Biorąc pod uwagę identyfikator spacji, ta funkcja pobiera przestrzenie podrzędne dla tego obszaru nadrzędnego.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId (ida przestrzeni)* | `guid` | Identyfikator miejsca |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒`sensor[]`

Biorąc pod uwagę identyfikator miejsca, ta funkcja pobiera czujniki podrzędne dla tej przestrzeni nadrzędnej.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId (ida przestrzeni)* | `guid` | Identyfikator miejsca |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒`device[]`

Biorąc pod uwagę identyfikator miejsca, ta funkcja pobiera urządzenia podrzędne dla tego obszaru nadrzędnego.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId (ida przestrzeni)* | `guid` | Identyfikator miejsca |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒`sensor[]`

Biorąc pod uwagę identyfikator urządzenia, ta funkcja pobiera czujniki podrzędne dla tego urządzenia nadrzędnego.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identyfikator urządzenia |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒`space`

Biorąc pod uwagę identyfikator spacji, ta funkcja pobiera jego przestrzeni nadrzędnej.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *childSpaceId (ida przestrzeni podrzędnej)* | `guid` | Identyfikator miejsca |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒`space`

Biorąc pod uwagę identyfikator czujnika, ta funkcja pobiera swoją przestrzeń nadrzędną.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identyfikator czujnika |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒`space`

Biorąc pod uwagę identyfikator urządzenia, ta funkcja pobiera jego przestrzeni nadrzędnej.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *childDeviceId (ida podrzędna)* | `guid` | Identyfikator urządzenia |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒`space`

Biorąc pod uwagę identyfikator czujnika, ta funkcja pobiera swoje urządzenie nadrzędne.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identyfikator czujnika |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒`extendedProperty`

Biorąc pod uwagę identyfikator spacji, ta funkcja pobiera właściwość i jej wartość z spacji.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId (ida przestrzeni)* | `guid` | Identyfikator miejsca |
| *Propertyname* | `string` | Nazwa właściwości spacji |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒`extendedProperty`

Biorąc pod uwagę identyfikator czujnika, ta funkcja pobiera właściwość i jej wartość z czujnika.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identyfikator czujnika |
| *Propertyname* | `string` | Nazwa właściwości czujnika |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, nazwa właściwości) ⇒`extendedProperty`

Biorąc pod uwagę identyfikator urządzenia, ta funkcja pobiera właściwość i jej wartość z urządzenia.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identyfikator urządzenia |
| *Propertyname* | `string` | Nazwa właściwości urządzenia |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, wartość)

Ta funkcja ustawia wartość obiektu czujnika o danym typie danych.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identyfikator czujnika |
| *Datatype*  | `string` | Typ danych czujnika |
| *value*  | `string` | Wartość |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

Ta funkcja ustawia wartość obiektu spacji o danym typie danych.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId (ida przestrzeni)* | `guid` | Identyfikator miejsca |
| *Datatype* | `string` | Typ danych |
| *value* | `string` | Wartość |

### <a name="logmessage"></a>dziennik(wiadomość)

Ta funkcja rejestruje następujący komunikat w funkcji zdefiniowanej przez użytkownika.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *Komunikat* | `string` | Wiadomość do zarejestrowania |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, ładunek)

Ta funkcja wysyła niestandardowe powiadomienie do wysłania.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *topologiaObjectId*  | `guid` | Identyfikator obiektu wykresu. Przykładami są spacja, czujnik i identyfikator urządzenia.|
| *topologiaTyp*  | `string` | Przykładami są czujnik i urządzenie.|
| *payload*  | `string` | Ładunek JSON, który ma zostać wysłany wraz z powiadomieniem. |

## <a name="return-types"></a>Typy zwrotów

Modele odpowiedzi zwrócone z metod pomocnika referencyjnego klienta są opisane poniżej.

### <a name="space"></a>Miejsce

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

### <a name="space-methods"></a>Metody kosmiczne

#### <a name="parent--space"></a>Rodzic() ⇒`space`

Ta funkcja zwraca przestrzeń nadrzędną bieżącej przestrzeni.

#### <a name="childsensors--sensor"></a>Czujniki podrzędne() ⇒`sensor[]`

Ta funkcja zwraca czujniki podrzędne bieżącej przestrzeni.

#### <a name="childdevices--device"></a>Dzieci() ⇒`device[]`

Ta funkcja zwraca urządzenia podrzędne bieżącej przestrzeni.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(nazwa właściwości) ⇒`extendedProperty`

Ta funkcja zwraca właściwość rozszerzoną i jej wartość dla bieżącego miejsca.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *Propertyname* | `string` | Nazwa właściwości rozszerzonej |

#### <a name="valuevaluename--value"></a>Wartość(nazwa wartości) ⇒`value`

Ta funkcja zwraca wartość bieżącego spacji.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nazwa wartości |

#### <a name="historyvaluename--value"></a>Historia(valueName) ⇒`value[]`

Ta funkcja zwraca wartości historyczne bieżącej przestrzeni.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nazwa wartości |

#### <a name="notifypayload"></a>Powiadamianie (ładunek)

Ta funkcja wysyła powiadomienie z określonym ładunkiem.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Ładunek JSON do uwzględnienia w powiadomieniu |

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

#### <a name="parent--space"></a>Rodzic() ⇒`space`

Ta funkcja zwraca przestrzeń nadrzędną bieżącego urządzenia.

#### <a name="childsensors--sensor"></a>Czujniki podrzędne() ⇒`sensor[]`

Ta funkcja zwraca czujniki podrzędne bieżącego urządzenia.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(nazwa właściwości) ⇒`extendedProperty`

Ta funkcja zwraca właściwość rozszerzoną i jej wartość dla bieżącego urządzenia.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *Propertyname* | `string` | Nazwa właściwości rozszerzonej |

#### <a name="notifypayload"></a>Powiadamianie (ładunek)

Ta funkcja wysyła powiadomienie z określonym ładunkiem.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Ładunek JSON do uwzględnienia w powiadomieniu |

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

### <a name="sensor-methods"></a>Metody czujników

#### <a name="space--space"></a>Spacja() ⇒`space`

Ta funkcja zwraca przestrzeń nadrzędną bieżącego czujnika.

#### <a name="device--device"></a>Urządzenie() ⇒`device`

Ta funkcja zwraca urządzenie nadrzędne bieżącego czujnika.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(nazwa właściwości) ⇒`extendedProperty`

Ta funkcja zwraca właściwość rozszerzoną i jej wartość dla bieżącego czujnika.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *Propertyname* | `string` | Nazwa właściwości rozszerzonej |

#### <a name="value--value"></a>Wartość() ⇒`value`

Ta funkcja zwraca wartość bieżącego czujnika.

#### <a name="history--value"></a>Historia() ⇒`value[]`

Ta funkcja zwraca wartości historyczne bieżącego czujnika.

#### <a name="notifypayload"></a>Powiadamianie (ładunek)

Ta funkcja wysyła powiadomienie z określonym ładunkiem.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Ładunek JSON do uwzględnienia w powiadomieniu |

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

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [funkcjach zdefiniowanych przez użytkownika usługi Azure Digital Twins](./concepts-user-defined-functions.md).

- Dowiedz [się, jak tworzyć funkcje zdefiniowane przez użytkownika](./how-to-user-defined-functions.md).

- Dowiedz [się, jak debugować funkcje zdefiniowane przez użytkownika](./how-to-diagnose-user-defined-functions.md).
