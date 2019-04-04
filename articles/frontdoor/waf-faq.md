---
title: Zapora aplikacji sieci web platformy Azure — często zadawane pytania
description: Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Service drzwi
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: kumud;tyao
ms.openlocfilehash: ef021bc9a4f3cb8b49ab3b455e00dfa8dc27dc76
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58910003"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Często zadawane pytania dotyczące zapory aplikacji sieci web platformy Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące funkcji umożliwiającymi zainstalowanie zapory aplikacji sieci web platformy Azure i funkcji. 

## <a name="what-is-azure-waf"></a>Co to jest Azure zapory aplikacji sieci Web?

Usługa Azure zapory aplikacji sieci Web jest Zapora aplikacji sieci web, która pomaga chronić aplikacje sieci web z typowych zagrożeń, takich jak SQL iniekcji skryptów między witrynami i inne luki w zabezpieczeniach sieci web. Można zdefiniować zasady zapory aplikacji internetowych składających się z kombinacji zarządzanych i niestandardowych reguł do kontrolowania dostępu do aplikacji sieci web.

Zasadę zapory aplikacji internetowych platformy Azure można zastosować do aplikacji sieci web usługi Application Gateway lub drzwi wejściowe platformy Azure w serwisie.

## <a name="what-is-waf-for-azure-front-door-service"></a>Co to jest zapory aplikacji sieci Web dla usługi Azure Service drzwiami frontowymi? 

Azure drzwiami frontowymi jest wysoce skalowalną, globalnie rozproszonych aplikacji, a usługa content delivery network. Azure zapory aplikacji sieci Web, po zintegrowaniu z usługą drzwiami frontowymi zatrzymuje typu "odmowa usługi", a celem ataków aplikacji na granicy sieci platformy Azure, blisko źródła ataku, zanim wejdzie do sieci wirtualnej, zapewnia ochronę bez obniżania oczekiwanego poziomu wydajności.

## <a name="how-will-i-be-charged-for-azure-waf-for-front-door"></a>Jak będzie rozliczany Zapora aplikacji sieci Web platformy Azure dla drzwiami frontowymi?
W publicznej wersji zapoznawczej zapory aplikacji sieci Web użycia na wejściu są bezpłatne. Zwróć uwagę, że opłata za wejściu dodatkowe. Zobacz cennik usługi drzwiami frontowymi [tutaj](https://azure.microsoft.com/pricing/details/frontdoor/).

## <a name="does-azure-waf-support-https"></a>Zapora aplikacji sieci Web w usłudze Azure obsługuje protokół HTTPS?

Usługa drzwiami frontowymi oferuje odciążanie protokołu SSL. Zapora aplikacji sieci Web natywnej integracji z drzwiami frontowymi i może sprawdzać żądania po są odszyfrowywane.

## <a name="does-azure-waf-support-ipv6"></a>Czy zapory aplikacji internetowych platformy Azure obsługuje protokół IPv6?

Tak. Można skonfigurować ograniczenia adresów IP dla protokołów IPv4 i IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Jak aktualne jest zestawów reguł zarządzanego?

Firma Microsoft starań na bieżąco zmieniającymi się zagrożeniach. Po zaktualizowaniu Nowa reguła jest dodawany do domyślnej zestawem reguł za pomocą nowego numeru wersji.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Co to jest czas propagacji, jeśli mimo zmiany do mojego zasad zapory aplikacji sieci Web?

Wdrażanie zasad zapory aplikacji sieci Web globalnie zwykle trwa około 5 minut, a często kończy wcześniej.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Zasady zapory aplikacji sieci Web mogą być różne dla różnych regionach?

Po zintegrowaniu z usługą drzwiami frontowymi, zapory aplikacji sieci Web jest globalny zasób. Tę samą konfigurację, stosuje się we wszystkich lokalizacjach drzwi wejściowe.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Jak ograniczyć dostęp do mojej zaplecza z drzwiami frontowymi tylko do?

Użytkownik może konfigurować listy kontroli dostępu IP w danym zapleczu umożliwiają tylko drzwiami frontowymi wychodzącego zakresów adresów IP, i odmawiać go dowolnym bezpośredni dostęp z Internetu. Tagi usługi są obsługiwane w przypadku użycia w sieci wirtualnej. Ponadto możesz sprawdzić, czy pole nagłówka HTTP X-Forwarded-Host jest odpowiedni dla aplikacji sieci web.




## <a name="which-azure-waf-options-should-i-choose"></a>Które opcje zapory aplikacji internetowych platformy Azure należy wybrać?

Dostępne są dwie opcje podczas stosowania zasad zapory aplikacji sieci Web na platformie Azure. Zapora aplikacji sieci Web za pomocą usługi Azure drzwiami frontowymi jest globalnie dystrybuowane rozwiązanie w zakresie zabezpieczeń brzegowych. Zapora aplikacji sieci Web z usługą Application Gateway to rozwiązanie regionalnych, dedykowanej. Zalecane jest wybranie rozwiązania, w zależności od wymagań ogólnej wydajności i bezpieczeństwa. Aby uzyskać więcej informacji, zobacz [równoważenia obciążenia przy użyciu pakietem dostarczania aplikacji platformy Azure](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Te same funkcje zapory aplikacji sieci Web są obsługiwane na wszystkich platformach zintegrowane?

Obecnie ModSec CRS 2.2.9 i reguł CRS 3.0, ale są obsługiwane tylko za pomocą zapory aplikacji sieci Web w usłudze Application Gateway. Ograniczanie szybkości, filtrowania geograficznego i platformy Azure zarządzanego domyślnego zestawu reguł reguły są obsługiwane tylko z zapory aplikacji sieci Web na platformie Azure drzwi wejściowe.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Czy usługa DDoS protection jest zintegrowany z drzwiami frontowymi? 

Globalnie rozproszone na krawędzi sieci platformy Azure, Azure drzwiami frontowymi można ochrony przed rozproszonymi i geograficznie izolowania dużą ataków. Możesz utworzyć niestandardowe zasady zapory aplikacji sieci Web do bloku automatycznie i współczynnik liczby ataków HTTP (s), o znanych sygnatur. Dodatkowo, można włączyć przed atakami DDoS Protection standardowego w sieci wirtualnej wdrożonym usługi zaplecza. Klienci platformy Azure przed atakami DDoS Protection standardowa otrzymać dodatkowe korzyści, w tym ochrona kosztów, żadnej gwarancji umowy SLA i dostęp do ekspertów od przed atakami DDoS Rapid Response Team natychmiastowej pomocy podczas ataku. 

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [ zapory aplikacji sieci web platformy Azure](waf-overview.md).
- Dowiedz się więcej o [Azure drzwiami frontowymi](front-door-overview.md).
