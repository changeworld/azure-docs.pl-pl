---
title: Centrum IoT Azure schematu siatki zdarzeń | Dokumentacja firmy Microsoft
description: Strona referencyjna format schematu zdarzeń i właściwości Centrum IoT
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 812ca3ba546112f54a76319fda853d441ce34f1b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Centrum IoT Azure schematu zdarzeń siatki zdarzeń

Ten artykuł zawiera właściwości i schematu dla zdarzeń Centrum IoT Azure. Aby obejrzeć wprowadzenie do schematów zdarzeń, zobacz [schematu zdarzeń siatki zdarzeń Azure](event-schema.md). 

## <a name="available-event-types"></a>Typy dostępnych zdarzeń

Centrum IoT Azure emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Gdy urządzenie jest zarejestrowane do Centrum IoT opublikowany. |
| Microsoft.Devices.DeviceDeleted | Opublikowana, gdy urządzenie zostanie usunięta z Centrum IoT. | 

## <a name="example-event"></a>Przykład zdarzeń

Schemat dla zdarzenia DeviceCreated i DeviceDeleted ma taką samą strukturę. To zdarzenie próbkowania zawiera schemat zdarzenia wywoływane, gdy urządzenie jest zarejestrowane do Centrum IoT:

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
    "deviceId": "LogicAppTestDevice",
    "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
    "opType": "DeviceCreated"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Właściwości zdarzenia

Wszystkie zdarzenia zawierają tych samych danych najwyższego poziomu: 

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| Temat | ciąg | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalny. Zdarzenie siatki udostępnia tę wartość. |
| Temat | ciąg | Ścieżka zdefiniowana wydawcy podmiotem zdarzeń. |
| Typ zdarzenia | ciąg | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzenia. |
| eventTime | ciąg | Czas jest generowane zdarzenie oparte na czas UTC dostawcy. |
| dane | obiekt | Dane zdarzenia Centrum IoT.  |
| dataVersion | ciąg | Wersja schematu dla obiektu danych. Wydawca definiuje wersji schematu. |
| Element metadataVersion | ciąg | Wersja schematu dla metadanych zdarzenia. Zdarzenie siatki definiuje schemat właściwości najwyższego poziomu. Zdarzenie siatki udostępnia tę wartość. |

Zawartość obiektu danych są różne dla każdego wydawca zdarzeń. Centrum IoT zdarzeń obiekt danych zawiera następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| hubName | ciąg | Nazwa centrum IoT, gdy urządzenie zostało utworzone lub usunięte. |
| deviceId | ciąg | Unikatowy identyfikator urządzenia. Ten ciąg z uwzględnieniem wielkości liter może mieć maksymalnie 128 znaków długości i obsługuje znaki alfanumeryczne ASCII 7-bitowego oraz następujące znaki specjalne: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| operationTimestamp | ciąg | Sygnatura czasowa ISO8601 operacji. |
| opType | ciąg | Typ zdarzenia określonych dla tej operacji przez Centrum IoT: albo `DeviceCreated` lub `DeviceDeleted`.
| twin | obiekt | Informacje o dwie urządzeń, czyli represenation chmury metadanych urządzenia aplikacji. | 
| Identyfikator urządzenia | ciąg | Unikatowy identyfikator dwie urządzenia. | 
| Element etag | ciąg | Fragment informacje opisujące zawartość dwie urządzenia. Każdy element etag jest musi być unikatowy na dwie urządzenia. | 
| status | ciąg | Określa, czy dwie urządzenia jest włączone lub wyłączone. | 
| statusUpdateTime | ciąg | Sygnatura czasowa ostatniego stanu dwie urządzenia ISO8601 aktualizacji. |
| Element connectionState | ciąg | Określa, czy urządzenie jest połączone lub rozłączone. | 
| lastActivityTime | ciąg | ISO8601 sygnaturę czasową ostatniej aktywności. | 
| cloudToDeviceMessageCount | liczba całkowita | Liczba chmury do urządzenia komunikaty wysyłane do tego urządzenia. | 
| Typ authenticationType | ciąg | Typ uwierzytelniania używany dla tego urządzenia: albo `SAS`, `SelfSigned`, lub `CertificateAuthority`. |
| X509Thumbprint | ciąg | Unikatową wartość x509 jest odcisk palca certyfikatu, najczęściej używanych można znaleźć określonego certyfikatu w magazynie certyfikatów. Odcisk palca dynamicznie jest generowany przy użyciu algorytmu SHA1, a nie istnieje fizycznie w certyfikacie. | 
| primaryThumbprint | ciąg | X509 głównej odcisku palca certyfikatu. |
| secondaryThumbprint | ciąg | X509 dodatkowej odcisku palca certyfikatu. | 
| wersja | liczba całkowita | Liczba całkowita, która jest zwiększany o jednej godziny urządzenia dwie jest aktualizowany. |
| żądany | obiekt | Część właściwości, które mogą być zapisywane tylko przez zaplecza aplikacji i odczytywane przez urządzenie. | 
| zgłoszone | obiekt | Część właściwości, które mogą być zapisywane tylko przez urządzenia i odczytywane przez zaplecza aplikacji. |
| lastUpdated | ciąg | Sygnatura czasowa ostatniego właściwości dwie urządzenia ISO8601 aktualizacji. | 

## <a name="next-steps"></a>Kolejne kroki

* Aby obejrzeć wprowadzenie do usługi Azure Event siatki, zobacz [co to jest zdarzenie siatki?](overview.md)
* Aby dowiedzieć się, jak Centrum IoT i siatki zdarzeń współdziałać ze sobą, zobacz [reagowania na zdarzenia Centrum IoT przy użyciu siatki zdarzeń do akcje wyzwalacza](../iot-hub/iot-hub-event-grid.md).