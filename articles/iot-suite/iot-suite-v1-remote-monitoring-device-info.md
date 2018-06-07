---
title: Urządzenie informacji metadanych w zdalnym rozwiązanie monitorowania | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 80f03a4cef1d79e819c59ca68a786776a5c4edb7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636100"
---
# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Urządzenie informacji metadanych w zdalnym wstępnie skonfigurowane rozwiązanie monitorowania

Pakiet IoT Azure zdalnego wstępnie skonfigurowane rozwiązanie monitorujące, przedstawiono podejście do zarządzania urządzeniami metadanych. W tym artykule przedstawiono podejście, które to rozwiązanie ma ułatwia zrozumienie:

* Jakie urządzenia metadane przechowuje rozwiązania.
* Sposób rozwiązania zarządzania metadane urządzenia.

## <a name="context"></a>Kontekst

Zdalne monitorowanie wstępnie skonfigurowane rozwiązanie używa [Centrum IoT Azure] [ lnk-iot-hub] umożliwiające urządzenia w celu wysyłania danych do chmury. Rozwiązania przechowuje informacje dotyczące urządzeń w trzech różnych miejscach:

| Lokalizacja | Informacje przechowywane | Wdrażanie |
| -------- | ------------------ | -------------- |
| Tożsamość rejestru | Identyfikator urządzenia, klucze uwierzytelniania, włączony stan | Wbudowana w Centrum IoT |
| Twins urządzenia | Metadane: właściwości zgłoszone, odpowiednie właściwości, znaczniki | Wbudowana w Centrum IoT |
| Cosmos DB | Historię poleceń i — metoda | Niestandardowe dla rozwiązania |

Centrum IoT obejmuje [rejestrze tożsamości urządzeń] [ lnk-identity-registry] do zarządzania dostępem do Centrum IoT i używa [twins urządzenia] [ lnk-device-twin] do zarządzania urządzeniami metadanych. Istnieje również zdalnego monitorowania określonego rozwiązania *rejestru urządzenia* który przechowuje historię poleceń i metody. Używa zdalnego rozwiązanie monitorowania [DB rozwiązania Cosmos] [ lnk-docdb] bazy danych do zaimplementowania magazynu niestandardowego dla historii poleceń i metody.

> [!NOTE]
> Zdalnego wstępnie skonfigurowane rozwiązanie monitorowania zachowuje w rejestrze tożsamości urządzenia zsynchronizowane z informacjami w bazie danych DB rozwiązania Cosmos. Oba rozwiązania używają tego samego identyfikatora urządzenia do unikatowej identyfikacji każdego urządzenia połączone z Centrum IoT.

## <a name="device-metadata"></a>Metadane urządzenia

Centrum IoT przechowuje [dwie urządzenia] [ lnk-device-twin] dla poszczególnych urządzeń fizycznych i symulowane podłączonej do zdalnego rozwiązanie monitorowania. Rozwiązanie używa twins urządzenia do zarządzania metadane skojarzone z urządzeń. Dwie urządzenia jest dokumentu JSON obsługiwany przez Centrum IoT i rozwiązanie używa interfejsu API Centrum IoT na interakcję z twins urządzenia.

Dwie urządzenia przechowuje trzy rodzaje metadanych:

- *Zgłoszone właściwości* są wysyłane do Centrum IoT przez urządzenie. W zdalnym rozwiązanie monitorowania symulowanego urządzenia wysyłają właściwości zgłoszone podczas rozruchu, a w odpowiedzi na **zmienić stan urządzenia** polecenia i metod. Możesz wyświetlić właściwości zgłoszone w **listę urządzeń** i **szczegóły urządzenia** w portalu rozwiązania. Zgłoszony właściwości są tylko do odczytu.
- *Żądany właściwości* są pobierane z Centrum IoT przez urządzenia. Jest odpowiedzialny za urządzeniu na żadnej konfiguracji niezbędne zmiany na urządzeniu. Należy również raportować zmiany do koncentratora jako właściwość zgłoszonego urządzenia. Można ustawić wartości właściwości żądaną za pośrednictwem portalu rozwiązania.
- *Tagi* istnieje tylko w dwie urządzenia i nigdy nie są synchronizowane z urządzeniem. Można ustawić wartości tagów w portalu rozwiązania i ich używać, aby filtrować listę urządzeń. Rozwiązanie używa również tag do identyfikowania ikonę, aby wyświetlić urządzenia w portalu rozwiązania.

Przykład zgłosił, że właściwości z urządzeń symulowane obejmują producenta, numer modelu szerokości geograficznej i długości. Symulowanego urządzenia także wrócić do listy obsługiwanych metod jako właściwość zgłoszony.

> [!NOTE]
> W kodzie na symulowanym urządzeniu są używane wyłącznie żądane właściwości **Desired.Config.TemperatureMeanValue** i **Desired.Config.TelemetryInterval** do aktualizowania zgłaszanych właściwości wysyłanych z powrotem do usługi IoT Hub. Wszystkie inne żądanej właściwości żądania zmiany są ignorowane.

