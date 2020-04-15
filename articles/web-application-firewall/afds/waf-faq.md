---
title: Zapora aplikacji sieci Web platformy Azure — często zadawane pytania
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Zapory aplikacji sieci Web w drzwiach frontowych platformy Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 09/25/2019
ms.author: victorh
ms.openlocfilehash: fefbb038cec0c061f1fd191a77164880372555a0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314320"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Często zadawane pytania dotyczące Zapory aplikacji sieci Web platformy Azure w usłudze Azure Front Door Service

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące funkcji i funkcji zapory aplikacji sieci Web platformy Azure (WAF). 

## <a name="what-is-azure-waf"></a>Co to jest azure WAF?

Usługa Azure WAF to zapora aplikacji sieci web, która pomaga chronić aplikacje sieci web przed typowymi zagrożeniami, takimi jak iniekcja SQL, skrypty między witrynami i inne exploity sieci Web. Można zdefiniować zasady WAF składające się z kombinacji reguł niestandardowych i zarządzanych, aby kontrolować dostęp do aplikacji sieci web.

Zasady usługi Azure WAF można zastosować do aplikacji sieci web hostowanych w usłudze Application Gateway lub Azure Front Doors.

## <a name="what-is-waf-on-azure-front-door"></a>Co to jest usługa WAF w drzwiach frontowych platformy Azure? 

Usługa Azure Front Door to wysoce skalowalna, globalnie rozproszona sieć dostarczania aplikacji i zawartości. Usługa Azure WAF, po zintegrowaniu z drzwiami frontowymi, zatrzymuje ataki typu "odmowa usługi" i ukierunkowane aplikacje na krawędzi sieci platformy Azure, blisko źródeł ataków przed wejściem do sieci wirtualnej, oferuje ochronę bez utraty wydajności.

## <a name="does-azure-waf-support-https"></a>Czy usługa Azure WAF obsługuje protokół HTTPS?

Drzwi wejściowe oferują rozładowywanie TLS. WAF jest natywnie zintegrowany z drzwiami frontowymi i może sprawdzić żądanie po jego odszyfrowaniu.

## <a name="does-azure-waf-support-ipv6"></a>Czy usługa Azure WAF obsługuje IPv6?

Tak. Ograniczenie protokołu IP można skonfigurować dla protokołu IPv4 i IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Jak aktualne są zestawy reguł zarządzanych?

Dokładamy wszelkich starań, aby nadążyć za zmieniającym się krajobrazem zagrożeń. Po zaktualizowaniu nowej reguły jest dodawana do domyślnego zestawu reguł z nowym numerem wersji.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Jaki jest czas propagacji, jeśli zmienię zasady WAF?

Wdrażanie zasad WAF globalnie zwykle trwa około 5 minut i często kończy się wcześniej.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Czy zasady WAF mogą być różne dla różnych regionów?

Po zintegrowaniu z drzwiami frontowymi WAF jest zasobem globalnym. Ta sama konfiguracja ma zastosowanie we wszystkich lokalizacjach drzwi przednich.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Jak ograniczyć dostęp do zaplecza, aby był tylko z drzwi wejściowych?

Można skonfigurować listę kontroli dostępu do adresów IP w zapleczu, aby umożliwić tylko wychodzące zakresy adresów IP drzwiami frontowymi i odmówić bezpośredniego dostępu z Internetu. Tagi usługi są obsługiwane do użycia w sieci wirtualnej. Ponadto można sprawdzić, czy pole nagłówka HTTP hosta X-Forwarded-Host jest prawidłowe dla aplikacji sieci web.

## <a name="which-azure-waf-options-should-i-choose"></a>Które opcje usługi Azure WAF wybrać?

Istnieją dwie opcje podczas stosowania zasad usługi WAF na platformie Azure. WAF z usługą Azure Front Door to globalnie rozproszone rozwiązanie zabezpieczające dla krawędzi. WAF z bramą aplikacji to regionalne, dedykowane rozwiązanie. Zalecamy wybranie rozwiązania na podstawie ogólnych wymagań dotyczących wydajności i zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Równoważenie obciążenia za pomocą pakietu dostarczania aplikacji platformy Azure.](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Czy obsługujesz te same funkcje WAF na wszystkich zintegrowanych platformach?

Obecnie reguły ModSec CRS 2.2.9, CRS 3.0 i CRS 3.1 są obsługiwane tylko w u portalu aplikacji WAF. Reguły domyślnego zestawu reguł z ograniczeniem stawek, filtrowaniem geograficznym i zarządzaniem platformą Azure są obsługiwane tylko w usłudze WAF w drzwiach frontowych platformy Azure.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Czy ochrona Przed Atakami DDoS jest zintegrowana z drzwiami przednimi? 

Usługa Azure Front Door, która jest globalnie rozpowszechniana na krawędziach sieci platformy Azure, może absorbować i geograficznie izolować ataki dużych woluminów. Można utworzyć niestandardowe zasady WAF, aby automatycznie blokować i ograniczać szybkość ataków http(ów), które mają znane podpisy. Ponadto można włączyć standard ochrony przed atakami DDoS w sieci wirtualnej, w której są wdrażane zaplecze. Klienci standardu Ochrony DDoS platformy Azure otrzymują dodatkowe korzyści, w tym ochronę kosztów, gwarancję umowy SLA i dostęp do ekspertów z zespołu szybkiego reagowania DDoS, aby uzyskać natychmiastową pomoc podczas ataku.

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Dlaczego dodatkowe żądania powyżej progu skonfigurowane dla reguły limitu szybkości są przekazywane do mojego serwera wewnętrznej bazy danych?

Reguła limitu szybkości może ograniczyć nienormalnie duży ruch z dowolnego adresu IP klienta. Można skonfigurować próg liczby żądań sieci web dozwolonych z adresu IP klienta w czasie jednej minuty lub pięciu minut. W przypadku kontroli szybkości szczegółowej ograniczanie szybkości można łączyć z dodatkowymi warunkami dopasowania, takimi jak dopasowywanie parametrów HTTP(S). 

Żądania od tego samego klienta często docierają do tego samego serwera drzwi frontowych. W takim przypadku zobaczysz dodatkowe żądania powyżej progu natychmiast zablokować. 

Jednak jest możliwe, że żądania od tego samego klienta mogą dotrzeć do innego serwera drzwiach frontowych, który nie odświeżył jeszcze licznika limitu szybkości. Na przykład klient może otworzyć nowe połączenie dla każdego żądania, a próg jest niski. W takim przypadku pierwsze żądanie do nowego serwera drzwiach frontowych przejdzie sprawdzanie limitu szybkości. Próg limitu szybkości jest zwykle ustawiony na wysoki, aby bronić się przed atakami typu "odmowa usługi" z dowolnego adresu IP klienta. W przypadku bardzo niskiego progu mogą zostać wyświetlene dodatkowe żądania powyżej progu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Zaporze aplikacji sieci Web platformy Azure](../overview.md).
- Dowiedz się więcej o [drzwiach frontowych platformy Azure](../../frontdoor/front-door-overview.md).
