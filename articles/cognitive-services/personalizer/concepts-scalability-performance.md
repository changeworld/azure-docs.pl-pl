---
title: Skalowalność i wydajność — Personalizowanie
titleSuffix: Azure Cognitive Services
description: 'W przypadku witryn sieci Web o wysokiej wydajności i dużych ruchu istnieją dwa główne czynniki, które należy wziąć pod uwagę przy użyciu programu Personalizacja w celu zapewnienia skalowalności i wydajności: opóźnienia i uczenie.'
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: diberry
ms.openlocfilehash: d116f6bd389b1404ea723c965111cd05880e6c30
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662817"
---
# <a name="scalability-and-performance"></a>Skalowalność i wydajność

W przypadku witryn sieci Web o wysokiej wydajności i dużych ruchu istnieją dwa główne czynniki, które należy wziąć pod uwagę w celu zapewnienia skalowalności i wydajności:

* Zachowywanie małych opóźnień podczas wykonywania wywołań interfejsu API rangi
* Zapewnianie, że przepływność szkoleń jest zachowywana przy użyciu danych wejściowych zdarzeń

Personalizacja może bardzo szybko zwracać rangę z większością czasu trwania wywołań, która jest przeznaczona do komunikacji za pomocą interfejsu API REST. Platforma Azure automatycznie skaluje możliwość szybkiego reagowania na żądania.

##  <a name="low-latency-scenarios"></a>Scenariusze o małym opóźnieniu

Niektóre aplikacje wymagają małych opóźnień podczas zwracania rangi. Jest to konieczne:

* Aby uniemożliwić użytkownikowi oczekiwanie na zauważalny czas przed wyświetleniem sklasyfikowanej zawartości.
* Aby ułatwić serwerowi, który ma wyższy ruch, unikaj nawiązywania nieprawidłowych czasu obliczeń i połączeń sieciowych.

<!--

If your web site is scaled on your infrastructure, you can avoid making HTTP calls by hosting the Personalizer API in your own servers running a Docker container.

This change would be transparent to your application, other than using an endpoint URL referring to the running docker instances as opposed to an online service in the cloud.



### Extreme Low Latency Scenarios

If you require latencies under a millisecond, and have already tested using Personalizer via containers, please contact our support team so we can assess your scenario and provide guidance suited to your needs.

-->

## <a name="scalability-and-training-throughput"></a>Skalowalność i przepływność szkoleń

Personalizacja działa, aktualizując model, który jest ponownie przemieszczony na podstawie komunikatów wysyłanych asynchronicznie przez personalizację po rangi i nagrodach interfejsów API. Te komunikaty są wysyłane przy użyciu centrum zdarzeń platformy Azure dla aplikacji.

 Jest mało prawdopodobne, że większość aplikacji osiągnie maksymalną przepustowość i uczenie się dla personalizacji. Osiągnięcie tej wartości maksymalnej nie spowoduje spowolnienia działania aplikacji. spowoduje to, że kolejki centrum zdarzeń są wprowadzane wewnętrznie w trybie szybszym niż można je wyczyścić.

## <a name="how-to-estimate-your-throughput-requirements"></a>Jak oszacować wymagania dotyczące przepływności

* Oszacuj średnią liczbę bajtów na zdarzenie klasyfikacji, dodając długości dokumentów JSON kontekstu i akcji.
* Podziel baza na sekundę przez ten szacowany średnią liczbę bajtów.

Na przykład jeśli średni ładunek ma 500 funkcji, a każdy z nich jest szacowany 20 znaków, każde zdarzenie jest około 10 KB. W przypadku tych szacunków 20 000 000/10 000 = 2 000 Events/s, które dotyczą zdarzeń 173 000 000 Events/dzień. 

Jeśli zbliżasz się do tych limitów, skontaktuj się z naszym zespołem pomocy technicznej, aby uzyskać poradę dotyczącą architektury.

## <a name="next-steps"></a>Następne kroki

[Utwórz i skonfiguruj personalizację](how-to-settings.md).