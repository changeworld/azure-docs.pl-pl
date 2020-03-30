---
title: Wdrażanie bezpiecznych aplikacji na platformie Microsoft Azure
description: W tym artykule omówiono najlepsze rozwiązania, które należy wziąć pod uwagę podczas fazy wydawania i odpowiedzi projektu aplikacji sieci web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: dfe4f09d00a5629249a3041946190f56e83c3480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934885"
---
# <a name="deploy-secure-applications-on-azure"></a>Wdrażanie bezpiecznych aplikacji na platformie Azure
W tym artykule przedstawiamy działania i kontrolki zabezpieczeń, które należy wziąć pod uwagę podczas wdrażania aplikacji dla chmury. Pytania zabezpieczające i pojęcia, które należy wziąć pod uwagę podczas fazy wydawania i reagowania cyklu życia programu Microsoft [Security Development (SDL).](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) Celem jest pomoc w definiowaniu działań i usług platformy Azure, których można użyć do wdrożenia bezpieczniejszej aplikacji.

W tym artykule uwzględnione są następujące fazy SDL:

- Release
- Odpowiedź

## <a name="release"></a>Release
Głównym celem fazy wydania jest przygotowanie projektu do publicznej wersji.
Obejmuje to planowanie sposobów efektywnego wykonywania zadań obsługi po wydaniu i eliminowania luk w zabezpieczeniach, które mogą wystąpić później.

### <a name="check-your-applications-performance-before-you-launch"></a>Sprawdź wydajność aplikacji przed uruchomieniem

Sprawdź wydajność aplikacji przed uruchomieniem go lub wdrożenie aktualizacji w produkcji. Uruchamianie [testów obciążenia opartych](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) na chmurze przy użyciu programu Visual Studio, aby znaleźć problemy z wydajnością w aplikacji, poprawić jakość wdrożenia, upewnij się, że aplikacja jest zawsze dostępna lub dostępna, a aplikacja może obsługiwać ruch podczas uruchamiania.

### <a name="install-a-web-application-firewall"></a>Instalowanie zapory aplikacji sieci Web

Aplikacje internetowe coraz częściej stają się obiektami złośliwych ataków wykorzystujących znane luki w zabezpieczeniach. Typowe wśród tych exploitów są ataki iniekcji SQL i ataki skryptów między witrynami. Zapobieganie tym atakom w kodzie aplikacji może być trudne. Może to wymagać rygorystycznej konserwacji, poprawek i monitorowania w wielu warstwach topologii aplikacji. Scentralizowana usługa WAF ułatwia zarządzanie zabezpieczeniami. Rozwiązanie WAF może również reagować na zagrożenie bezpieczeństwa, łatając znaną lukę w zabezpieczeniach w centralnej lokalizacji w porównaniu do zabezpieczania poszczególnych aplikacji sieci web.

[Usługa Azure Application Gateway WAF](../../application-gateway/waf-overview.md) zapewnia scentralizowaną ochronę aplikacji sieci web przed typowymi exploitami i lukami w zabezpieczeniach. WAF opiera się na regułach z [podstawowych reguł OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 lub 2.2.9.

### <a name="create-an-incident-response-plan"></a>Tworzenie planu reagowania na zdarzenia

Przygotowanie planu reagowania na incydenty ma kluczowe znaczenie dla rozwiązania nowych zagrożeń, które mogą pojawić się w czasie. Przygotowanie planu reagowania na incydenty obejmuje identyfikowanie odpowiednich kontaktów awaryjnych zabezpieczeń i ustanawianie planów obsługi zabezpieczeń dla kodu odziedziczonego po innych grupach w organizacji i dla licencjonowanego kodu innej firmy.

### <a name="conduct-a-final-security-review"></a>Przeprowadzanie ostatecznego przeglądu zabezpieczeń

Celowe przeglądanie wszystkich wykonanych działań związanych z zabezpieczeniami pomaga zapewnić gotowość do wydania lub aplikacji. Ostateczny przegląd zabezpieczeń (FSR) zwykle obejmuje badanie modeli zagrożeń, wyjść narzędzi i wydajności względem bram jakości i pasków błędów, które zostały zdefiniowane w fazie wymagań.

### <a name="certify-release-and-archive"></a>Certyfikuj wydanie i archiwum

Certyfikowanie oprogramowania przed wydaniem pomaga zapewnić, że wymagania dotyczące zabezpieczeń i prywatności są spełnione. Archiwizacja wszystkich istotnych danych jest niezbędna do wykonywania zadań serwisowych po wydaniu. Archiwizacja pomaga również obniżyć długoterminowe koszty związane z trwałą inżynierią oprogramowania.

## <a name="response"></a>Odpowiedź
Faza odpowiedzi po wydaniu koncentruje się na tym, że zespół programistów jest w stanie i jest dostępny, aby odpowiednio reagować na wszelkie zgłoszenia pojawiających się zagrożeń i luk w zabezpieczeniach oprogramowania.

### <a name="execute-the-incident-response-plan"></a>Wykonanie planu reagowania na incydenty

Możliwość wdrożenia planu reagowania na incydenty wprowadzonego w fazie wydania ma zasadnicze znaczenie dla ochrony klientów przed pojawianiem się luk w zabezpieczeniach oprogramowania lub prywatności.

### <a name="monitor-application-performance"></a>Monitorowanie wydajności aplikacji

Ciągłe monitorowanie aplikacji po jej wdrożeniu potencjalnie pomaga wykryć problemy z wydajnością, a także luki w zabezpieczeniach.
Usługi platformy Azure, które pomagają w monitorowaniu aplikacji są:

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

[Usługa Application Insights](../../azure-monitor/app/app-insights-overview.md) to usługa zarządzania wydajnością aplikacji rozszerzalna (APM) dla deweloperów sieci web na wielu platformach. Użyj tej usługi do monitorowania aplikacji internetowej na żywo. Usługa Application Insights automatycznie wykrywa anomalie wydajności. Zawiera zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów i zrozumienie, co użytkownicy faktycznie robią z twoją aplikacją. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań.

#### <a name="azure-security-center"></a>Azure Security Center

[Usługa Azure Security Center](../../security-center/security-center-intro.md) pomaga zapobiegać zagrożeniom, wykrywać i reagować na nie dzięki zwiększonemu wglądowi w zabezpieczenia zasobów platformy Azure, w tym aplikacji sieci Web(i) i kontroli nad nim. Usługa Azure Security Center pomaga wykrywać zagrożenia, które w przeciwnym razie mogą przejść niezauważone. Współpracuje z różnymi rozwiązaniami bezpieczeństwa.

Warstwa bezpłatna usługi Security Center oferuje ograniczone zabezpieczenia tylko dla zasobów platformy Azure. Warstwa [Standard usługi Security Center](../../security-center/security-center-onboarding.md) rozszerza te możliwości na zasoby lokalne i inne chmury.
Usługa Security Center Standard pomaga:

  - Znajdowanie i naprawianie luk w zabezpieczeniach.
  - Zastosuj formanty dostępu i aplikacji, aby zablokować złośliwą aktywność.
  - Wykrywaj zagrożenia za pomocą analizy i analizy.
  - Szybko reaguj, gdy jest atakowany.

## <a name="next-steps"></a>Następne kroki
W poniższych artykułach zaleca się kontrole zabezpieczeń i działania, które mogą pomóc w projektowaniu i opracowywaniu bezpiecznych aplikacji.

- [Projektowanie bezpiecznych aplikacji](secure-design.md)
- [Tworzenie bezpiecznych aplikacji](secure-develop.md)
