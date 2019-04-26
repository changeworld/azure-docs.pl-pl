---
title: Przewodnik dewelopera usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dla deweloperów usługi Azure IoT Hub zawiera omówienie punktów końcowych, zabezpieczenia, zarządzanie urządzeniami, zadania, metod bezpośrednich, operacje przekazywania plików, w rejestrze tożsamości, bliźniacze reprezentacje urządzeń, język zapytań usługi IoT Hub, a wiadomości.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 1ff7d430edd3f638ad5efcc5a89604e4ed732211
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60400154"
---
# <a name="azure-iot-hub-developer-guide"></a>Przewodnik dla deweloperów w usłudze Azure IoT Hub

Usługa Azure IoT Hub to w pełni zarządzana usługa, która ułatwia włączanie bezpieczną i niezawodną komunikację dwukierunkową między milionami urządzeń i zapleczem rozwiązania.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Usługa Azure IoT Hub zapewnia:

* Bezpieczna komunikacja przy użyciu poświadczeń zabezpieczeń na poziomie urządzenia i kontrola dostępu.

* Wiele opcji komunikacji ogromne urządzenia do chmury i z chmury do urządzeń.

* Odpytywalny przechowywania informacji o stanie poszczególnych urządzeń i metadane.

* Łączność urządzeń łatwe za pomocą biblioteki urządzeń dla najbardziej popularnych języków i platform.

Ten przewodnik dla deweloperów usługi IoT Hub zawiera następujące artykuły:

* [Wskazówki dotyczące komunikacji z urządzenia do chmury](iot-hub-devguide-d2c-guidance.md) pomaga wybrać komunikatów z urządzenia do chmury, zgłaszanych właściwości bliźniaczej reprezentacji urządzenia i przekazywanie pliku.

* [Wskazówki dotyczące komunikacji z chmury do urządzenia](iot-hub-devguide-c2d-guidance.md) pomaga wybrać jedną z metod bezpośrednich, żądane właściwości bliźniaczej reprezentacji urządzenia i komunikaty z chmury do urządzenia.

* [Urządzenia do chmury i obsługa komunikatów za pomocą usługi IoT Hub chmury do urządzenia](iot-hub-devguide-messaging.md) opisano funkcje obsługi komunikatów (urządzenia do chmury i z chmury do urządzeń), które udostępnia usługi IoT Hub.

  * [Wysyłanie komunikatów z urządzenia do chmury do Centrum IoT Hub](iot-hub-devguide-messages-d2c.md).

  * [Odczytywanie komunikatów z urządzenia do chmury z wbudowanego punktu końcowego](iot-hub-devguide-messages-read-builtin.md).

  * [Użyj niestandardowych punktów końcowych i reguł routingu dla komunikatów z urządzenia do chmury](iot-hub-devguide-messages-read-custom.md).

  * [Wysyłanie komunikatów z chmury do urządzeń z usługi IoT Hub](iot-hub-devguide-messages-c2d.md).

  * [Tworzenie i odczytywanie komunikatów usługi IoT Hub](iot-hub-devguide-messages-construct.md).

* [Przekazywanie plików z urządzenia](iot-hub-devguide-file-upload.md) w tym artykule opisano, jak przekazać plików z urządzenia. Artykuł zawiera również informacje o zagadnienia, takie jak powiadomienia, które można wysyłać procesu przekazywania.

* [Zarządzanie tożsamościami urządzeń w usłudze IoT Hub](iot-hub-devguide-identity-registry.md)opisuje jakie informacje magazyny rejestrze tożsamości Centrum IoT. W artykule opisano również, jak można uzyskać dostęp i zmodyfikować go.

* [Kontrola dostępu do usługi IoT Hub](iot-hub-devguide-security.md) w tym artykule opisano model zabezpieczeń umożliwia udzielanie dostępu do funkcji usługi IoT Hub dla obu urządzeń i w chmurze składniki. Artykuł zawiera informacje dotyczące używania tokenów i certyfikaty X.509 i szczegółowe informacje o uprawnienia, które można udzielić uprawnień.

