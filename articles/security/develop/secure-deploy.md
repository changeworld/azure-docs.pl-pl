---
title: Wdrażaj bezpieczne aplikacje w systemie Microsoft Azure
description: W tym artykule omówiono najlepsze rozwiązania, należy wziąć pod uwagę podczas fazy wydania i odpowiedzi projektu aplikacji sieci web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: e8249113ee65c28414c79f00c53d11596673434b
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144453"
---
# <a name="deploy-secure-applications-on-azure"></a>Wdrażaj bezpieczne aplikacje na platformie Azure
W tym artykule, firma Microsoft przedstawia działania dotyczące zabezpieczeń i kontroli do rozważenia podczas wdrażania aplikacji w chmurze. Pytania zabezpieczające i koncepcji, które należy wziąć pod uwagę podczas fazy wydania i reagowania firmy Microsoft [cykl projektowania zabezpieczeń (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) zostały uwzględnione. Celem jest pomagającym w zdefiniowaniu działań i usług platformy Azure, które można użyć do wdrożenia aplikacji bardziej bezpieczne.

W tym artykule omówione są następujące fazy SDL:

- Release
- Odpowiedź

## <a name="release"></a>Release
Fokus fazy wydania jest przygotowujemy projekt do wersji publicznej.
Obejmuje to planowanie sposoby efektywnego wykonywania zadań obsługi po wydaniu i luk w zabezpieczeniach, które mogą wystąpić później.

### <a name="check-your-applications-performance-before-you-launch"></a>Sprawdź wydajność aplikacji, przed uruchomieniem

Sprawdź wydajność aplikacji przed jej uruchamiania lub wdrażania aktualizacji w środowisku produkcyjnym. Uruchom oparte na chmurze [testy obciążeniowe](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) za pomocą programu Visual Studio, aby znaleźć problemy z wydajnością w aplikacji, zwiększyć jakość wdrożenia, upewnij się, że aplikacja jest zawsze aktualne i dostępne i że aplikacja może obsługiwać ruch do uruchamiania.

### <a name="install-a-web-application-firewall"></a>Zainstaluj zaporę aplikacji sieci web

Aplikacje internetowe coraz częściej stają się obiektami złośliwych ataków wykorzystujących znane luki w zabezpieczeniach. Typowe te luki w zabezpieczeniach są ataki przez wstrzyknięcie kodu SQL i ataki z użyciem skryptów między witrynami. Zapobieganie atakom w kodzie aplikacji może być trudne. Może ona wymagać rygorystycznego przestrzegania harmonogramu konserwacji, poprawek i monitorowania na poziomie wielu warstw topologii aplikacji. Scentralizowane zapory aplikacji sieci Web ułatwia zarządzanie zabezpieczeniami prostsze. Rozwiązanie zapory aplikacji sieci Web może reagować na zagrożenia bezpieczeństwa poprzez wdrażanie poprawek znanych luk w zabezpieczeniach w centralnej lokalizacji i zabezpieczanie każdej aplikacji sieci web indywidualnych.

[Azure Application Gateway zapory aplikacji sieci Web](https://docs.microsoft.com/azure/application-gateway/waf-overview) zapewnia scentralizowaną ochronę aplikacji sieci web z typowe luki w zabezpieczeniach i luk w zabezpieczeniach. Zapora aplikacji sieci Web na podstawie reguł z [podstawowych zestawów reguł OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) wersji 3.0 lub 2.2.9.

### <a name="create-an-incident-response-plan"></a>Tworzenie planu reagowania na zdarzenia

Przygotowywanie planu reagowania na zdarzenia bezwzględnie sprawdzi się nowe zagrożenia, które mogą pojawić się wraz z upływem czasu. Przygotowywanie planu reagowania na zdarzenia zawiera identyfikowanie rzutem awaryjnego kontakty i utworzenia zabezpieczeń, obsługi planów dla kodu, który jest dziedziczona z innych grup w organizacji oraz licencjonowane kodu innych firm.

### <a name="conduct-a-final-security-review"></a>Przeprowadzanie przeglądu ostatecznego zabezpieczeń

Celowo przeglądanie wszystkie działania dotyczące zabezpieczeń, które były wykonywane temu gotowości dla wersji oprogramowania lub aplikacji. Przegląd zabezpieczeń końcowe (FRS) zawiera zazwyczaj badanie modele zagrożeń, dane wyjściowe narzędzia i wydajności względem bramki jakości i paski usterki, które zostały zdefiniowane w fazie wymagania.

### <a name="certify-release-and-archive"></a>Certyfikowanie wydania i archiwum

Certyfikowanie oprogramowania przed wydania pomaga upewnić się, że spełnione są wymagania dotyczące zabezpieczeń i prywatności. Archiwizowanie wszystkich potrzebnych danych jest niezbędne do wykonywania zadań obsługi po wydaniu. Archiwizowanie również pomaga niższych długoterminowe koszty związane z stałą inżynierii oprogramowania.

## <a name="response"></a>Odpowiedź
Centra fazę po wydaniu odpowiedzi na zespół deweloperów może i dostępna reagować odpowiednio na wszystkie raporty pojawiających się zagrożeń oprogramowania i luk w zabezpieczeniach.

### <a name="execute-the-incident-response-plan"></a>Wykonanie planu reagowania na zdarzenia

Możliwość wdrożenia planu reagowania na zdarzenia ustanowionego w fazie wersji jest niezbędne do zapewnienia klientom z bezpieczeństwem albo prywatnością luk w zabezpieczeniach oprogramowania wynikające.

### <a name="monitor-application-performance"></a>Monitorowanie wydajności aplikacji

Ciągłe monitorowanie aplikacji po jej wdrożeniu potencjalnie pomaga wykrywać problemy z wydajnością, a także luki w zabezpieczeniach.
Usługi platformy Azure, które pomagają z monitorowaniem aplikacji są:

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

[Usługa Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) to rozszerzalna Usługa zarządzania wydajnością aplikacji (APM) dla deweloperów sieci web na wielu platformach. Użyj tej usługi do monitorowania aplikacji internetowej na żywo. Usługa Application Insights automatycznie wykrywa anomalie wydajność. Zawiera zaawansowane funkcje analityczne narzędzia ułatwiające diagnozowanie problemów i zrozumieć, jak użytkownicy w rzeczywistości korzystają z aplikacją. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań.

#### <a name="azure-security-center"></a>Azure Security Center

[Usługa Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) pomaga zapobiegać zagrożeniom, wykrywać i odpowiadanie na nie dzięki lepszemu wglądowi w (i kontrolę nad) zabezpieczeń swoich zasobów platformy Azure, w tym aplikacje sieci web. Usługa Azure Security Center pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone. Działa z różnych rozwiązań zabezpieczeń.

Security Center w warstwie bezpłatna zapewnia ograniczone bezpieczeństwo jedynie zasobów platformy Azure. [Security Center w warstwie standardowa](https://docs.microsoft.com/azure/security-center/security-center-onboarding) rozszerza te możliwości do zasobów lokalnych i innych chmur.
Usługa Security Center w warstwie standardowa pomaga:

  - Znajdowanie i naprawianie luk w zabezpieczeniach.
  - Stosowanie kontroli dostępu i aplikacji w celu blokowania złośliwych działań.
  - Wykrywanie zagrożeń za pomocą analizy i analizy.
  - Szybko reagować podczas ataku.

## <a name="next-steps"></a>Kolejne kroki
W następujących artykułach firma Microsoft zaleca środki kontroli bezpieczeństwa i działań, które mogą pomóc Ci projektowania i programowania bezpiecznych aplikacji.

- [Projektowanie bezpiecznych aplikacji](secure-design.md)
- [Tworzenie bezpiecznych aplikacji](secure-develop.md)
