---
title: Azure Front Door - architektura routingu | Dokumenty firmy Microsoft
description: Ten artykuł pomaga zrozumieć aspekt widoku globalnego architektury drzwi frontowych.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: fd1f06bcb92ea97e0e9e9a6eefeac957031575a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471561"
---
# <a name="routing-architecture-overview"></a>Omówienie architektury routingu

Drzwi frontowe platformy Azure po odebraniu żądań klienta, a następnie odpowiada na nie (jeśli buforowanie jest włączone) lub przekazuje je do odpowiedniej wewnętrznej bazy danych aplikacji (jako odwrotnego serwera proxy).

</br>Istnieją możliwości optymalizacji ruchu podczas routingu do drzwi frontowych platformy Azure, a także podczas routingu do zaplecza.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Wybór środowiska drzwi przednich dla routingu ruchu (Anycast)

Routing do środowisk drzwiach frontowych platformy Azure wykorzystuje [anycast](https://en.wikipedia.org/wiki/Anycast) dla ruchu DNS (Domain Name System) i HTTP (Hypertext Transfer Protocol), więc ruch użytkownika zostanie przekierowany do najbliższego środowiska pod względem topologii sieci (najmniejsza liczba przeskoków). Ta architektura zazwyczaj oferuje lepsze czasy podróży w obie strony dla użytkowników końcowych (maksymalizując korzyści z podziału TCP). Drzwi frontowe organizuje swoje środowiska w podstawowe i rezerwowe "pierścienie".  Pierścień zewnętrzny ma środowiska, które są bliżej użytkowników, oferując niższe opóźnienia.  Pierścień wewnętrzny ma środowiska, które mogą obsługiwać pracy awaryjnej dla środowiska pierścienia zewnętrznego w przypadku, gdy występuje problem. Pierścień zewnętrzny jest preferowanym celem dla całego ruchu, ale pierścień wewnętrzny jest niezbędny do obsługi przepełnienia ruchu z pierścienia zewnętrznego. Jeśli chodzi o adresy VIP (Virtual Internet Protocol), każdy host frontendu lub domena obsługiwana przez drzwi frontowe jest przypisana do podstawowego adresu VIP, który jest ogłaszany przez środowiska zarówno w pierścieniu wewnętrznym, jak i zewnętrznym, a także rezerwowy vip, który jest ogłaszany tylko przez środowiska w pierścieniu wewnętrznym. 

</br>Ta ogólna strategia gwarantuje, że żądania od użytkowników końcowych zawsze docierają do najbliższego środowiska drzwi frontowych i że nawet jeśli preferowane środowisko drzwi frontowych jest w złej kondycji, ruch automatycznie przenosi się do najbliższego środowiska.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Podłączanie do środowiska drzwi przednich (Split TCP)

[Split TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) to technika zmniejszania opóźnień i problemów TCP przez przerwanie połączenia, które spowodowałoby wysoki czas podróży w obie strony na mniejsze kawałki.  Umieszczając środowiska drzwi frontowych bliżej użytkowników końcowych i kończąc połączenia TCP w środowisku drzwi wejściowych, jedno połączenie TCP z dużym czasem podróży w obie strony (RTT) do wewnętrznej bazy danych aplikacji jest dzielone na dwa połączenia TCP. Krótkie połączenie między użytkownikiem końcowym a środowiskiem drzwiami frontowymi oznacza, że połączenie zostanie nawiązane przez trzy krótkie objazdy zamiast trzech długich rund, oszczędzając opóźnienia.  Długie połączenie między środowiskiem drzwiami frontowymi a zapleczem może być wstępnie ustanowione i ponownie opublikowane w wielu połączeniach użytkowników końcowych, co ponownie pozwala zaoszczędzić czas połączenia TCP.  Efekt jest mnożony podczas ustanawiania połączenia SSL/TLS (Transport Layer Security), ponieważ istnieje więcej rund, aby zabezpieczyć połączenie.

## <a name="processing-request-to-match-a-routing-rule"></a>Żądanie przetwarzania zgodne z regułą routingu
Po ustanowieniu połączenia i wykonaniu uzgadniania SSL, gdy żądanie wyląduje w środowisku drzwiami frontowymi, dopasowanie reguły routingu jest pierwszym krokiem. To dopasowanie zasadniczo jest określanie ze wszystkich konfiguracji w drzwiach frontowych, które określone reguły routingu, aby dopasować żądanie do. Przeczytaj o tym, jak drzwi frontowe [dopasowują trasę,](front-door-route-matching.md) aby dowiedzieć się więcej.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identyfikowanie dostępnych zaplecza w puli wewnętrznej bazy danych dla reguły marszruty
Gdy drzwi frontowe ma dopasowanie do reguły routingu na podstawie żądania przychodzącego i jeśli nie ma buforowania, następnym krokiem jest wyciągnięcie stanu sondy kondycji dla puli wewnętrznej bazy danych skojarzonej z dopasowaną trasą. Przeczytaj o tym, jak drzwi frontowe monitoruje kondycji zaplecza przy użyciu [sondy kondycji,](front-door-health-probes.md) aby dowiedzieć się więcej.

## <a name="forwarding-the-request-to-your-application-backend"></a>Przekazywanie żądania do wewnętrznej bazy danych aplikacji
Na koniec przy założeniu, że nie jest skonfigurowane buforowanie, żądanie użytkownika jest przekazywane do "najlepszego" zaplecza na podstawie konfiguracji [metody routingu drzwiami frontowymi.](front-door-routing-methods.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
