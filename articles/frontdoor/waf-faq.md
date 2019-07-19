---
title: Zapora aplikacji sieci Web platformy Azure — często zadawane pytania
description: Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure front-drzwi
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: c993e465bc439ff52cba3241dbff64b7655d1f12
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849175"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Często zadawane pytania dotyczące zapory aplikacji sieci Web platformy Azure

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące funkcji i funkcji zapory aplikacji sieci Web (WAF) platformy Azure. 

## <a name="what-is-azure-waf"></a>Co to jest Azure WAF?

Azure WAF to Zapora aplikacji sieci Web, która pomaga chronić aplikacje sieci Web przed typowymi zagrożeniami, takimi jak iniekcja SQL, skrypty między lokacjami i inne luki w zabezpieczeniach sieci Web. Można zdefiniować zasady WAF, które składają się z kombinacji reguł niestandardowych i zarządzanych w celu kontrolowania dostępu do aplikacji sieci Web.

Zasady usługi Azure WAF można stosować do aplikacji sieci Web hostowanych na Application Gateway lub w usługach Azure front-drzwi.

## <a name="what-is-waf-for-azure-front-door-service"></a>Co to jest WAF dla usługi Azure front-drzwi? 

Drzwi frontonu platformy Azure to wysoce skalowalna, globalnie dystrybuowana aplikacja i usługa Content Delivery Network. Usługa Azure WAF, która jest zintegrowana z przednim Drzwiem, kończy ataki typu "odmowa usługi" i "ukierunkowane na aplikacje" na granicy sieci platformy Azure, a przed wprowadzeniem sieci wirtualnej zapewnia ochronę bez utraty wydajności.

## <a name="does-azure-waf-support-https"></a>Czy usługa Azure WAF obsługuje protokół HTTPS?

Usługa front-drzwi oferuje odciążanie protokołu SSL. WAF jest natywnie zintegrowana z przednimi drzwiami i może sprawdzić żądanie po jego odszyfrowaniu.

## <a name="does-azure-waf-support-ipv6"></a>Czy usługa Azure WAF obsługuje protokół IPv6?

Tak. Można skonfigurować ograniczenie adresów IP dla protokołów IPv4 i IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Jak aktualne są zarządzane zestawy reguł?

Naszym najlepszym rozwiązaniem jest zmiana krajobrazu zagrożenia. Po zaktualizowaniu nowej reguły zostanie ona dodana do domyślnego zestawu reguł przy użyciu nowego numeru wersji.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Jaki jest czas propagacji w przypadku wprowadzenia zmiany zasad WAFymi?

Wdrożenie zasad WAF globalnie zwykle trwa około 5 minut i często kończy się wkrótce.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Czy zasady WAF mogą być różne dla różnych regionów?

Po zintegrowaniu z usługą front-drzwi WAF jest zasobem globalnym. Ta sama konfiguracja ma zastosowanie do wszystkich miejsc z przodu.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Jak mogę ograniczyć dostęp do mojego zaplecza tylko z przodu?

Możesz skonfigurować listę Access Control IP w zapleczu, aby zezwolić tylko na wychodzące zakresy adresów IP z przodu i odmówić dostępu z Internetu. Tagi usług są obsługiwane, aby można było używać ich w sieci wirtualnej. Ponadto można sprawdzić, czy pole nagłówka HTTP X-Forward-host jest prawidłowe dla aplikacji sieci Web.




## <a name="which-azure-waf-options-should-i-choose"></a>Które opcje usługi Azure WAF należy wybrać?

Istnieją dwie opcje stosowania zasad WAFymi na platformie Azure. WAF z platformą Azure front-drzwi to globalne, rozproszone rozwiązanie do zabezpieczeń. WAF with Application Gateway to regionalne, dedykowane rozwiązanie. Zalecamy wybranie rozwiązania na podstawie ogólnych wymagań dotyczących wydajności i zabezpieczeń. Aby uzyskać więcej informacji, zobacz [równoważenie obciążenia przy użyciu pakietu dostarczania aplikacji platformy Azure](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Czy te same funkcje WAF są obsługiwane we wszystkich zintegrowanych platformach?

Obecnie reguły ModSec KSR 2.2.9 i KSR 3,0 są obsługiwane tylko przez program WAF w Application Gateway. Reguły ograniczania, filtrowania geograficznego i zarządzanego zestawu reguł platformy Azure są obsługiwane tylko w przypadku WAF na platformie Azure.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Czy ochrona DDoS jest zintegrowana z przednimi drzwiami? 

Ogólnie dystrybuowane na krawędziach sieci platformy Azure, drzwi frontonu platformy Azure mogą wchłonąć i geograficznie izolować duże ataki. Można utworzyć niestandardowe zasady WAF, aby automatycznie blokować i ograniczać liczbę ataków http (s), które mają znane sygnatury. Więcej informacji można DDoS Protection włączyć w sieci wirtualnej, w której zostaną wdrożone zaplecza. Klienci w Azure DDoS Protection standard otrzymują dodatkowe korzyści, w tym ochronę kosztów, gwarancję SLA i dostęp do ekspertów z zespołu szybkiego reagowania na DDoS w celu uzyskania natychmiastowej pomocy w trakcie ataku. 

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [zaporze aplikacji sieci Web platformy Azure](waf-overview.md).
- Dowiedz się więcej na temat [zewnętrznych drzwi platformy Azure](front-door-overview.md).