* [Użyj bliźniacze reprezentacje urządzeń, aby zsynchronizować stan i konfiguracje](iot-hub-devguide-device-twins.md) opisuje *bliźniaczej reprezentacji urządzenia* koncepcji. Opisano również urządzenie funkcje, które udostępnić twins, takie jak synchronizacja urządzenia za pomocą jego bliźniaczej reprezentacji urządzenia. Artykuł zawiera informacje o danych przechowywanych w bliźniaczej reprezentacji urządzenia.

* [Wywoływanie metody bezpośredniej o urządzeniu](iot-hub-devguide-direct-methods.md) opisuje cykl życia metody bezpośredniej. W artykule opisano, jak wywoływać metody na urządzeniu z poziomu aplikacji zaplecza i obsługiwać bezpośrednie metody na urządzeniu.

* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md) w tym artykule opisano, jak możesz planować prace na wielu urządzeniach. W artykule opisano sposób przesyłania zadań wykonujących zadania co wykonywanie metody bezpośredniej aktualizacji urządzenia przy użyciu bliźniaczej reprezentacji urządzenia. Opisuje ona również, jak wykonać zapytania o stan zadania.

* [Odwołania — wybór protokołu komunikacyjnego](iot-hub-devguide-protocols.md) opisuje protokołów komunikacyjnych, że usługi IoT Hub obsługuje komunikacji z urządzeniem i wyświetla listę portów, które powinno być otwarte.

* [Odwołanie - punktów końcowych usługi IoT Hub](iot-hub-devguide-endpoints.md) w tym artykule opisano różne punkty końcowe, które każde Centrum IoT hub udostępnia dla operacji zarządzania i środowiska uruchomieniowego. W artykule opisano również, jak utworzyć dodatkowe punkty końcowe w usłudze IoT hub i jak włączyć łączność z punktami końcowymi usługi IoT Hub w scenariuszach niestandardowych za pomocą bramy w terenie.

* [Odwołanie — język zapytań usługi IoT Hub dla bliźniaczych reprezentacji urządzeń, zadań i routingu wiadomości](iot-hub-devguide-query-language.md) opisuje tego języka zapytań usługi IoT Hub, która umożliwia pobieranie informacji z Twojego Centrum dotyczących bliźniaczych reprezentacji urządzeń i zadań.

* [Odwołanie - przydziałów i dławienia](iot-hub-devguide-quotas-throttling.md) znajduje się podsumowanie przydziały w usługę IoT Hub i ograniczania przepustowości, które występuje, gdy przekracza limit przydziału.

* [Odwołania — cennik](iot-hub-devguide-pricing.md) zawiera ogólne informacje dotyczące różnych jednostek SKU i ceny dla usługi IoT Hub i szczegółowe informacje na temat sposobu różnych funkcji usługi IoT Hub są mierzone jako komunikaty przez usługę IoT Hub.

* [Odwołanie - urządzeniem i usługą SDK](iot-hub-devguide-sdks.md) Wyświetla listę zestawów SDK usługi Azure IoT do tworzenia aplikacji usług i urządzeń, które współdziałają z Centrum IoT hub. Artykuł zawiera linki do dokumentacji online dotyczącej interfejsu API.

* [Odwołanie — Obsługa protokołu MQTT Centrum IoT](iot-hub-mqtt-support.md) zawiera szczegółowe informacje o sposobie obsługi protokołu MQTT w Centrum IoT Hub. Artykuł zawiera opis Obsługa protokołu MQTT wbudowana w usłudze Azure IoT SDKs i zawiera informacje dotyczące bezpośrednio przy użyciu protokołu MQTT.

* [Słownik](iot-hub-devguide-glossary.md) listę typowe terminy związane z Centrum IoT Hub.