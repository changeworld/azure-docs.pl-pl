---
title: Cennik warstw usługi Azure Security Center
description: Usługa Azure Security Center jest oferowana w dwóch warstwach — bezpłatnej i standardowej. Na tej stronie pokazano, jak uaktualnić z bezpłatnego do standardowego.
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
ms.date: 12/13/2019
ms.author: memildin
ms.openlocfilehash: 60a88e667918533f2c507846fa75b0e036ba5262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921287"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>Uaktualnienie do warstwy Standardowa w celu zwiększenia zabezpieczeń
Usługa Azure Security Center zapewnia ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami na potrzeby obciążeń uruchamianych na platformie Azure, lokalnie i w innych chmurach. Zapewnia widoczność i kontrolę nad obciążeniami chmury hybrydowej, aktywną obronę, która zmniejsza narażenie na zagrożenia, oraz inteligentne wykrywanie, które pomaga nadążać za szybko zmieniającymi się atakami cybernetycznymi.

## <a name="pricing-tiers"></a>Warstwy cenowe
Usługa Security Center jest oferowana w dwóch warstwach:

- **Bezpłatna** warstwa jest włączona we wszystkich subskrypcjach platformy Azure po przejściu pulpitu nawigacyjnego Usługi Azure Security Center w witrynie Azure portal po raz pierwszy lub jeśli jest włączona programowo za pośrednictwem interfejsu API. Warstwa bezpłatna zawiera zasady zabezpieczeń, ciągłą ocenę zabezpieczeń i zalecenia dotyczące zabezpieczeń, które ułatwią ochronę zasobów platformy Azure.
- Warstwa **Standardowa** rozszerza możliwości warstwy Bezpłatna na obciążenia działające w chmurach prywatnych i innych publicznych, zapewniając ujednolicone zarządzanie zabezpieczeniami i ochronę przed zagrożeniami w przypadku obciążeń chmury hybrydowej. Warstwa standardowa dodaje również funkcje ochrony przed zagrożeniami, które wykorzystują wbudowaną analizę behawioralną i uczenie maszynowe do identyfikowania ataków i exploitów zero-day, kontroli dostępu i aplikacji w celu zmniejszenia narażenia na ataki sieciowe i złośliwe oprogramowanie i nie tylko. Ponadto warstwa standardowa dodaje skanowanie luk w zabezpieczeniach dla maszyn wirtualnych. Możesz wypróbować warstwę standardową za darmo. Standard Usługi Security Center obsługuje zasoby platformy Azure, w tym maszyny wirtualne, zestawy skalowania maszyn wirtualnych, usługi app service, serwery SQL i konta magazynu. Jeśli masz standard Usługi Azure Security Center, możesz zrezygnować z pomocy technicznej na podstawie typu zasobu. 

Większość ocen zabezpieczeń warstwy bezpłatnej dla maszyn wirtualnych, a także wiele alertów zabezpieczeń warstwy standardowej wymaga zainstalowania funkcji programu Microsoft Monitoring Agent (MMA). Można włączyć automatyczne aprowizacji w usłudze Security Center, aby automatycznie wdrożyć agenta dla maszyn wirtualnych platformy Azure.

## <a name="try-standard-tier-free-for-30-days"></a>Wypróbuj warstwę standardową za darmo przez 30 dni
Warstwa standardowa jest bezpłatna przez pierwsze 30 dni. Pod koniec 30 dni, jeśli zdecydujesz się kontynuować korzystanie z usługi, automatycznie rozpoczniemy ładowanie za korzystanie.

Można uaktualnić całą subskrypcję platformy Azure do warstwy standardowej, która jest dziedziczona przez wszystkie zasoby w ramach subskrypcji.

Aby uzyskać warstwę standardową:

1. Wybierz **ustawienia & cennika** w menu głównym **Centrum zabezpieczeń.**
2. Wybierz subskrypcję, którą chcesz uaktualnić do standardu.
3. Wybierz **warstwę cenową**.
4. Wybierz **standard,** aby uaktualnić.
5. Kliknij przycisk **Zapisz**.

[![Cennik centrum zabezpieczeń](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> Aby włączyć wszystkie funkcje usługi Security Center, należy zastosować standardową warstwę cenową do subskrypcji zawierającej odpowiednie maszyny wirtualne. Konfigurowanie cen dla obszaru roboczego nie umożliwia dostępu do maszyn wirtualnych w czasie, formantów aplikacji adaptacyjnych i wykrywania sieci zasobów platformy Azure.
>

## <a name="why-upgrade-to-standard"></a>Dlaczego warto uaktualnić do standardu?
Usługa Security Center oferuje zwiększone zabezpieczenia i ochronę przed zagrożeniami dla obciążeń chmury hybrydowej, w tym:

- **Zabezpieczenia hybrydowe** — uzyskaj ujednolicony widok zabezpieczeń we wszystkich obciążeniach lokalnych i chmurowych. Zastosuj zasady zabezpieczeń i stale oceniaj bezpieczeństwo obciążeń chmury hybrydowej, aby zapewnić zgodność ze standardami zabezpieczeń. Zbieraj, wyszukuj i analizuj dane zabezpieczeń z wielu źródeł, w tym z zapór i innych rozwiązań partnerskich.
- **Alerty zabezpieczeń** — skorzystaj z zaawansowanej analizy i inteligentnego wykresu zabezpieczeń firmy Microsoft, aby uzyskać przewagę nad ewoluującymi atakami cybernetycznymi. Wykorzystaj wbudowaną analizę behawioralną i uczenie maszynowe, aby identyfikować ataki i exploity typu zero-day. Monitoruj sieci, maszyny i usługi w chmurze pod kątem ataków przychodzących i działań po naruszeniu. Usprawnij dochodzenie za pomocą interaktywnych narzędzi i kontekstowej analizy zagrożeń.
- **Skanowanie luk w zabezpieczeniach maszyn wirtualnych** — łatwe wdrażanie skanera na wszystkich maszynach wirtualnych, który zapewnia najbardziej zaawansowane w branży rozwiązanie do zarządzania lukami w zabezpieczeniach. Wyświetlanie, badanie i korygowanie wyników bezpośrednio w centrum zabezpieczeń. 
- **Kontrola dostępu i aplikacji** — blokowanie złośliwego oprogramowania i innych niechcianych aplikacji przez zastosowanie zaleceń opartych na uczeniu maszynowym na białej liście dostosowanych do określonych obciążeń. Zmniejsz obszar ataku sieciowego dzięki kontrolowanemu dostępowi do portów zarządzania na maszynach wirtualnych platformy Azure. To drastycznie zmniejsza narażenie na brutalną siłę i inne ataki sieciowe.
- **Funkcje zabezpieczeń kontenerów** — korzystaj z zarządzania lukami w zabezpieczeniach i ochrony przed zagrożeniami w czasie rzeczywistym w środowiskach konteneryzowanych. Po włączeniu zasobu rejestrów kontenerów może upłynąć do 12 godzin, aż wszystkie funkcje zostaną włączone.


## <a name="next-steps"></a>Następne kroki
W tym artykule wprowadzono do cen dla centrum zabezpieczeń. Aby dowiedzieć się więcej o ulepszonych zabezpieczeniach warstwy Standard i zaawansowanej ochronie przed zagrożeniami, zobacz:

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](threat-protection.md)
- [Kontrola dostępu do maszyn wirtualnych just-in-time](security-center-just-in-time.md)
- [Omówienie zabezpieczeń kontenera](container-security.md)
- [Szczegóły cen w wybranej walucie i w zależności od regionu](https://azure.microsoft.com/pricing/details/security-center/)