---
title: Przewodnik dewelopera Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera usługi Azure IoT Hub omówiono punktów końcowych, zabezpieczeń, w rejestrze tożsamości, zarządzanie urządzeniami, bezpośrednie metod, twins urządzenia, przekazywania plików, zadań, Centrum IoT język zapytania, a wiadomości.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 1871ae68008f9fe9b3cb201a7b5f205792bc0817
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632979"
---
# <a name="azure-iot-hub-developer-guide"></a>Przewodnik dewelopera Centrum IoT Azure

Centrum IoT Azure jest w pełni zarządzaną usługę, która umożliwia włączanie i niezawodności komunikację dwukierunkową między milionów urządzeń i zaplecze rozwiązania.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Centrum IoT Azure oferuje:

* Bezpieczna komunikacja przy użyciu poświadczeń zabezpieczeń urządzenia i kontrola dostępu.
* Wiele opcji komunikacji hiperskali urządzenia do chmury i chmury do urządzenia.
* Kolejność przechowywania informacji o stanie na urządzenie i metadanych.
* Łatwe urządzenia łączność z bibliotekami urządzenia dla najbardziej popularnych języków i platform.

Ten przewodnik dewelopera Centrum IoT zawiera następujące artykuły:

* [Wskazówki dotyczące komunikacji urządzenia do chmury] [ lnk-d2c-guidance] ułatwia wybór między wiadomości urządzenia do chmury, dwie urządzenia zgłoszonego właściwości i przekazywania pliku.
* [Wskazówki dotyczące komunikacji chmury do urządzenia] [ lnk-c2d-guidance] ułatwia wybór między metody bezpośredniego, odpowiednie właściwości dwie urządzenia i komunikaty chmury do urządzenia.
* [Urządzenia do chmury i wiadomości z Centrum IoT chmury do urządzenia] [ devguide-messaging] opisano funkcje obsługi wiadomości (urządzenia do chmury i chmury do urządzenia), które udostępnia Centrum IoT.
  * [Wysyłanie komunikatów urządzenia do chmury do Centrum IoT][devguide-messages-d2c].
  * [Przeczytaj komunikaty urządzenia do chmury z wbudowanym punktem końcowym][devguide-builtin].
  * [Użyj niestandardowe punkty końcowe i reguły routingu dla komunikatów urządzenia do chmury][devguide-custom].
  * [Wysyłanie wiadomości chmury do urządzenia z Centrum IoT][devguide-messages-c2d].
  * [Tworzenie i odczytywanie wiadomości Centrum IoT][devguide-format].
* [Przekazywanie plików z urządzenia] [ devguide-upload] w tym artykule opisano, jak można przekazać pliki z urządzenia. Artykuł zawiera także informacje o tematów, takich jak powiadomienia, czy można wysyłać procesu przekazywania.
* [Zarządzanie tożsamościami urządzenie w Centrum IoT] [ devguide-identities] opisuje jakie informacje magazyny rejestru tożsamości każdego centrum IoT. Artykuł opisuje również sposób dostępu i zmodyfikuj go.
* [Kontrola dostępu do Centrum IoT] [ devguide-security] opisano model zabezpieczeń używany do udostępnienia funkcji Centrum IoT dla obu urządzeń i składniki w chmurze. Artykuł zawiera informacje dotyczące używania tokenów i certyfikaty X.509 i szczegóły uprawnień, które mogą udzielać dostępu.
* [Umożliwia synchronizowanie stanu i konfiguracji urządzenia twins] [ devguide-device-twins] opisuje *dwie urządzenia* koncepcji. Artykuł również funkcje twins urządzenie tym ujawnia, takich jak synchronizowanie urządzenia z jego dwie urządzenia. Artykuł zawiera informacje na temat danych przechowywanych w dwie urządzenia.
* [Wywoływanie metody bezpośrednio na urządzeniu] [ devguide-directmethods] opisuje cyklem życia metoda bezpośrednia. Artykuł opisuje sposób wywołania metody na urządzeniu z aplikacji zaplecza i obsługiwać metodę bezpośrednio na urządzeniu.
* [Planowanie zadań na wielu urządzeniach] [ devguide-jobs] zawiera opis sposobu tworzenia harmonogramu zadań na wielu urządzeniach. Artykuł opisuje sposób przesyłania zadań, wykonujących zadania jako wykonywania metoda bezpośrednia aktualizacja urządzenia przy użyciu podwójnego urządzenia. On również opis zbadać stanu zadania.
* [Odwołanie — wybierz protokół komunikacyjny] [ devguide-protocol] opisuje protokołów komunikacyjnych, że Centrum IoT do komunikacji urządzeń i obsługuje listę portów, które powinno być otwarte.
* [Odwołanie — punkty końcowe Centrum IoT] [ devguide-endpoints] opisano różne punkty końcowe, które udostępnia każdego centrum IoT dla operacji obsługi i zarządzania. Artykuł opisuje również sposób możesz utworzyć dodatkowe punkty końcowe w Centrum IoT i jak użyć bramy pola, aby umożliwić łączność z punktami końcowymi Centrum IoT w scenariuszach niestandardowe.
* [Odwołanie - Centrum IoT zapytania języka twins urządzenia, zadania i rozsyłania wiadomości] [ devguide-query] opisuje tego języka zapytania Centrum IoT, która umożliwia pobieranie informacji z Centrum temat zadań i twins urządzenia.
* [Odwołanie - przydziały i ograniczenia przepustowości] [ devguide-quotas] podsumowuje przydziały w usłudze IoT Hub i ograniczenie, które występuje, gdy przekracza limit przydziału.
* [Odwołanie — cennik] [ devguide-pricing] zawiera ogólne informacje dotyczące różnych jednostki SKU i cenach dla Centrum IoT i szczegółowe informacje o sposobie różne funkcje Centrum IoT są mierzone jako wiadomości przez Centrum IoT.
* [Odwołanie - urządzeń i usług zestawów SDK] [ devguide-sdks] Wyświetla listę zestawów SDK IoT Azure do tworzenia aplikacji usług i urządzeń, które współdziałają z Centrum IoT. Artykuł zawiera linki do dokumentacji interfejsu API.
* [Odwołanie — Obsługa MQTT Centrum IoT] [ devguide-mqtt] zawiera szczegółowe informacje o sposobie obsługi protokołu MQTT w Centrum IoT. Tym artykule opisano obsługę protokołu MQTT wbudowanych do zestawów SDK IoT Azure i zawiera informacje dotyczące korzystania z protokołu MQTT bezpośrednio.
* [Słownik] [ devguide-glossary] listę typowe terminy związane z Centrum IoT.

[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md
[devguide-pricing]: iot-hub-devguide-pricing.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[devguide-messages-d2c]: iot-hub-devguide-messages-d2c.md
[devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[devguide-custom]: iot-hub-devguide-messages-read-custom.md
[devguide-messages-c2d]: iot-hub-devguide-messages-c2d.md
[devguide-format]: iot-hub-devguide-messages-construct.md
[devguide-protocol]: iot-hub-devguide-protocols.md
