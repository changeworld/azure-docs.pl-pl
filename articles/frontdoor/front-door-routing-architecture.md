---
title: Usługa Azure drzwiami frontowymi — architektura routingu | Dokumentacja firmy Microsoft
description: Ten artykuł ułatwia zapoznanie aspekt globalny widok danych drzwiami frontowymi architektury.
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
ms.openlocfilehash: 6af5e7c7d8788dffa8f144b2ee77c291ceda86c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736290"
---
# <a name="routing-architecture-overview"></a>Omówienie architektury routingu

Usługi Azure Service drzwiami frontowymi po odebraniu Twój klient zażąda następnie albo odpowiedzi je (jeśli jest włączone buforowanie) lub będzie przekazywać je do odpowiedniej aplikacji wewnętrznej bazy danych (jako zwrotny serwer proxy).

</br>Ma możliwości, aby zoptymalizować ruch, gdy routing na platformie Azure drzwi wejściowe, a także gdy routingu do zaplecza.

## <a name = "anycast"></a>Wybierając środowisko drzwiami frontowymi (emisji dowolnej) routingu ruchu

Routing na wejściu Azure wykorzystuje środowisk [emisji dowolnej](https://en.wikipedia.org/wiki/Anycast) DNS (Domain Name System) i ruch HTTP (Hypertext Transfer Protocol), więc ruch użytkownika zostanie wysłany do najbliższego środowisku pod względem topologii sieci (najmniejszą liczbę przeskoków). Ta architektura zazwyczaj udostępnia lepiej czasy błądzenia dla użytkowników końcowych (maksymalizacja korzyści TCP podziału). Drzwiami frontowymi organizuje środowiska do podstawowych i rezerwowej "pierścieniach".  Zewnętrznego ma środowisk, w których są bliżej użytkowników, oferując mniejsze opóźnienia.  Pierścień wewnętrzny ma środowisk, które może obsłużyć trybu failover dla środowiska pierścień zewnętrzny, w przypadku, gdy problem występuje. Pierścień zewnętrzny jest preferowany cel dla całego ruchu, ale pierścień wewnętrzny jest niezbędne do obsługi ruchu przepełnienia z zewnętrznego. Pod względem adresów VIP (adresy wirtualnej Internet Protocol) poszczególne hosty frontonu lub domeny obsługiwanej przez drzwiami frontowymi jest przypisany podstawowym Wirtualnym zostanie zaanonsowana przez środowisk w pierścienia wewnętrznych i zewnętrznych, a także rezerwowego adresów VIP, który tylko zostanie zaanonsowana przez środowiska pierścienia wewnętrznego. 

</br>Ten ogólny strategia żądań od użytkowników końcowych będzie zawsze łączyć najbliższego środowiska drzwiami frontowymi i że nawet jeśli preferowanym środowisku drzwiami frontowymi jest w złej kondycji następnie ruch automatycznie przechodzi do następnego najbliższej środowiska.

## <a name = "splittcp"></a>Trwa łączenie ze środowiskiem drzwiami frontowymi (Podziel TCP)

[Podziel TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) to technika, aby zmniejszyć opóźnienia i problemy z protokołu TCP, dzieląc połączenie, które zostałyby naliczone wysoki czas błądzenia na mniejsze części.  Wprowadzenie do środowisk drzwiami frontowymi bliżej użytkowników końcowych i zakończenie połączenia protokołu TCP w środowisku drzwiami frontowymi, jedno połączenie TCP z dużych czas błądzenia (RTT) do zaplecza aplikacji zostanie podzielona na dwa połączenia TCP. Krótkie połączenie między użytkownika końcowego i środowiska drzwiami frontowymi oznacza, że pobiera nawiązane połączenie, ponad trzech krótkich rund zamiast trzech długie rund, zapisywanie opóźnienia.  Długie połączenia między środowiskiem drzwiami frontowymi i wewnętrznej bazy danych można wstępnie ustalonymi i ponownie używane w wielu wywołań przez użytkownika końcowego, ponownie oszczędność czasu połączenia TCP.  Efekt jest mnożona podczas ustanawiania połączenia SSL/TLS (Transport Layer Security), ponieważ więcej rund do zabezpieczenia połączenia.

## <a name="processing-request-to-match-a-routing-rule"></a>Przetwarza żądanie, aby dopasować reguły routingu
Po ustanowieniu połączenia i wykonywania SSL uzgadniania, gdy żądanie umieszczać swoje dokumenty w środowisku drzwiami frontowymi pasujących do reguły routingu jest pierwszym krokiem. To dopasowanie zasadniczo jest określenie ze wszystkich konfiguracji z przodu drzwi biblioteki, które określonego routingu regułę pasuje do żądania do. Przeczytaj, jak działa drzwiami frontowymi [dopasowania trasy](front-door-route-matching.md) Aby dowiedzieć się więcej.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identyfikowanie dostępnych zaplecza w puli zaplecza dla reguły routingu
Po wejściu ma dopasowania dla reguły routingu na podstawie żądania przychodzącego, a nie występuje buforowanie, następnym krokiem jest pobierania stanu sondy kondycji dla puli zaplecza, skojarzone z dopasowanej trasy. Przeczytaj, jak drzwiami frontowymi monitoruje kondycję zaplecza za pomocą [sondy kondycji](front-door-health-probes.md) Aby dowiedzieć się więcej.

## <a name="forwarding-the-request-to-your-application-backend"></a>Żądania z wewnętrzną bazą danych aplikacji
Na koniec, zakładając, że nie występuje buforowanie skonfigurowane, żądanie użytkownika jest przekazywany do "Najważniejsze" wewnętrznej bazy danych na podstawie Twojej [metody routingu opartego na wejściu](front-door-routing-methods.md) konfiguracji.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
