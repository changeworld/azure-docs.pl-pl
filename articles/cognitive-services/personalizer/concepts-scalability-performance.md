---
title: Skalowalność i wydajność — personalizator
titleSuffix: Azure Cognitive Services
description: 'Witryny i aplikacje o wysokiej wydajności i dużym natężeniu ruchu mają dwa główne czynniki, które należy wziąć pod uwagę za pomocą personalizatora, aby skalowalności i wydajności: opóźnienia i przepustowość szkolenia.'
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: 5ac9a870cb05328f040febd0f8161a97f0982e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73490772"
---
# <a name="scalability-and-performance"></a>Skalowalność i wydajność

Witryny i aplikacje o wysokiej wydajności i dużym natężeniu ruchu mają dwa główne czynniki, które należy wziąć pod uwagę za pomocą personalizatora, aby skalowalności i wydajności:

* Utrzymywanie niskich opóźnień podczas wykonywania wywołań interfejsu API rangi
* Upewnianie się, że przepustowość szkolenia nadąża za wprowadzaniem danych o zdarzeniach

Personalizacja może szybko zwrócić rangę, przy czym większość czasu trwania wywołania jest przeznaczona dla komunikacji za pośrednictwem interfejsu API REST. Platforma Azure będzie automatycznie skalować możliwość szybkiego odpowiadania na żądania.

##  <a name="low-latency-scenarios"></a>Scenariusze o małym opóźnieniu

Niektóre aplikacje wymagają małych opóźnień podczas zwracania rangi. Konieczne są niskie opóźnienia:

* Aby uniemożliwić użytkownikowi oczekiwanie zauważalnej ilości czasu przed wyświetleniem zawartości w rankingu.
* Aby pomóc serwerowi, na który występuje ekstremalny ruch, należy unikać wiązania ograniczonego czasu obliczeniowego i połączeń sieciowych.


## <a name="scalability-and-training-throughput"></a>Skalowalność i przepustowość szkoleniowa

Personalizer działa przez aktualizowanie modelu, który jest przeszkolony na podstawie wiadomości wysyłanych asynchronicznie przez personalizator po rangi i nagrody interfejsów API. Te komunikaty są wysyłane przy użyciu usługi Azure EventHub dla aplikacji.

 Jest mało prawdopodobne, większość aplikacji osiągnie maksymalną przepustowość łączenia i szkolenia Personalizer. Osiągnięcie tego maksimum nie spowolni aplikacji, oznaczałoby to, że kolejki usługi Event Hub są wypełniane wewnętrznie szybciej niż można je wyczyścić.

## <a name="how-to-estimate-your-throughput-requirements"></a>Jak oszacować wymagania dotyczące przepływności

* Oszacuj średnią liczbę bajtów na zdarzenie rankingu, dodając długości kontekstu i akcji dokumentów JSON.
* Podziel 20 MB/s przez te szacowane średnie bajty.

Na przykład, jeśli średnia ładowność ma 500 obiektów, a każdy z nich ma około 20 znaków, każde zdarzenie ma około 10 kb. Z tych szacunków, 20,000,000 / 10,000 = 2,000 zdarzeń / s, czyli około 173 milionów zdarzeń dziennie. 

Jeśli osiągasz te limity, skontaktuj się z naszym zespołem pomocy technicznej w celu uzyskania porady na temat architektury.

## <a name="next-steps"></a>Następne kroki

[Tworzenie i konfigurowanie programu Personalizer](how-to-settings.md).