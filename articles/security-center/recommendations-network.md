---
title: Azure Security Center zalecenia dotyczące sieci
description: Ten artykuł zawiera listę zaleceń dotyczących zabezpieczeń Azure Security Center, które ułatwiają ochronę zasobów sieciowych.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2019
ms.author: memildin
ms.openlocfilehash: 1b8d84286d14949c007d1ece3d089a7606464aaf
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781971"
---
# <a name="network-recommendations---reference-guide"></a>Zalecenia dotyczące sieci — Przewodnik referencyjny

Ten artykuł zawiera pełną listę zaleceń, które mogą pojawić się w Azure Security Center dotyczących topologii sieci i punktów końcowych dostępnych w Internecie.

Aby dowiedzieć się, jak je znaleźć i jak rozwiązać, zobacz [tutaj](security-center-network-recommendations.md).

## <a name="network-recommendations"></a>Zalecenia dotyczące sieci

|Nazwa zalecenia|Opis|Ważność|Wskaźnik bezpieczeństwa|Typ zasobu|
|----|----|----|----|----|----|
|Sieciowe grupy zabezpieczeń na poziomie podsieci powinny być włączone|Włącz sieciowe grupy zabezpieczeń, aby kontrolować dostęp sieciowy do zasobów wdrożonych w podsieciach.|Wysoki/średni|30|Podsieć|
|Maszyny wirtualne powinny być skojarzone z sieciową grupą zabezpieczeń|Włącz sieciowe grupy zabezpieczeń, aby kontrolować dostęp sieciowy do maszyn wirtualnych.|Wysoki/średni|30|Maszyna wirtualna|
|Dostęp powinien być ograniczony do ograniczeń sieciowych grup zabezpieczeń z maszynami wirtualnymi z Internetu|Podwyższenie poziomu bezpieczeństwa sieci maszyn wirtualnych połączonych z Internetem przez ograniczenie dostępu do istniejących reguł zezwalania.|Wysoka|20|Maszyna wirtualna|
|Reguły dla aplikacji sieci Web na IaaS sieciowych grup zabezpieczeń powinny być zaostrzone|Zawzmacniaj grupę zabezpieczeń sieci (sieciowej grupy zabezpieczeń) dla maszyn wirtualnych, na których działają aplikacje sieci Web, z regułami sieciowej grupy zabezpieczeń, które są nadmiernie ograniczające w odniesieniu do portów aplikacji sieci Web.|Wysoka|20|Maszyna wirtualna|
|Dostęp do App Services powinien być ograniczony|Ogranicz dostęp do App Services, zmieniając konfigurację sieci, aby odmówić ruchu przychodzącego z zakresów, które są zbyt szerokie.|Wysoka|10|Usługa App Service|
|Porty zarządzania powinny być zamknięte na maszynach wirtualnych|Ogranicz grupę zabezpieczeń sieci maszyn wirtualnych, aby ograniczyć dostęp do portów zarządzania.|Wysoka|10|Maszyna wirtualna|
Należy włączyć Standard DDoS Protection|Ochrona sieci wirtualnych zawierających aplikacje z publicznymi adresami IP przez włączenie usługi DDoS Protection Standard. Ochrona DDoS umożliwia łagodzenie ataków między pomiarami i protokołami sieciowymi.|Wysoka|10|Sieć wirtualna|
|Przekazywanie adresów IP na maszynie wirtualnej powinno być wyłączone|Wyłącz przekazywanie adresów IP. Po włączeniu przekazywania adresów IP na karcie sieciowej maszyny wirtualnej komputer może odbierać ruch skierowany do innych miejsc docelowych. Przekazywanie adresów IP jest rzadko wymagane (na przykład w przypadku używania maszyny wirtualnej jako sieciowego urządzenia wirtualnego), dlatego powinno być przeglądane przez zespół ds. zabezpieczeń sieci.|Średnie|10|Maszyna wirtualna|
|Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Włącz dostęp tylko do protokołu HTTPS dla aplikacji sieci Web. Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej.|Średnie|20|Aplikacja internetowa|
|Kontrola dostępu do sieci just in Time powinna być stosowana na maszynach wirtualnych|Zastosuj kontrolę dostępu maszyny wirtualnej just-in-Time (JIT), aby trwale zablokować dostęp do wybranych portów i umożliwić autoryzowanym użytkownikom otwieranie ich za pośrednictwem JIT tylko przez ograniczoną ilość czasu.|Wysoka|20|Maszyna wirtualna|
|Aplikacje funkcji powinny być dostępne tylko za pośrednictwem protokołu HTTPS|Włącz dostęp tylko do protokołu HTTPS dla aplikacji funkcji. Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej.|Średnie|20|Aplikacja funkcji|
|Należy włączyć bezpieczny transfer do kont magazynu|Włącz bezpieczny transfer do kont magazynu. Bezpieczny transfer to opcja, która wymusza akceptowanie przez konto magazynu żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane przed atakami z warstwy sieci, takimi jak Man-in-the-Middle, podsłuchiwanie i przejmowanie sesji.|Wysoka|20|Konto magazynu|


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Monitorowanie tożsamości i dostępu](security-center-identity-access.md)
* [Ochrona maszyn i aplikacji](security-center-virtual-machine-protection.md)
* [Ochrona usługi Azure SQL](security-center-sql-service-recommendations.md)

