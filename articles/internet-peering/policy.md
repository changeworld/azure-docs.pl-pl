---
title: Zasady komunikacji równorzędnej firmy Microsoft
titleSuffix: Azure
description: Zasady komunikacji równorzędnej firmy Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775227"
---
# <a name="peering-policy"></a>Zasady komunikacji równorzędnej
Ogólne wymagania firmy Microsoft dotyczące sieci zostały wyjaśnione w poniższych sekcjach. Mają one zastosowanie zarówno do bezpośrednich żądań komunikacji równorzędnej, jak i żądań komunikacji równorzędnej programu Exchange.

## <a name="technical-requirements"></a>Wymagania techniczne

* W pełni nadmiarowa sieć o wystarczającej zdolności do wymiany ruchu bez zatorów.
* Peer będzie miał publicznie rutowalny numer systemu autonomicznego (ASN).
* Obsługiwane są zarówno IPv4, jak i IPv6, a firma Microsoft oczekuje ustanowienia sesji obu typów w każdej lokalizacji komunikacji równorzędnej.
* MD5 nie jest obsługiwany.
* **Szczegóły ASN:**
    * Firma Microsoft zarządza as8075 wraz z następującymi sieciami ASN: AS8068, AS8069, AS12076. Aby uzyskać pełną listę numerów ASN z komunikacją równorzędną AS8075, odwołaj się do programu AS-SET MICROSOFT.
    * Wszystkie strony komunikacji równorzędnej z firmą Microsoft zgadzają się nie akceptować tras z as12076 (trasa ekspresowa) w żadnym wypadku i powinny odfiltrować AS12076 na wszystkich elementach równorzędnych.
