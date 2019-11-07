---
title: Cennik Azure Security Center warstw
description: Ten artykuł zawiera informacje na temat cen Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 0c3fdc48d9b3bc77b629d4d6f1da081d70658c88
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73664261"
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>Uaktualnij do warstwy Standardowa Security Center na potrzeby zwiększonych zabezpieczeń
Usługa Azure Security Center zapewnia ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami na potrzeby obciążeń uruchamianych na platformie Azure, lokalnie i w innych chmurach. Zapewnia widoczność i kontrolę nad obciążeniami w chmurze hybrydowej, aktywną obroną, która zmniejsza narażenie na zagrożenia oraz Inteligentne wykrywanie, które ułatwiają szybkie rozwijanie ataków cybernetycznymi.

## <a name="pricing-tiers"></a>Warstwy cenowe
Usługa Security Center jest oferowana w dwóch warstwach:

- Warstwa **bezpłatna** jest włączana we wszystkich subskrypcjach platformy Azure, gdy po raz pierwszy odwiedzasz pulpit nawigacyjny Azure Security Center w Azure Portal lub włączono programowo za pośrednictwem interfejsu API. Warstwa Bezpłatna zapewnia zasady zabezpieczeń, ciągłą ocenę zabezpieczeń i zalecenia dotyczące zabezpieczeń, które ułatwiają ochronę zasobów platformy Azure.
- Warstwa **standardowa** rozszerza możliwości warstwy Bezpłatna do obciążeń działających w prywatnych i innych chmurach publicznych, zapewniając ujednolicone Zarządzanie zabezpieczeniami i ochronę przed zagrożeniami w ramach obciążeń chmury hybrydowej. Warstwa standardowa dodaje także zaawansowane możliwości wykrywania zagrożeń, które wykorzystują wbudowaną analizę behawioralną i uczenie maszynowe, aby identyfikować ataki i luki w zabezpieczeniach, kontrolę dostępu i aplikacji w celu ograniczenia narażenia na ataki sieciowe i złośliwe oprogramowanie. szczegółowe. Możesz bezpłatnie wypróbować warstwę Standardowa. Standard Security Center obsługuje zasoby platformy Azure, w tym maszyny wirtualne, zestawy skalowania maszyn wirtualnych, App Service, serwery SQL i konta magazynu. Jeśli masz Azure Security Center Standard, możesz zrezygnować z obsługi na podstawie typu zasobu. 

Większość ocen zabezpieczeń warstwy Bezpłatna dla maszyn wirtualnych oraz wielu alertów zabezpieczeń warstwy Standardowa wymaga instalacji funkcji Microsoft Monitoring Agent (MMA). Możesz włączyć automatyczne Inicjowanie obsługi administracyjnej Security Center, aby automatycznie wdrożyć agenta dla maszyn wirtualnych platformy Azure.

## <a name="try-standard-free-for-30-days"></a>Wypróbuj usługę Standard bezpłatnie przez 30 dni
Korzystanie z warstwy Standardowa jest bezpłatne przez pierwsze 30 dni. Po upływie 30 dni, jeśli chcesz kontynuować korzystanie z usługi, automatycznie zaczniemy naliczać opłaty za użycie.

Możesz uaktualnić całą subskrypcję platformy Azure do warstwy Standardowa, która jest dziedziczona przez wszystkie zasoby w ramach subskrypcji.

Aby uzyskać warstwę standardową:

1. Wybierz pozycję **Cennik ustawienia &** w menu głównym **Security Center** .
2. Wybierz subskrypcję, którą chcesz uaktualnić do wersji Standard.
3. Wybierz **warstwę cenową**.
4. Wybierz pozycję **standardowa** , aby przeprowadzić uaktualnienie.
5. Kliknij pozycję **Zapisz**.

(Ceny w obrazie są udostępniane tylko do celów informacyjnych) [cennik Security Center![](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> Aby włączyć wszystkie funkcje usługi Security Center, należy najpierw do subskrypcji zawierającej odpowiednie maszyny wirtualne zastosować warstwę cenową Standardowa. Konfigurowanie cen dla obszaru roboczego nie umożliwia dostępu just in Time do maszyny wirtualnej, adaptacyjnych kontroli aplikacji i wykrywania sieci dla zasobów platformy Azure.
>

## <a name="why-upgrade-to-standard"></a>Dlaczego warto przeprowadzić uaktualnienie do wersji Standard?
Security Center oferuje zwiększone zabezpieczenia i ochronę przed zagrożeniami dla obciążeń chmury hybrydowej, w tym:

- **Bezpieczeństwo hybrydowe** — Uzyskaj ujednolicony widok zabezpieczeń we wszystkich obciążeniach lokalnych i w chmurze. Stosuj zasady zabezpieczeń i stale oceniaj bezpieczeństwo obciążeń chmury hybrydowej w celu zapewnienia zgodności ze standardami zabezpieczeń. Zbieranie, wyszukiwanie i analizowanie danych zabezpieczeń z wielu źródeł, w tym zapór i innych rozwiązań partnerskich.
- **Zaawansowane wykrywanie zagrożeń** — Użyj zaawansowanej analizy i Microsoft Intelligent Security Graph, aby uzyskać krawędzie przed rozwojem ataków cybernetycznymi. Wykorzystaj wbudowaną analizę behawioralną i uczenie maszynowe do identyfikowania ataków i luk typu zero day. Monitoruj sieci, maszyny i usługi w chmurze pod kątem przychodzących ataków i działań po naruszeniu zabezpieczeń. Usprawnij badanie dzięki interaktywnym narzędziom i kontekstowej analizie zagrożeń.
- **Kontrola dostępu i aplikacji** — blokowanie złośliwego oprogramowania i innych niechcianych aplikacji przez zastosowanie listy dozwolonychowych zaleceń dotyczących uczenia maszynowego dostosowanych do określonych obciążeń. Ogranicz obszar ataków sieci z dostępem just-in-Time do portów zarządzania na maszynach wirtualnych platformy Azure. Radykalnie zmniejsza to narażenie na rozżycie i inne ataki sieciowe.
- **Funkcje zabezpieczeń kontenera** — korzyści wynikające z zarządzania lukami w zabezpieczeniach i wykrywania zagrożeń w czasie rzeczywistym w środowiskach kontenerów. Po włączeniu zasobu rejestrów kontenerów może upłynąć do 12hrs do momentu włączenia wszystkich funkcji.


## <a name="next-steps"></a>Następne kroki
W tym artykule wprowadzono cenniki dotyczące Security Center. Aby dowiedzieć się więcej na temat zwiększonych zabezpieczeń warstwy standardowej i zaawansowanej ochrony przed zagrożeniami, zobacz:

- [Zaawansowane wykrywanie zagrożeń](security-center-threat-report.md)
- [Kontrola dostępu just in Time do maszyny wirtualnej](security-center-just-in-time.md)
- [Omówienie zabezpieczeń kontenera](container-security.md)
- [Szczegóły cennika w wybranej walucie i zgodnie z Twoim regionem](https://azure.microsoft.com/pricing/details/security-center/)