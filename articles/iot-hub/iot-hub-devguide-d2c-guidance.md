---
title: Opcje urządzenia do chmury w usłudze Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — wskazówki dotyczące kiedy należy używać komunikatów z urządzenia do chmury, zgłoszone właściwości aktualizuje lub przekazywanie pliku do komunikacji z chmury do urządzenia.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: fffa064b912a96b05feb901d1d2d44533c4681b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60885520"
---
# <a name="device-to-cloud-communications-guidance"></a>Wskazówki dotyczące komunikacji z urządzenia do chmury

Podczas wysyłania informacji z aplikacji urządzenia do zaplecza rozwiązania, usługi IoT Hub udostępnia trzy opcje:

* [Komunikaty z urządzenia do chmury](iot-hub-devguide-messages-d2c.md) czas serii telemetrię i alerty.

* [Bliźniacza reprezentacja urządzenia użytkownika zgłaszanych właściwości](iot-hub-devguide-device-twins.md) raportowania informacje o stanie urządzenia, takie jak dostępne możliwości, postanowienia lub stan długotrwałe przepływy pracy. Na przykład konfiguracji i aktualizacji oprogramowania.

* [Przekazywanie pliku](iot-hub-devguide-file-upload.md) nośnika pliki i telemetrii dużych partii przekazane przez sporadycznie połączonych urządzeń lub skompresować, aby oszczędzić przepustowość.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Oto szczegółowe porównanie różnych opcji komunikacji urządzenia do chmury.

|  | Komunikaty z urządzenia do chmury | Zgłaszane właściwości bliźniaczej reprezentacji urządzenia | Operacje przekazywania plików |
| ---- | ------- | ---------- | ---- |
| Scenariusz | Szeregi czasowe telemetrię i alerty. Na przykład partie danych czujnika 256 KB wysyłane co 5 minut. | Dostępne możliwości i warunki. Na przykład bieżącego urządzenia tryb łączności takich jak sieci komórkowej lub Wi-Fi. Synchronizowanie długotrwałe przepływy pracy, takich jak konfiguracji lub aktualizacji oprogramowania. | Pliki multimedialne. Partie dużych telemetrii (zazwyczaj skompresowane). |
| Przechowywanie i pobieranie | Tymczasowo przechowywane przez usługę IoT Hub maksymalnie 7 dni. Tylko sekwencyjnego odczytu. | Przechowywane przez usługę IoT Hub w bliźniaczej reprezentacji urządzenia. Pobieranie przy użyciu [język zapytań usługi IoT Hub](iot-hub-devguide-query-language.md). | Przechowywane na koncie usługi Azure Storage podanego przez użytkownika. |
| Rozmiar | Maksymalnie 256 KB komunikatów. | Rozmiar maksymalny zgłoszone właściwości aktualizuje to 8 KB. | Maksymalny obsługiwany rozmiar pliku przez usługę Azure Blob Storage. |
| Częstotliwość | Wysoka. Aby uzyskać więcej informacji, zobacz [usługi IoT Hub ogranicza](iot-hub-devguide-quotas-throttling.md). | Średnia. Aby uzyskać więcej informacji, zobacz [usługi IoT Hub ogranicza](iot-hub-devguide-quotas-throttling.md). | Niski. Aby uzyskać więcej informacji, zobacz [usługi IoT Hub ogranicza](iot-hub-devguide-quotas-throttling.md). |
| Protocol | Dostępna dla wszystkich protokołów. | Dostępne przy użyciu protokołu MQTT lub AMQP. | Dostępne w przypadku korzystania z protokołów, ale wymaga protokołu HTTPS na urządzeniu. |

Aplikacja może potrzebować do wysyłania informacji zarówno jako dane telemetryczne szeregów czasowych lub alertu i udostępnić ją w bliźniaczej reprezentacji urządzenia. W tym scenariuszu można wybrać jedną z następujących opcji:

* Aplikacja urządzenia wysyła komunikat z urządzenia do chmury i zmiana właściwości raportów.
* Zaplecze rozwiązania może przechowywać informacje w bliźniaczej reprezentacji urządzenia tagi, po odebraniu wiadomości.

Ponieważ komunikaty z urządzenia do chmury włączyć znacznie wyższą przepływność niż aktualizacji bliźniaczej reprezentacji urządzenia, czasami jest pożądane, aby uniknąć aktualizacji bliźniaczej reprezentacji urządzenia dla każdej wiadomości z urządzenia do chmury.