Dokument JSON metadanych informacji urządzenia w bazie danych DB rozwiązania Cosmos rejestru urządzenia ma następującą strukturę:

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
> Informacje o urządzeniu mogą obejmować metadanych opisujących dane telemetryczne, które urządzenie wysyła do Centrum IoT. Rozwiązanie monitorowania zdalnego wykorzystuje te metadane telemetrii, aby dostosować sposób wyświetlania pulpitu nawigacyjnego [dynamiczne telemetrii][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Cykl życia

Po utworzeniu urządzenia w portalu rozwiązania, rozwiązanie tworzy wpis w bazie danych DB rozwiązania Cosmos do przechowywania historii poleceń i metody. W tym momencie rozwiązanie również tworzy wpis dla urządzenia w rejestrze tożsamości urządzeń, który generuje klucze, używanych przez urządzenia do uwierzytelniania za pomocą Centrum IoT. Tworzy dwie urządzenia.

Gdy urządzenie łączy się najpierw do rozwiązania, wysyła zgłoszone właściwości oraz komunikat z informacjami urządzenia. Wartości właściwości zgłoszone są automatycznie zapisywane w dwie urządzenia. Zgłoszony właściwości obejmują producenta urządzenia, numer modelu, numer seryjny i listę obsługiwanych metod. Komunikat z informacjami urządzenia zawiera listę poleceń, które obsługuje urządzenie tym informacje o żadnych parametrów polecenia. Po rozwiązaniu odbiera ten komunikat, aktualizuje informacje o urządzeniu w bazie danych DB rozwiązania Cosmos.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Umożliwia wyświetlanie i edytowanie informacji o urządzeniu w portalu rozwiązania

Lista urządzeń w portalu rozwiązania następujące właściwości urządzenia jako kolumny domyślnie wyświetla: **stan**, **DeviceId**, **producenta**, **modelu Numer**, **numer seryjny**, **oprogramowania układowego**, **platformy**, **procesora**, i  **Zainstalowana pamięć RAM**. Kolumny można dostosować, klikając **Edytor kolumn**. Właściwości urządzenia **szerokości geograficznej** i **geograficzne** dysków lokalizacji mapy Bing na pulpicie nawigacyjnym.

![Edytor kolumn na liście urządzeń][img-device-list]

W **szczegóły urządzenia** okienku w portalu rozwiązania można edytować żądanego właściwości i tagi (zgłosić właściwości są tylko do odczytu).

![W okienku szczegółów urządzenia][img-device-edit]

Portal rozwiązanie umożliwia usunięcie urządzenia z rozwiązania. Po usunięciu urządzenia rozwiązania spowoduje usunięcie tego wpisu z rejestru tożsamości, a następnie usuwa dwie urządzenia. Rozwiązania spowoduje również usunięcie informacji związanych z urządzenia z bazy danych DB rozwiązania Cosmos. Aby móc usunąć urządzenie, należy ją wyłączyć.

![Usuwanie urządzenia][img-device-remove]

## <a name="device-information-message-processing"></a>Przetwarzanie komunikatów informacji urządzenia

Komunikaty informacyjne urządzenia wysyłane przez urządzenie różnią się od komunikatów telemetrii. Komunikaty informacyjne urządzenia obejmują poleceń, które urządzenia mogą odpowiadać na, a cała historia polecenia. Centrum IoT, sama nie ma informacji o metadanych komunikat z informacjami urządzenia i przetwarza wiadomość w taki sam sposób przetwarza komunikat dowolnego urządzenia do chmury. W zdalnym rozwiązanie monitorowania [Azure Stream Analytics] [ lnk-stream-analytics] zadania (ASA) odczytuje komunikaty z Centrum IoT. **DeviceInfo** filtry dla wiadomości, które zawierają zadania stream analytics **"ObjectType": "DeviceInfo"** i przekazuje je do **EventProcessorHost** wystąpienie hosta który wykonuje zadanie sieci web. Logikę w **EventProcessorHost** wystąpienie używa Identyfikatora urządzenia można znaleźć rekordu rozwiązania Cosmos bazy danych dla określonego urządzenia i zaktualizować rekord.

> [!NOTE]
> Komunikat z informacjami urządzenia jest standardowy komunikat urządzenia do chmury. Rozwiązanie rozróżnia komunikaty informacyjne urządzenia i dane telemetryczne wiadomości przy użyciu ASA zapytań.

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu learning, jak można dostosować wstępnie skonfigurowanych rozwiązań można eksplorować niektóre inne funkcje i możliwości rozwiązań wstępnie pakiet IoT:

* [Omówienie rozwiązania konserwacji predykcyjnej wstępnie][lnk-predictive-overview]
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
[lnk-security-groundup]:../iot-accelerators/securing-iot-ground-up.md
