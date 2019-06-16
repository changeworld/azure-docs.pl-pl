---
title: Opcje chmury do urządzenia w usłudze Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — wskazówki dotyczące kiedy należy używać metod bezpośrednich, żądane właściwości bliźniaczej reprezentacji urządzenia lub komunikatów z chmury do urządzeń do komunikacji z chmury do urządzenia.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 4b738f34ae75478c0120832e7ad2b6a6a83dbf69
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61224782"
---
# <a name="cloud-to-device-communications-guidance"></a>Wskazówki dotyczące komunikacji z chmury do urządzenia

Usługa IoT Hub udostępnia trzy opcje dla aplikacji dla urządzeń do udostępnienia funkcji do aplikacji zaplecza:

* [Metody bezpośrednie](iot-hub-devguide-direct-methods.md) komunikacji, które wymagają natychmiastowej potwierdzenie wyników. Metody bezpośrednie są często używane do interaktywnego kontroli urządzeń, takich jak włączenie kibicem.

* [Żądane właściwości bliźniaczej reprezentacji](iot-hub-devguide-device-twins.md) dla długotrwałych poleceń przeznaczone do umieszczenia urządzenia w pewnym żądanego stanu. Na przykład ustawić interwał wysyłania danych telemetrycznych do 30 minut.

* [Komunikaty z chmury do urządzenia](iot-hub-devguide-messages-c2d.md) jednokierunkowe powiadomień do aplikacji urządzenia.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Oto szczegółowe porównanie różnych opcji komunikacji z chmury do urządzenia.

|  | Metody bezpośrednie | Żądane właściwości bliźniaczej reprezentacji | Komunikaty z chmury do urządzenia |
| ---- | ------- | ---------- | ---- |
| Scenariusz | Polecenia, które wymagają natychmiastowego potwierdzenia, takich jak włączenie kibicem. | Długotrwałych poleceń przeznaczone do umieszczenia urządzenia do niektórych żądanego stanu. Na przykład ustawić interwał wysyłania danych telemetrycznych do 30 minut. | Jednokierunkowa powiadomienia do aplikacji urządzenia. |
| Przepływ danych | Dwukierunkowe. W aplikacji urządzenia można błyskawiczne reagowanie na nie do metody. Zaplecze rozwiązania odbiera wynik kontekstowe na żądanie. | Jednokierunkowe. Aplikacja urządzenie odbiera powiadomienie o zmianie właściwości. | Jednokierunkowe. Aplikacja urządzenie odbiera wiadomości
| Trwałość | Odłączone urządzenia nie będą wykorzystywane. Zaplecze rozwiązania zostanie powiadomiony, że urządzenie nie jest połączone. | Wartości właściwości są zachowywane w bliźniaczej reprezentacji urządzenia. Urządzenie odczyta go na następnej ponownego połączenia. Wartości właściwości są możliwe do pobierania z [język zapytań usługi IoT Hub](iot-hub-devguide-query-language.md). | Komunikaty mogą być zachowywane przez przez usługę IoT Hub w ciągu maksymalnie 48 godzin. |
| Obiekty docelowe | Za pomocą pojedynczego urządzenia **deviceId**, lub wielu urządzeń przy użyciu [zadań](iot-hub-devguide-jobs.md). | Za pomocą pojedynczego urządzenia **deviceId**, lub wielu urządzeń przy użyciu [zadań](iot-hub-devguide-jobs.md). | Pojedyncze urządzenie przez **deviceId**. |
| Rozmiar | Rozmiar ładunku metody bezpośredniej maksymalna to 128 KB. | Maksymalna żądanego rozmiaru właściwości wynosząca 8 KB. | Maksymalnie 64 KB wiadomości. |
| Częstotliwość | Wysoka. Aby uzyskać więcej informacji, zobacz [usługi IoT Hub ogranicza](iot-hub-devguide-quotas-throttling.md). | Średnia. Aby uzyskać więcej informacji, zobacz [usługi IoT Hub ogranicza](iot-hub-devguide-quotas-throttling.md). | Niski. Aby uzyskać więcej informacji, zobacz [usługi IoT Hub ogranicza](iot-hub-devguide-quotas-throttling.md). |
| Protocol | Dostępne przy użyciu protokołu MQTT lub AMQP. | Dostępne przy użyciu protokołu MQTT lub AMQP. | Dostępna dla wszystkich protokołów. Urządzenie musi wykonać sondowanie, przy użyciu protokołu HTTPS. |

Dowiedz się, jak używać metod bezpośrednich, żądanych właściwości i komunikatów z chmury do urządzeń w ramach następujących samouczków:

* [Używanie metod bezpośrednich](quickstart-control-device-node.md)
* [Żądane właściwości umożliwiają konfigurowanie urządzeń](tutorial-device-twins.md) 
* [Wysyłanie komunikatów z chmury do urządzeń](iot-hub-node-node-c2d.md)