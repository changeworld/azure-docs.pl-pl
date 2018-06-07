---
title: Opcje urządzenia do chmury systemu Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — wskazówki dotyczące użycie wiadomości urządzenia do chmury, zgłoszone właściwości lub przekazywania pliku do komunikacji z chmury do urządzenia.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: a1881b74ba3f4f66e9e47b24d2ee8c8c17c5f05d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634220"
---
# <a name="device-to-cloud-communications-guidance"></a>Wskazówki dotyczące komunikacji urządzenia do chmury
Podczas wysyłania informacji z aplikacji urządzenia zaplecza rozwiązania, Centrum IoT udostępnia trzy opcje:

* [Urządzenia do chmury wiadomości] [ lnk-d2c] telemetrii serie czasu i alerty.
* [Dwie urządzenia użytkownika zgłosiła właściwości] [ lnk-twins] raportowania urządzenia stan informacje takie jak dostępne możliwości, warunków lub stan długotrwałe przepływów pracy. Na przykład konfiguracji i aktualizacji oprogramowania.
* [Przekazywanie pliku] [ lnk-fileupload] nośnika pliki i partie dużych telemetrii przekazany przez sporadycznie połączonych urządzeń lub skompresowane, aby oszczędzić przepustowość.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Oto szczegółowe porównanie różnych opcji komunikacji urządzenia do chmury.

|  | Komunikaty z urządzenia do chmury | Dwie urządzenia zgłoszonego właściwości | Przekazywania plików |
| ---- | ------- | ---------- | ---- |
| Scenariusz | Dane telemetryczne szeregów czasowych i alerty. Na przykład partii danych czujnika 256 KB wysyłane co 5 minut. | Dostępne możliwości i warunki. Na przykład bieżącego urządzenia tryb łączności przykład sieci komórkowej lub Wi-Fi. Synchronizowanie długotrwałe przepływów pracy, takich jak aktualizacje oprogramowania i konfiguracji oprogramowania. | Pliki multimedialne. Partie dużych telemetrii (zazwyczaj skompresowane). |
| Przechowywanie i pobieranie | Tymczasowo przechowywane przez Centrum IoT maksymalnie 7 dni. Tylko sekwencyjnego odczytu. | Przechowywane przez Centrum IoT w dwie urządzenia. Pobieranie przy użyciu [język zapytań Centrum IoT][lnk-query]. | Przechowywane na koncie usługi Magazyn Azure dostarczane przez użytkownika. |
| Rozmiar | Maksymalnie 256 KB wiadomości. | Rozmiar maksymalny zgłoszone właściwości jest 8 KB. | Maksymalny rozmiar obsługiwany przez magazyn obiektów Blob Azure. |
| Częstotliwość | Wysoka. Aby uzyskać więcej informacji, zobacz [ogranicza Centrum IoT][lnk-quotas]. | Średnia. Aby uzyskać więcej informacji, zobacz [ogranicza Centrum IoT][lnk-quotas]. | Niski. Aby uzyskać więcej informacji, zobacz [ogranicza Centrum IoT][lnk-quotas]. |
| Protokół | Dostępna dla wszystkich protokołów. | Dostępne przy użyciu MQTT lub AMQP. | Dostępne, gdy przy użyciu protokołów, ale wymaga protokołu HTTPS na urządzeniu. |

Aplikacja może być konieczne wysyłanie informacji zarówno jako szeregów czasowych telemetrii lub alertu i udostępnić go w dwie urządzenia. W tym scenariuszu można wybrać jedną z następujących opcji:

* Aplikacja urządzenie wysyła komunikat urządzenia do chmury i raporty zmiany właściwości.
* Zaplecze rozwiązania może przechowywać informacje w znacznikach dwie urządzenia po odebraniu wiadomości.

Ponieważ urządzenia do chmury wiadomości włączyć znacznie wyższą przepływność niż aktualizacji dwie urządzenia, czasami jest pożądane, aby uniknąć aktualizowanie dwie urządzenia dla każdej wiadomości urządzenia do chmury.


[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-fileupload]: iot-hub-devguide-file-upload.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
