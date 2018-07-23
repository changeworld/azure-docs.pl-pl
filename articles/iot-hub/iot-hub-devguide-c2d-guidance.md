---
title: Opcje chmury do urządzenia w usłudze Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — wskazówki dotyczące kiedy należy używać metod bezpośrednich, żądane właściwości bliźniaczej reprezentacji urządzenia lub komunikatów z chmury do urządzeń do komunikacji z chmury do urządzenia.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: 2cc9bd39371741caaa3ae025df494e225dc754b0
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187040"
---
# <a name="cloud-to-device-communications-guidance"></a>Wskazówki dotyczące komunikacji z chmury do urządzenia
Usługa IoT Hub udostępnia trzy opcje dla aplikacji dla urządzeń do udostępnienia funkcji do aplikacji zaplecza:

* [Metody bezpośrednie] [ lnk-methods] komunikacji, które wymagają natychmiastowej potwierdzenie wyników. Metody bezpośrednie są często używane do interaktywnego kontroli urządzeń, takich jak włączenie kibicem.
* [Żądane właściwości bliźniaczej reprezentacji] [ lnk-twins] dla długotrwałych poleceń przeznaczone do umieszczenia urządzenia w pewnym żądanego stanu. Na przykład ustawić interwał wysyłania danych telemetrycznych do 30 minut.
* [Komunikaty z chmury do urządzenia] [ lnk-c2d] jednokierunkowe powiadomień do aplikacji urządzenia.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Oto szczegółowe porównanie różnych opcji komunikacji z chmury do urządzenia.

|  | Metody bezpośrednie | Żądane właściwości bliźniaczej reprezentacji | Komunikaty z chmury do urządzenia |
| ---- | ------- | ---------- | ---- |
| Scenariusz | Polecenia, które wymagają natychmiastowego potwierdzenia, takich jak włączenie kibicem. | Długotrwałych poleceń przeznaczone do umieszczenia urządzenia do niektórych żądanego stanu. Na przykład ustawić interwał wysyłania danych telemetrycznych do 30 minut. | Jednokierunkowa powiadomienia do aplikacji urządzenia. |
| Przepływ danych | Dwukierunkowe. W aplikacji urządzenia można błyskawiczne reagowanie na nie do metody. Zaplecze rozwiązania odbiera wynik kontekstowe na żądanie. | Jednokierunkowe. Aplikacja urządzenie odbiera powiadomienie o zmianie właściwości. | Jednokierunkowe. Aplikacja urządzenie odbiera wiadomości
| Trwałość | Odłączone urządzenia nie będą wykorzystywane. Zaplecze rozwiązania zostanie powiadomiony, że urządzenie nie jest połączone. | Wartości właściwości są zachowywane w bliźniaczej reprezentacji urządzenia. Urządzenie odczyta go na następnej ponownego połączenia. Wartości właściwości są możliwe do pobierania z [język zapytań usługi IoT Hub][lnk-query]. | Komunikaty mogą być zachowywane przez przez usługę IoT Hub w ciągu maksymalnie 48 godzin. |
| Cele | Za pomocą pojedynczego urządzenia **deviceId**, lub wielu urządzeń przy użyciu [zadania][lnk-jobs]. | Za pomocą pojedynczego urządzenia **deviceId**, lub wielu urządzeń przy użyciu [zadania][lnk-jobs]. | Pojedyncze urządzenie przez **deviceId**. |
| Rozmiar | Rozmiar ładunku metody bezpośredniej maksymalna to 128 KB. | Maksymalna żądanego rozmiaru właściwości wynosząca 8 KB. | Maksymalnie 64 KB wiadomości. |
| Częstotliwość | Wysoka. Aby uzyskać więcej informacji, zobacz [usługi IoT Hub ogranicza][lnk-quotas]. | Średnia. Aby uzyskać więcej informacji, zobacz [usługi IoT Hub ogranicza][lnk-quotas]. | Niski. Aby uzyskać więcej informacji, zobacz [usługi IoT Hub ogranicza][lnk-quotas]. |
| Protokół | Dostępne przy użyciu protokołu MQTT lub AMQP. | Dostępne przy użyciu protokołu MQTT lub AMQP. | Dostępna dla wszystkich protokołów. Urządzenie musi wykonać sondowanie, przy użyciu protokołu HTTPS. |

Dowiedz się, jak używać metod bezpośrednich, żądanych właściwości i komunikatów z chmury do urządzeń w ramach następujących samouczków:

* [Używanie metod bezpośrednich][lnk-methods-tutorial], dla metod bezpośrednich;
* [Umożliwia konfigurowanie urządzeń żądane właściwości][lnk-twin-properties]dla bliźniaczej reprezentacji urządzenia użytkownika żądane właściwości; 
* [Wysyłanie komunikatów z chmury do urządzeń][lnk-c2d-tutorial], dla komunikatów z chmury do urządzeń.

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-methods-tutorial]: quickstart-control-device-node.md
[lnk-twin-properties]: tutorial-device-twins.md
[lnk-c2d-tutorial]: iot-hub-node-node-c2d.md
