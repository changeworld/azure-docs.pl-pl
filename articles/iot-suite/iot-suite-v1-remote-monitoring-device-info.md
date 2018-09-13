---
title: Metadane informacji urządzeń w rozwiązaniu do monitorowania zdalnego | Dokumentacja firmy Microsoft
description: Opis wstępnie skonfigurowanego rozwiązania Azure IoT do monitorowania zdalnego wraz z informacjami dotyczącymi architektury rozwiązania.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 1b334769-103b-4eb0-a293-184f3d1ba9a3
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 4efea316c05f566add3e175bc5bb18842225ede3
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35758165"
---
# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Urządzenie informacji o metadanych w wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego

Azure IoT Suite ze wstępnie skonfigurowanym rozwiązaniem monitorowania zdalnego pokazuje podejście do zarządzania metadane urządzenia. W tym artykule przedstawiono podejście, które przyjmuje tego rozwiązania, aby umożliwić Ci zrozumienie:

* Metadane urządzenia, jakie rozwiązanie przechowuje.
* Sposób rozwiązania zarządzania metadane urządzenia.

## <a name="context"></a>Kontekst

Zdalne monitorowanie wstępnie skonfigurowane rozwiązanie używa [usługi Azure IoT Hub] [ lnk-iot-hub] umożliwiające urządzeń w celu wysyłania danych do chmury. Rozwiązanie przechowuje informacje dotyczące urządzeń w trzech różnych miejscach:

| Lokalizacja | Informacje przechowywane | Wdrażanie |
| -------- | ------------------ | -------------- |
| Rejestr tożsamości | Identyfikator urządzenia i klucze uwierzytelniania, włączony stan | Wbudowana w usługę IoT Hub |
| Bliźniacze reprezentacje urządzeń | Metadane: zgłaszanych właściwości, żądanych właściwości tagi | Wbudowana w usługę IoT Hub |
| Cosmos DB | Historia polecenia i metody | Niestandardowe rozwiązania |

Usługa IoT Hub zawiera [rejestr tożsamości urządzeń] [ lnk-identity-registry] zarządzanie dostępem do usługi IoT hub i używa [bliźniaczych reprezentacji urządzeń] [ lnk-device-twin] do zarządzania metadanymi urządzenia. Istnieje również zdalnego monitorowania specyficznych dla rozwiązania *rejestru urządzeń* , przechowuje historię poleceń i metody. Korzysta z rozwiązania do monitorowania zdalnego [Cosmos DB] [ lnk-docdb] bazy danych, aby zaimplementować magazyn niestandardowy dla historii poleceń i metody.

> [!NOTE]
> Wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego przechowuje rejestr tożsamości urządzeń w synchronizacji z informacjami w bazie danych Cosmos DB. Obaj za pomocą takim samym identyfikatorem urządzenia identyfikują każdego urządzenia połączonego Centrum IoT hub.

## <a name="device-metadata"></a>Metadane urządzenia

Usługa IoT Hub utrzymuje [bliźniaczej reprezentacji urządzenia] [ lnk-device-twin] dla każdego symulowane i fizyczne urządzenia podłączone do rozwiązania do monitorowania zdalnego. Rozwiązania używa bliźniaczych reprezentacji urządzeń do zarządzania metadane skojarzone z urządzenia. Bliźniak urządzenia to dokument JSON przechowywane przez usługę IoT Hub, a rozwiązanie używa interfejsu API z Centrum IoT na interakcję z bliźniaczych reprezentacji urządzeń.

Trzy rodzaje metadane są przechowywane w bliźniaczej reprezentacji urządzenia:

- *Zgłaszane właściwości* są wysyłane do usługi IoT hub za pomocą urządzenia. W rozwiązaniu do monitorowania zdalnego, symulowane urządzenia wysyłają zgłaszane właściwości podczas rozruchu i w odpowiedzi na **zmiany stanu urządzenia** poleceniami i metodami. Możesz wyświetlić właściwości zgłaszanych w **listy urządzeń** i **szczegóły urządzenia** w portalu rozwiązania. Zgłaszane właściwości są tylko do odczytu.
- *Żądane właściwości* są pobierane z usługi IoT hub za pomocą urządzeń. Jest odpowiedzialny za urządzenie, aby zwolnić wszelkie niezbędne zmianę konfiguracji na urządzeniu. Jest również odpowiedzialny za zgłoszenia tej zmiany z powrotem do Centrum jako zgłaszanej właściwości urządzenia. Można ustawić wartość żądanej właściwości, za pośrednictwem portalu rozwiązania.
- *Tagi* istnieją tylko w bliźniaczej reprezentacji urządzenia i nigdy nie są synchronizowane z urządzeniem. Można ustawić wartości tagów w portalu rozwiązania i ich używać, aby filtrować listę urządzeń. Rozwiązanie używa również tag, aby zidentyfikować ikonę do wyświetlenia urządzenia w portalu rozwiązania.

Przykład zgłosił, że właściwości symulowane urządzenia obejmują producenta, numer modelu, szerokości i długości geograficznej. Symulowane urządzenia również zwracać listę obsługiwanych metod jako zgłaszanej właściwości.

> [!NOTE]
> W kodzie na symulowanym urządzeniu są używane wyłącznie żądane właściwości **Desired.Config.TemperatureMeanValue** i **Desired.Config.TelemetryInterval** do aktualizowania zgłaszanych właściwości wysyłanych z powrotem do usługi IoT Hub. Wszystkie inne żądania zmiany żądanych właściwości są ignorowane.

