---
title: Uczenia przez wzmacnianie - Personalizer
titleSuffix: Azure Cognitive Services
description: 'Witryny sieci Web o wysokiej wydajności i o dużym natężeniu ruchu i aplikacje mają dwa główne czynniki do rozważenia przy użyciu Personalizer skalowalności i wydajności: opóźnienia i przepływności szkolenia.'
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 06/07/2019
ms.author: edjez
ms.openlocfilehash: 242cc1b96c08cd79dc3e2ef5efbbe96a934b8ad3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058441"
---
# <a name="scalability-and-performance"></a>Skalowalność i wydajność

Witryny sieci Web o wysokiej wydajności i o dużym natężeniu ruchu i aplikacje mają dwa główne czynniki do rozważenia przy użyciu Personalizer skalowalności i wydajności:

* Utrzymywanie małego opóźnienia w wywołaniach interfejsu API ranga
* Upewnienie się, że przepływność szkolenia nadąża z danymi wejściowymi zdarzeń

Personalizacja może zwrócić rangę bardzo szybko z większością czas trwania wywołania do komunikacji za pośrednictwem interfejsu API REST w wersji dedykowanej. Azure będzie automatycznie skalowana możliwość szybko reagować na żądania.

##  <a name="low-latency-scenarios"></a>Scenariuszy o małych opóźnieniach

Niektóre aplikacje wymagają małych opóźnień, gdy zwracany jest rangę. Jest to konieczne:

* Aby uniemożliwić użytkownikowi oczekiwania zauważalne ilość czasu, przed wyświetleniem pozycjonowane zawartości.
* Ułatwiające na serwerze, na którym występuje ruch extreme uniknąć zajmowania deficytowe obliczeń czasu i połączenia sieciowe.

<!--

If your web site is scaled on your infrastructure, you can avoid making HTTP calls by hosting the Personalizer API in your own servers running a Docker container.

This change would be transparent to your application, other than using an endpoint URL referring to the running docker instances as opposed to an online service in the cloud.



### Extreme Low Latency Scenarios

If you require latencies under a millisecond, and have already tested using Personalizer via containers, please contact our support team so we can assess your scenario and provide guidance suited to your needs.

-->

## <a name="scalability-and-training-throughput"></a>Skalowalność i przepływność szkolenia

Działa personalizer, aktualizując model, który jest retrained na podstawie komunikaty wysyłane asynchronicznie przez Personalizer po rangi i interfejsów API osób trzecich. Te komunikaty są wysyłane przy użyciu Centrum zdarzeń platformy Azure dla aplikacji.

 Jest mało prawdopodobne, większość aplikacji będzie korzystał z maksymalną dołączania i szkolenia przepływność Personalizer. Gdy osiągnięcia ta wartość maksymalna nie spowolni działanie aplikacji, oznaczałoby że kolejek Centrum zdarzeń, które są wprowadzenie wypełnione wewnętrznie szybciej, niż ich mogą zostać wyczyszczone.

## <a name="how-to-estimate-your-throughput-requirements"></a>Sposób szacowania wymagań dotyczących przepływności

* Oszacuj średnią liczbę bajtów na zdarzenie klasyfikacji, dodając długości dokumentów JSON kontekstu i akcji.
* Podziel o rozmiarze 20MB na sekundę przez ta szacowana średnia liczba bajtów.

Na przykład jeśli Twoje średni ładunek ma funkcje 500, a każda z nich jest szacowany 20 znaków, każde zdarzenie jest około 10kb. Za pomocą tych oszacowań 20.000.000 / 10 000 = 2000 zdarzeń na sekundę, czyli około 173 milionów zdarzeń na dzień. 

Jeśli chcesz się połączyć te limity, skontaktuj się z naszym zespołem pomocy technicznej, uzyskać porady architektury.

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie i konfigurowanie Personalizer](how-to-settings.md).