---
title: Korzystanie z zaleceń usługi Azure Security Center w celu zwiększenia bezpieczeństwa | Dokumenty firmy Microsoft
description: " Dowiedz się, jak korzystać z zasad zabezpieczeń i zaleceń w usłudze Azure Security Center, aby ograniczyć atak zabezpieczeń. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: memildin
ms.openlocfilehash: a1034eb47010da2b0e795ee8c79646f06151cac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603282"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Używanie zaleceń usługi Azure Security Center w celu ulepszania zabezpieczeń
Można zmniejszyć prawdopodobieństwo wystąpienia istotnego zdarzenia zabezpieczeń, konfigurując zasady zabezpieczeń, a następnie implementując zalecenia dostarczone przez usługę Azure Security Center. W tym artykule pokazano, jak używać zasad zabezpieczeń i zaleceń w Centrum zabezpieczeń, aby pomóc w ograniczeniu ataku zabezpieczeń. 

Usługa Security Center automatycznie uruchamia ciągłe skanowanie w celu przeanalizowania stanu zabezpieczeń zasobów platformy Azure. Gdy usługa Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia, które poprowadzą Użytkownika przez proces konfigurowania wymaganych zabezpieczeń zabezpieczeń. Usługa Security Center aktualizuje swoje zalecenia w ciągu 24 godzin, z następującymi wyjątkami:

- Zalecenia dotyczące konfiguracji zabezpieczeń systemu operacyjnego są aktualizowane w ciągu 48 godzin
- Zalecenia dotyczące problemów związanych z ochroną punktu końcowego są aktualizowane w ciągu 8 godzin

## <a name="scenario"></a>Scenariusz
W tym scenariuszu pokazano, jak używać usługi Security Center, aby zmniejszyć ryzyko zdarzenia zabezpieczeń, monitorując zalecenia usługi Security Center i podejmując działania. W scenariuszu użyto fikcyjnej firmy Contoso i ról przedstawionych w [przewodniku planowania i operacji](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)usługi Security Center . W tym scenariuszu koncentrujemy się na rolach następujących personas:

![Role scenariuszy](./media/security-center-using-recommendations/scenario-roles.png)

Firma Contoso niedawno zmigrowała niektóre z ich zasobów lokalnych na platformę Azure. Contoso chce chronić swoje zasoby i zmniejszyć podatność ich zasobów w chmurze.

## <a name="use-azure-security-center"></a>Korzystanie z usługi Azure Security Center
David, z zabezpieczeń IT firmy Contoso, wybrał już wbudowane centrum zabezpieczeń w ramach subskrypcji usługi Contoso w usłudze Azure Security Center, aby zapobiegać lukom w zabezpieczeniach i wykrywać ich. 

Usługa Security Center automatycznie analizuje stan zabezpieczeń zasobów platformy Azure firmy Contoso i stosuje domyślne zasady zabezpieczeń. Gdy Usługa Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia na podstawie **formantów** określonych w zasadach zabezpieczeń. 

Firma David uruchamia warstwę standardową usługi Azure Security we wszystkich subskrypcjach, aby uzyskać pełny zestaw dostępnych zaleceń i funkcji zabezpieczeń. Jeff dołącza również wszystkie istniejące serwery lokalne, które nie zostały jeszcze zmigrowane do chmury, aby mogły korzystać z obsługi hybrydowej usługi Security Center na serwerach [Windows](quick-onboard-windows-computer.md) i [Linux.](quick-onboard-linux-computer.md)

Jeff jest właścicielem obciążenia w chmurze. Jeff jest odpowiedzialny za stosowanie kontroli zabezpieczeń zgodnie z zasadami zabezpieczeń firmy Contoso. 

Jeff wykonuje następujące zadania:

- Monitorowanie zaleceń dotyczących zabezpieczeń dostarczonych przez centrum zabezpieczeń
- Oceń zalecenia dotyczące zabezpieczeń i zdecyduj, czy powinny one stosować lub odrzucać zalecenia.
- Stosowanie zaleceń dotyczących zabezpieczeń

### <a name="remediate-threats-using-recommendations"></a>Korygowanie zagrożeń za pomocą zaleceń
W ramach codziennych działań związanych z monitorowaniem Jeff loguje się do platformy Azure i otwiera centrum zabezpieczeń. 

1. Jeff wybiera subskrypcje obciążenia.

2. Jeff sprawdza **Secure Score,** aby uzyskać ogólny obraz tego, jak bezpieczne są subskrypcje i widzi, że wynik wynosi 548.

3. Jeff musi zdecydować, które zalecenia do obsługi w pierwszej kolejności. Więc Jeff kliknięć Secure Score i zaczyna obsługiwać zalecenia w oparciu o to, jak bardzo poprawia jego [wpływ Secure Score](security-center-secure-score.md).

4. Ponieważ Jeff ma wiele podłączonych maszyn wirtualnych i serwerów, Jeff postanawia skupić się na **obliczeniach i aplikacjach.**

5. Gdy Jeff kliknie **compute i aplikacje,** widzą listę zaleceń i obsługuje je zgodnie z secure score wpływ.

6. Jeff ma wiele maszyn wirtualnych z Internetem, a ponieważ ich porty są narażone, obawiają się, że osoba atakująca może przejąć kontrolę nad serwerami. Więc Jeff decyduje się na korzystanie [**z dostępu just-in-time VM**](security-center-just-in-time.md).

Jeff nadal przechodzi przez zalecenia o wysokim priorytecie i średnim priorytecie i podejmuje decyzje dotyczące wdrażania. Dla każdego zalecenia Jeff analizuje szczegółowe informacje dostarczone przez centrum zabezpieczeń, aby zrozumieć, które zasoby mają wpływ, jaki jest wpływ bezpiecznego wyniku, co oznacza każde zalecenie i kroki korygowania, aby zmniejszyć każdy problem.

## <a name="conclusion"></a>Podsumowanie
Zalecenia dotyczące monitorowania w u centrum zabezpieczeń pomagają wyeliminować luki w zabezpieczeniach przed atakiem. Po korygowanie zaleceń poprawia się wynik secure score i postawa zabezpieczeń obciążeń. Usługa Security Center automatycznie odnajduje nowe zasoby, które wdrażasz, ocenia je pod kątem zasad zabezpieczeń i udostępnia nowe zalecenia dotyczące ich zabezpieczania.


## <a name="next-steps"></a>Następne kroki
Upewnij się, że masz proces monitorowania w miejscu, w którym regularnie sprawdzać zalecenia w u centrum zabezpieczeń, dzięki czemu można upewnić się, aby zachować swoje zasoby bezpieczne w czasie.

W tym scenariuszu pokazano, jak używać zasad zabezpieczeń i zaleceń w u centrum zabezpieczeń, aby pomóc w łagodzeniu ataku zabezpieczeń.

Dowiedz się, jak reagować na zagrożenia, [chcę zarządzać alertami zabezpieczeń i odpowiadać na nie.](security-center-managing-and-responding-alerts.md)