Urządzenie informacje metadanych dokumentem JSON przechowywane w bazie danych Cosmos DB w rejestrze urządzeń ma następującą strukturę:

```json
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

> [!NOTE]
> Informacje o urządzeniu mogą również zawierać metadane opisują dane telemetryczne, które urządzenie wysyła do usługi IoT Hub. Rozwiązania do monitorowania zdalnego wykorzystuje te metadane telemetrii, aby dostosować sposób wyświetlania pulpitu nawigacyjnego [telemetrii dynamicznej][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Cykl życia

Po utworzeniu urządzenia w portalu rozwiązania, rozwiązanie tworzy wpis w bazie danych Cosmos DB do przechowywania historii poleceń i metody. W tym momencie rozwiązanie tworzy wpis dla urządzenia w rejestrze tożsamości urządzeń, które generuje klucze, który jest używany do uwierzytelniania za pomocą usługi IoT Hub. Tworzy również w bliźniaczej reprezentacji urządzenia.

Przy pierwszym połączeniu urządzenia z rozwiązaniem, wysyła zgłaszane właściwości oraz komunikat z informacjami o urządzeniu. Wartości zgłaszanych właściwości są automatycznie zapisywane w bliźniaczej reprezentacji urządzenia. Zgłaszane właściwości obejmują producent urządzenia, numer modelu, numer seryjny i Lista obsługiwanych metod. Komunikat z informacjami o urządzeniu obejmują listę poleceń obsługiwanych przez urządzenie w tym informacje o parametrach dowolnego polecenia. Gdy rozwiązanie odbiera ten komunikat, aktualizuje informacje o urządzeniu w bazie danych Cosmos DB.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Wyświetlanie i edytowanie informacji o urządzeniu w portalu rozwiązania

Lista urządzeń w portalu rozwiązania następujące właściwości urządzenia jako kolumny domyślnie wyświetla: **stan**, **DeviceId**, **producenta**, **modelu Liczba**, **numer seryjny**, **oprogramowania układowego**, **platformy**, **procesora**, i  **Zainstalowana pamięć RAM**. Kolumny można dostosować, klikając **Edytor kolumn**. Właściwości urządzenia **Latitude** i **długości geograficznej** dysku lokalizacji na mapie Bing na pulpicie nawigacyjnym.

![Edytor kolumn na liście urządzeń][img-device-list]

W **szczegóły urządzenia** okienku w portalu rozwiązania możesz edytować żądane właściwości i tagów (zgłaszane właściwości są tylko do odczytu).

![Okienko szczegółów urządzenia][img-device-edit]

Aby usunąć urządzenie z rozwiązania, można użyć portalu rozwiązania. Po usunięciu urządzenia rozwiązania spowoduje usunięcie tego wpisu w rejestrze tożsamości, a następnie usuwa bliźniaczej reprezentacji urządzenia. To rozwiązanie spowoduje również usunięcie informacji związanych z urządzeniem z bazy danych Cosmos DB. Zanim będzie można usunąć urządzenia, należy ją wyłączyć.

![Usuwanie urządzenia][img-device-remove]

## <a name="device-information-message-processing"></a>Przetwarzanie komunikatów informacje o urządzeniu

Komunikaty z informacjami o urządzeniu wysyłana przez urządzenie różnią się od komunikaty telemetryczne. Komunikaty z informacjami o urządzeniu obejmują poleceń, które może odpowiedzieć urządzenie i Historia poleceń. Usługa IoT Hub, sama nie ma informacji o metadanych zawartych w komunikat z informacjami o urządzeniu i przetwarza wiadomość w taki sam sposób, jak są przetwarzane wszystkie komunikaty z urządzenia do chmury. W rozwiązaniu do monitorowania zdalnego [usługi Azure Stream Analytics] [ lnk-stream-analytics] zadania (ASA) odczytuje komunikaty z usługi IoT Hub. **DeviceInfo** filtrów dla komunikatów, które zawierają zadania usługi stream analytics **"ObjectType": "DeviceInfo"** i przekazuje je do **EventProcessorHost** wystąpienie hosta która działa w zadaniu sieci web. Logika **EventProcessorHost** wystąpienia używa Identyfikatora urządzenia, aby odnaleźć rekordu usługi Cosmos DB dla określonego urządzenia i zaktualizuj rekord.

> [!NOTE]
> Komunikat z informacjami o urządzeniu jest standardową wiadomość urządzenia do chmury. Rozwiązanie rozróżnia komunikaty z informacjami o urządzeniu i komunikaty telemetryczne przy użyciu zapytań ASA.

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu nauki, w jaki sposób dostosować wstępnie skonfigurowane rozwiązania, możesz zapoznać się z niektóre inne funkcje i możliwości IoT wstępnie skonfigurowanych rozwiązań pakietu:

* [Omówienie rozwiązania do konserwacji predykcyjnej, wstępnie skonfigurowane][lnk-predictive-overview]
* [Często zadawane pytania dotyczące Pakietu IoT][lnk-faq]
* [Zabezpieczenia IoT od podstaw][lnk-security-groundup]

<!-- Images and links -->
[img-device-list]: media/iot-suite-v1-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-v1-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-v1-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dynamic-telemetry]: iot-suite-v1-dynamic-telemetry.md

[lnk-predictive-overview]:../iot-accelerators/iot-accelerators-predictive-overview.md
[lnk-faq]: iot-suite-v1-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
