---
title: Usługa Azure Security Center zaleceń, aby zwiększyć poziom bezpieczeństwa | Dokumentacja firmy Microsoft
description: " Dowiedz się, jak używać zasad zabezpieczeń i zalecenia w usłudze Azure Security Center aby ułatwić uniknięcie atak na zabezpieczenia. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/24/2019
ms.author: monhaber
ms.openlocfilehash: 8c8108697e39153aef8727942c166a741cb8398c
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402766"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Usługa Azure Security Center zaleceń, aby zwiększyć poziom bezpieczeństwa
Konfigurowanie zasad zabezpieczeń, a następnie wdrażanie zaleceń dotyczących, dostarczone przez usługę Azure Security Center może zmniejszyć prawdopodobieństwo zdarzeń zabezpieczeń. W tym artykule przedstawiono sposób używania zasad zabezpieczeń i zalecenia w usłudze Security Center, aby ułatwić uniknięcie atak na zabezpieczenia. 

Usługa Security Center automatycznie uruchamia ciągłe skanowania, aby analizuje stan zabezpieczeń zasobów platformy Azure. Gdy usługa Security Center zidentyfikuje potencjalnych luk w zabezpieczeniach, tworzy zaleceń, które przeprowadzą Cię przez proces konfigurowania kontroli zabezpieczeń potrzebne. Usługa Security Center aktualizuje zalecenia co 24 godziny.

## <a name="scenario"></a>Scenariusz
W tym scenariuszu pokazano, jak zmniejszają prawdopodobieństwo wystąpienia zdarzenia zabezpieczeń, monitorując zaleceń usługi Security Center i podjęcia działań za pomocą usługi Security Center. Scenariuszu fikcyjnej firmy, Contoso i ról znajdujące się w Centrum zabezpieczeń [przewodnik planowania i obsługi](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). W tym scenariuszu firma Microsoft jest koncentrujących się na rolach następujących:

![Scenariusz ról](./media/security-center-using-recommendations/scenario-roles.png)

Contoso ostatnio migracji niektórych swoich zasobów lokalnych na platformę Azure. Firma Contoso chce chronić swoje zasoby i zmniejszenia stopnia narażenia ich zasobów w chmurze.

## <a name="use-azure-security-center"></a>Użyj usługi Azure Security Center
David firmy Contoso bezpieczeństwa informatycznego, dostępne już wybrał dołączyć Centrum zabezpieczeń dla subskrypcji firmy Contoso w usłudze Azure Security Center, aby zapobiec i wykryć luki w zabezpieczeniach. 

Usługa Security Center analizuje stan zabezpieczeń zasobów platformy Azure z firmy Contoso i automatycznie stosuje domyślne zasady zabezpieczeń. Gdy usługa Security Center zidentyfikuje potencjalnych luk w zabezpieczeniach, tworzy **zalecenia** oparte na kontrolki, w ramach zasad zabezpieczeń. 

David uruchamia zabezpieczeń platformy Azure w warstwie standardowa dla wszystkich jego subskrypcji, aby uzyskać pełny zestaw zaleceń i funkcji zabezpieczeń dostępnych. Jeff dołącza wszystkie swoje istniejące serwery lokalne, które nie zostały jeszcze zostały poddane migracji do chmury, aby on korzystać z zalet hybrydowego Centrum zabezpieczeń obsługują również na jego [Windows](quick-onboard-windows-computer.md) i [Linux](quick-onboard-linux-computer.md) serwery.

Jeff jest właścicielem obciążenia chmury. Jeff jest odpowiedzialny za stosowanie kontroli zabezpieczeń, zgodnie z zasadami zabezpieczeń firmy Contoso. 

Jan wykonuje następujące zadania:

- Zalecenia dotyczące zabezpieczeń monitorowania dostarczane przez usługę Security Center
- Oceń zalecenia dotyczące zabezpieczeń i zdecydować, jeśli on powinien Zastosuj lub Odrzuć
- Stosowanie zalecenia dotyczące zabezpieczeń

### <a name="remediate-threats-using-recommendations"></a>Korygowanie zagrożeń przy użyciu zalecenia
W ramach jego codziennych działań monitorowania Jeff loguje się do platformy Azure i otwiera usługę Security Center. 

1. Jan wybiera subskrypcje jego obciążenie.

2. Jan sprawdza, czy jego **secure wynik** uzyskać ogólny obraz, jak bezpieczne subskrypcje są i zauważa to 548 jego wynik.

3. Jeff ma podjęcie decyzji, które zalecenia dotyczące obsługi najpierw. Jeff kliknie wynik bezpieczny i rozpoczyna się do obsługi zalecenia w zależności od ilości zwiększa jego [secure ocenę wpływu](security-center-secure-score.md).

4. Jeff ma wiele połączonych maszyn wirtualnych i serwerów, Jeff chce skupić się na **obliczeniowe i aplikacje**.

5. Po kliknięciu Jeff **obliczeniowe i aplikacje**, użytkownik widzi listę zaleceń i postępują zgodnie z bezpiecznego ocena wpływu.

6. Jeff ma wiele maszyn wirtualnych z Internetem, a ponieważ ich portów są udostępniane, jest Boisz się, że osoba atakująca może uzyskać kontrolę nad serwerów. Aby Jan zdecydował się użyć (**dostęp do maszyny Wirtualnej just-in-time**) [zabezpieczeń center-just w time.md].

Jeff poruszania się o wysokim priorytecie i zalecenia średni priorytet w dalszym ciągu i podejmuje decyzje w implementacji. Dla każdego zalecenia Jeff przegląda szczegółowe informacje podane przez usługę Security Center, aby poznać zasoby, których dotyczy problem, wpływ na wynik bezpiecznego są, jakie każdy oznacza, że zalecenia oraz czynności zaradcze dotyczące rozwiązać każdy problem.

## <a name="conclusion"></a>Podsumowanie
Funkcja monitorowania zalecenia w usłudze Security Center ułatwia eliminowanie luk w zabezpieczeniach, zanim wystąpi atak. Możesz korygować zalecenia, poprawić bezpiecznego ocenę i poziom bezpieczeństwa Twoich obciążeń. Usługa Security Center automatycznie odnajduje nowe zasoby, wdrażanie, ocenia je względem zasad zabezpieczeń i zawiera nowe zalecenia dotyczące ich ochrony.


## <a name="next-steps"></a>Kolejne kroki
Upewnij się, że masz proces monitorowania w miejscu, w którym regularnie sprawdzać zalecenia w usłudze Security Center, aby można upewnić się zabezpieczyć swoje zasoby wraz z upływem czasu.

W tym scenariuszu pokazano sposób używania zasad zabezpieczeń i zalecenia w usłudze Security Center, aby ułatwić uniknięcie atak na zabezpieczenia. Zobacz [scenariuszach reagowania na zdarzenia](security-center-incident-response.md) Aby dowiedzieć się, jak planem przed wystąpieniem ataku reagowania na zdarzenia.

Dowiedz się, jak reagować na zagrożenia przy użyciu [reagowania na zdarzenia](security-center-incident-response.md).