* **Zasady routingu:**
    * Peer będzie miał co najmniej jeden publicznie rutable /24.
    * Firma Microsoft zastąpi odebrane programy Multi-Exit Discriminators (MED).
    * Firma Microsoft woli otrzymywać tagi społeczności BGP od elementów równorzędnych, aby wskazać pochodzenie trasy.
    * Peer oczekuje się zarejestrować swoje trasy w publicznej bazie danych Internet Routing Registry (IRR), w celu filtrowania i podejmie wysiłki w dobrej wierze, aby utrzymać te informacje na bieżąco.
    * Sugerujemy, aby elementy równorzędne ustawiało maksymalny prefiks tras 1000 (IPv4) i 100 (IPv6) w sesjach komunikacji równorzędnej z firmą Microsoft.
    * O ile nie uzgodniono wcześniej, oczekuje się, że elementy równorzędne będą ogłaszać spójne trasy we wszystkich lokalizacjach, w których są równorzędne z firmą Microsoft.
    * Ogólnie rzecz biorąc sesje komunikacji równorzędnej z AS8075 anonsują wszystkie trasy AS-MICROSOFT. Połączenia międzysystemowe AS8075 w Afryce i Azji mogą być ograniczone do tras istotnych dla danego regionu.
    * Żadna ze stron nie ustanowi trasy statycznej, trasy ostatniej szansy, ani w inny sposób nie wyśle ruchu do drugiej strony na trasę nieokaszacą za pośrednictwem BGP.
    * Oczekuje się, że partnerzy będą przestrzegać standardów branżowych [MANRS](https://www.manrs.org/) w zakresie bezpieczeństwa tras.

## <a name="operational-requirements"></a>Wymagania operacyjne
* W pełni obsadzone 24x7 Network Operations Center (NOC), zdolne do pomocy w rozwiązywaniu wszystkich problemów technicznych i wydajnościowych, naruszenia zabezpieczeń, ataki typu "odmowa usługi" lub wszelkie inne nadużycia pochodzące z peer lub ich klientów.
* Peers oczekuje się, że pełny i aktualny profil na [PeeringDB](https://www.peeringdb.com) w tym 24x7 NOC e-mail z domeny firmowej i numeru telefonu. Używamy tych informacji do sprawdzania poprawności danych peera, takich jak informacje NOC, techniczne informacje kontaktowe i ich obecność w obiektach komunikacji równorzędnej itp. Osobiste konta Yahoo, Gmail i hotmail nie są akceptowane.

## <a name="physical-connection-requirements"></a>Wymagania dotyczące połączenia fizycznego
* Lokalizacje, w których można połączyć się z firmą Microsoft w celu bezpośredniego komunikacji równorzędnej lub komunikacji równorzędnej programu Exchange, są wymienione w [aplikacji PeeringDB](https://www.peeringdb.com/net/694)
* **Komunikacja równorzędna programu Exchange:**
    * Połączenie musi być przez światłow jednym trybie przy użyciu odpowiedniej optyki 10Gbps.
    * Oczekuje się, że elementy równorzędne zmodernizuje swoje porty, gdy szczytowe wykorzystanie przekracza 50%.
* **Bezpośrednia komunikacja równorzędna:**
    * Połączenie musi być przez światłow jednym trybie przy użyciu odpowiedniej optyki 10Gbps lub 100Gbps.
    * Firma Microsoft będzie kierować komunikacją równorzędną tylko z dostawcami usług internetowych lub sieci.
    * Oczekuje się, że rówieśnicy zmodernizują swoje porty, gdy szczytowe wykorzystanie przekracza 50% i utrzymają zróżnicowaną przepustowość w każdym metrze, w jednej lokalizacji lub w kilku lokalizacjach w metrze.
    * Każda komunikacja bezpośrednia składa się z dwóch połączeń z dwoma routerami microsoft edge z routerów równorzędnych znajdujących się na krawędzi elementu Równorzędnego. Firma Microsoft wymaga dwóch sesji Protokołu BGP w przypadku tych połączeń. Element równorzędny może zdecydować, aby nie wdrażać urządzeń nadmiarowych na ich końcu.

## <a name="traffic-requirements"></a>Wymagania dotyczące ruchu
* Elementy równorzędne w stosunku do komunikacji równorzędnej programu Exchange muszą mieć co najmniej 200 Mb ruchu i mniej niż 2 Gb.  Dla ruchu przekraczającego 2Gb Direct peering powinny być sprawdzane.
* W przypadku komunikacji bezpośredniej ruch z sieci do firmy Microsoft musi spełniać wymagania poniżej minimalnego.

    | Obszar geograficzny                      | Minimalny ruch do firmy Microsoft   |
    | :----------------------- |:-------------------------------|
    | Afryka                   | 500 Mb/s                       |
    | APAC (z wyjątkiem Indii)      |   2 Gb/s                       |
    | APAC (tylko Indie)        | 500 Mb/s                       |
    | Europa                   |   2 Gb/s                       |
    | Latam                    |   2 Gb/s                       |
    | Bliski Wschód              | 500 Mb/s                       |
    | Nie dotyczy                       |   2 Gb/s                       |

* **Różnorodności:**
    * W NA, Europa, APAC i LATAM, łączą połączenia w co najmniej trzech geograficznie zróżnicowanych lokalizacjach, jeśli jest to możliwe, i utrzymują zróżnicowaną zdolność do umożliwienia pracy awaryjnej w każdym metrze.
    * W Afryce, na Bliskim Wschodzie i w Indiach, połączyć w jak największej liczby różnych miejscach, jak to możliwe. Musi utrzymać wystarczającą zróżnicowaną zdolność do zapewnienia ruchu w regionie.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o krokach konfigurowania komunikacji bezpośredniej z firmą Microsoft, postępuj zgodnie z [wskazówki dotyczące komunikacji równorzędnej direct](walkthrough-direct-all.md).
* Aby dowiedzieć się więcej o krokach konfigurowania komunikacji równorzędnej programu Exchange z firmą Microsoft, postępuj zgodnie z instrukcją dotyczącą [komunikacji równorzędnej programu Exchange](walkthrough-exchange-all.md).