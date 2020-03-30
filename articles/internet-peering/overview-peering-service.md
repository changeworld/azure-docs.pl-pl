---
title: Komunikacja równorzędna w Internecie a usługa komunikacji równorzędnej
titleSuffix: Azure
description: Komunikacja równorzędna w Internecie a usługa komunikacji równorzędnej
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 6c1205fcacd3d7228f1aecf1e603b66d9e1fcee5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75775705"
---
# <a name="internet-peering-vs-peering-service"></a>Komunikacja równorzędna w Internecie a usługa komunikacji równorzędnej

Komunikacja równorzędna w Internecie odnosi się do wszelkich połączeń między globalną siecią firmy Microsoft (AS8075) a siecią Operatorzy lub dostawcy usług. Usługodawca może stać się partnerem usługi komunikacji równorzędnej, implementując wymagania dotyczące partnerstwa usługi komunikacji równorzędnej opisane poniżej, aby zapewnić niezawodną i wydajną łączność publiczną z optymalnym routingiem od klienta do sieci firmy Microsoft.

## <a name="about-peering-service"></a>Peering Service — informacje
Usługa komunikacji równorzędnej to program partnerski z kluczowymi dostawcami usług, który zapewnia najlepszą w swojej klasie publiczną łączność z Internetem dla użytkowników korporacyjnych. Partnerzy, którzy są częścią programu będą mieli bezpośrednie, wysoce dostępne połączenia geograficznie nadmiarowe i zoptymalizowane routing do firmy Microsoft. Usługa komunikacji równorzędnej jest dodatkiem do portfolio łączności firmy Microsoft:
*   Usługa ExpressRoute dla prywatnej łączności z zasobami IaaS lub PaaS (obsługa prywatnej przestrzeni IP)
    *   Łączność oparta na partnerach
    *   Bezpośrednia łączność 100G z firmą Microsoft
*   Protokół IPSEC przez Internet zapewnia łączność sieci VPN z chmurą
*   Łączność SD-WAN z platformą Azure za pośrednictwem wirtualnej sieci WAN

Segment docelowy dla usługi komunikacji równorzędnej to łączność SaaS, klienci SD-WAN gotowi do robienia wyrwania się z Internetu w oddziale oraz klienci posiadający podwójną strategię MPLS i internet klasy korporacyjnej.

Głównym celem podczas łączenia się z usługą Microsoft Cloud powinno być zminimalizowanie opóźnień poprzez skrócenie czasu podróży w obie strony (RTT) z witryny użytkownika do sieci Microsoft Global Network, która jest publicznym szkieletem sieci firmy Microsoft, który łączy wszystkie centra danych firmy Microsoft i wiele punktów wejścia aplikacji w chmurze. Zobacz [Uzyskiwanie najlepszej łączności i wydajności w usłudze Office 365](https://techcommunity.microsoft.com/t5/Office-365-Blog/Getting-the-best-connectivity-and-performance-in-Office-365/ba-p/124694).

> [!div class="mx-imgBorder"]
> ![Obraz dostępu rozproszonego](./media/distributed-access.png)

Na rysunku powyżej każdego oddziału globalnego przedsiębiorstwa łączy się z najbliższą możliwą lokalizacją microsoft edge możliwe za pośrednictwem sieci partnera.

**Korzyści dla klientów usługi komunikacji równorzędnej:**
* Najlepsze publiczne routing przez Internet do usług Microsoft Cloud Services dla optymalnej wydajności i niezawodności.
* Możliwość wybrania preferowanego SP do łączenia się z usługą Microsoft Cloud.
* Informacje o ruchu drogowym, takie jak raportowanie opóźnień i monitorowanie prefiksów.
* Optymalne przeskoki sieciowe (AS Hops) firmy Microsoft Cloud.
* Analiza tras i statystyki — zdarzenia dla protokołu[BGP (Border](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)Gateway Protocol) (wykrywanie przecieków/porwania) i nieoptymalne routingu.

## <a name="peering-service-partnership-requirements"></a>Wymagania dotyczące partnerstwa usługi komunikacji równorzędnej
* Łączność z usługą Microsoft Cloud w lokalizacji najbliższej klientowi. Dostawca usług partnerskich będzie kierować ruch użytkownika do krawędzi firmy Microsoft najbliżej użytkownika. Podobnie w ruchu w kierunku użytkownika firma Microsoft będzie kierować ruch (przy użyciu tagu BGP) do lokalizacji krawędzi najbliżej użytkownika, a sp dostarczy ruch do użytkownika.
* Partner zachowa wysoką dostępną, wysoką przepustowość i łączność geograficznie nadmiarową z siecią Microsoft Global Network.
* Partner może wykorzystać istniejące peering do obsługi usługi komunikacji równorzędnej, jeśli spełnia wymagania

## <a name="faq"></a>Najczęściej zadawane pytania
Aby zapoznać się z często zadawanymi pytaniami, zobacz [Usługa komunikacji równorzędnej — często zadawane pytania](service-faqs.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o korzyściach dla klientów dzięki [usłudze komunikacji równorzędnej](https://docs.microsoft.com/azure/peering-service/).
* Dowiedz się więcej o krokach umożliwiających włączenie przewodnika partnera direct peering dla usługi komunikacji równorzędnej w programie [Peering Service](walkthrough-peering-service-all.md).
