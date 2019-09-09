---
title: Schemat usługi Azure Event Grid dla Centrum IoT Hub | Dokumentacja firmy Microsoft
description: Strona odwołań format schematu zdarzeń i właściwości działania usługi IoT Hub
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: kgremban
ms.openlocfilehash: 4e96276a862844cea1d0800eafb952d4a0df97ab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076350"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Schemat zdarzeń Azure Event Grid dla Centrum IoT Hub

Ten artykuł zawiera właściwości i schematów zdarzeń usługi Azure IoT Hub. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [schematu zdarzeń usługi Azure Event Grid](event-schema.md). 

Aby uzyskać listę przykładowych skryptów i samouczków, zobacz [źródła zdarzeń usługi IoT Hub](event-sources.md#iot-hub).

## <a name="available-event-types"></a>Zdarzenie dostępne typy

Usługa Azure IoT Hub emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Opublikowane po zarejestrowaniu urządzenia do usługi IoT hub. |
| Microsoft.Devices.DeviceDeleted | Opublikowana, gdy urządzenie zostanie usunięty z usługi IoT hub. | 
| Microsoft.Devices.DeviceConnected | Gdy urządzenie jest podłączone do usługi IoT hub opublikowany. |
| Microsoft.Devices.DeviceDisconnected | Gdy urządzenie jest odłączony od usługi IoT hub opublikowany. | 
| Microsoft.Devices.DeviceTelemetry | Opublikowana, gdy komunikaty telemetryczne są wysyłane do usługi IoT hub. |

Wszystkie zdarzenia urządzenia, z wyjątkiem zdarzeń telemetrii urządzenia są ogólnie dostępne we wszystkich regionach obsługiwanych przez usługę Event Grid. Zdarzenia telemetrii urządzenia jest w publicznej wersji zapoznawczej i jest dostępna we wszystkich regionach z wyjątkiem wschodnie stany USA, zachodnie stany USA, Europa Zachodnia, [Azure dla instytucji rządowych](../azure-government/documentation-government-welcome.md), [Azure China 21Vianet](/azure/china/china-welcome), i [Azure (Niemcy)](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="example-event"></a>Przykład zdarzenia

Schemat zdarzeń DeviceConnected i DeviceDisconnected mieć tę samą strukturę. To zdarzenie próbki przedstawia schematu zdarzenia wywoływane, gdy urządzenie jest podłączone do usługi IoT hub:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

Zdarzenie DeviceTelemetry jest wywoływane, gdy zdarzenia telemetrii są wysyłane do usługi IoT Hub. Poniżej przedstawiono przykładowe schematu dla tego zdarzenia.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Schemat zdarzeń DeviceCreated i DeviceDeleted mieć tę samą strukturę. To zdarzenie próbki przedstawia schematu zdarzenia wywoływane, gdy urządzenie jest zarejestrowane w Centrum IoT:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Właściwości zdarzenia

Wszystkie zdarzenia zawierają te same dane najwyższego poziomu: 

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| id | string | Unikatowy identyfikator zdarzenia. |
| topic | string | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalna. Usługa Event Grid udostępnia tę wartość. |
| subject | string | Ścieżka zdefiniowana przez wydawcę na temat zdarzenia. |
| eventType | string | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzeń. |
| eventTime | string | Czas, którego zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| data | object | Dane zdarzeń usługi IoT Hub.  |
| dataVersion | string | Wersja schematu dla obiektu danych. Wydawca Określa wersję schematu. |
| metadataVersion | string | Wersja schematu dla metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Usługa Event Grid udostępnia tę wartość. |

Dla wszystkich zdarzeń usługi IoT Hub obiekt danych zawiera następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| hubName | string | Nazwa usługi IoT Hub, gdy urządzenie utworzony lub został usunięty. |
| deviceId | string | Unikatowy identyfikator urządzenia. Ten ciąg uwzględniający wielkość liter może mieć długości maksymalnie 128 znaków i obsługuje znaki ASCII 7-bitowe znaki alfanumeryczne oraz następujące znaki specjalne: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

Zawartość obiektu danych są różne dla każdego wydawcy zdarzeń. 

Aby uzyskać **urządzenie połączone** i **Urządzenie odłączone** zdarzeń usługi IoT Hub, obiekt danych zawiera następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| moduleId | string | Unikatowy identyfikator modułu. To pole jest dane wyjściowe tylko na urządzeniach z modułu. Ten ciąg uwzględniający wielkość liter może mieć długości maksymalnie 128 znaków i obsługuje znaki ASCII 7-bitowe znaki alfanumeryczne oraz następujące znaki specjalne: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| deviceConnectionStateEventInfo | object | Informacje zdarzeń o stanie połączenia urządzenia
| sequenceNumber | string | Liczba, która pomaga wskazać kolejność podłączone urządzenie lub urządzenia odłączony zdarzenia. Najnowsze zdarzenia będzie mieć numer sekwencji jest wyższa niż poprzednie zdarzenie. Ten numer może ulec zmianie przez więcej niż 1, ale jest dokładnie rosnącej. Zobacz [sposób użycia numer sekwencyjny](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Aby uzyskać **danych Telemetrycznych z urządzenia** zdarzeń usługi IoT Hub zawiera obiekt danych komunikatu urządzenia do chmury w [format komunikatów usługi IoT hub](../iot-hub/iot-hub-devguide-messages-construct.md) i ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Treść | string | Zawartość wiadomości z urządzenia. |
| properties | string | Właściwości aplikacji są zdefiniowane przez użytkownika ciągów, które można dodać do wiadomości. Te pola są opcjonalne. |
| Właściwości systemu | string | [Właściwości systemu](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) pomagać w identyfikacji zawartości i źródła wiadomości. Komunikaty telemetryczne urządzenia musi być w prawidłowym formacie JSON za pomocą contentType wartość JSON i contentEncoding równa UTF-8 we właściwościach komunikatu systemu. Jeśli to nie jest ustawiona, usługi IoT Hub będzie zapisywać komunikaty w podstawowej formacie zakodowanym 64.  |

Aby uzyskać **utworzyć urządzenia** i **urządzenie usunięte** zdarzeń usługi IoT Hub, obiekt danych zawiera następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| twin | object | Informacje na temat bliźniaczej reprezentacji urządzenia, czyli chmury reprezentacja metadanych urządzenia w aplikacji. | 
| deviceID | string | Unikatowy identyfikator bliźniaczej reprezentacji urządzenia. | 
| etag | string | Moduł weryfikacji dla zapewnienia spójności aktualizacji bliźniaczej reprezentacji urządzenia. Każdy element etag jest musi być unikatowa dla każdej bliźniaczej reprezentacji urządzenia. |  
| deviceEtag| string | Moduł weryfikacji dla zapewnienia spójności aktualizacji do rejestru urządzeń. Każdy deviceEtag może być unikatowy dla rejestru urządzeń. |
| status | string | Bliźniacza reprezentacja urządzenia jest włączone czy wyłączone. | 
| statusUpdateTime | string | Zaktualizuj ISO8601 sygnatura czasowa ostatniego stanu bliźniaczej reprezentacji urządzenia. |
| connectionState | string | Czy urządzenie jest połączone lub odłączone. | 
| lastActivityTime | string | ISO8601 sygnaturę czasową ostatniej aktywności. | 
| cloudToDeviceMessageCount | liczba całkowita | Liczba komunikatów urządzeń, wysyłane do tego urządzenia w chmurze. | 
| authenticationType | string | Typ uwierzytelniania używany dla tego urządzenia: albo `SAS`, `SelfSigned`, lub `CertificateAuthority`. |
| x509Thumbprint | string | Odcisk palca jest unikatową wartość dla x509 certyfikatu, często używane, można znaleźć określonego certyfikatu w magazynie certyfikatów. Odcisk palca jest generowana dynamicznie przy użyciu algorytmu SHA1, a nie istnieje fizycznie w certyfikacie. | 
| primaryThumbprint | string | Podstawowy odcisk palca dla x509 certyfikatu. |
| secondaryThumbprint | string | Pomocniczy odcisk palca dla x509 certyfikatu. | 
| version | liczba całkowita | Liczba całkowita, która jest zwiększany o jeden każdy Godzina aktualizacji bliźniaczej reprezentacji. |
| desired | object | Część właściwości, które mogą być zapisywane tylko przez serwer zaplecza aplikacji i odczytywane przez urządzenie. | 
| reported | object | Część właściwości, które mogą być zapisywane tylko przez urządzenia i odczytywane przez serwer zaplecza aplikacji. |
| lastUpdated | string | Zaktualizuj ISO8601 sygnatura czasowa ostatniej właściwości bliźniaczej reprezentacji urządzenia. | 

## <a name="next-steps"></a>Kolejne kroki

* Wprowadzenie do usługi Azure Event Grid, zobacz [co to jest usługa Event Grid?](overview.md)
* Aby dowiedzieć się, jak usługa IoT Hub i Event Grid współpracują ze sobą, zobacz [reagować na zdarzenia IoT Hub przy użyciu usługi Event Grid do wyzwalania akcji](../iot-hub/iot-hub-event-grid.md).