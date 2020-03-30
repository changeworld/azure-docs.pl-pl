---
title: Najważniejsze wskazówki dotyczące bezpiecznego programowania na platformie Microsoft Azure
description: Najważniejsze wskazówki, które pomogą Ci opracować bezpieczniejszy kod i wdrożyć bezpieczniejszą aplikację w chmurze.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c4314a0dcbbcb907ef4d6de0a2788cf04dfe1641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934863"
---
# <a name="secure-development-best-practices-on-azure"></a>Najważniejsze wskazówki dotyczące bezpiecznego programowania na platformie Azure
Ta seria artykułów przedstawia działania zabezpieczeń i kontrole, które należy wziąć pod uwagę podczas tworzenia aplikacji dla chmury. Zostaną omówione fazy cyklu życia programu Microsoft Security Development (SDL) oraz pytania i pojęcia dotyczące zabezpieczeń, które należy wziąć pod uwagę podczas każdej fazy cyklu życia. Celem jest pomoc w definiowaniu działań i usług platformy Azure, których można używać w każdej fazie cyklu życia do projektowania, opracowywania i wdrażania bezpieczniejszej aplikacji.

Zalecenia zawarte w artykułach pochodzą z naszego środowiska z zabezpieczeniami platformy Azure i z doświadczeń naszych klientów. Możesz użyć tych artykułów jako odniesienia do tego, co należy wziąć pod uwagę podczas określonej fazy projektu dewelopera, ale sugerujemy, aby przeczytać wszystkie artykuły od początku do końca co najmniej raz. Czytanie wszystkich artykułów wprowadza do pojęć, które mogły zostać pominięte we wcześniejszych fazach projektu. Wdrożenie tych koncepcji przed wydaniem produktu może pomóc w tworzeniu bezpiecznego oprogramowania, spełnianiu wymagań dotyczących zgodności zabezpieczeń i obniżaniu kosztów rozwoju.

Te artykuły mają być zasobem dla projektantów oprogramowania, deweloperów i testerów na wszystkich poziomach, którzy twórz i wdrażają bezpieczne aplikacje platformy Azure.

## <a name="overview"></a>Omówienie

Bezpieczeństwo jest jednym z najważniejszych aspektów każdej aplikacji i nie jest to prosta rzecz, aby uzyskać prawo. Na szczęście platforma Azure oferuje wiele usług, które mogą pomóc w zabezpieczeniu aplikacji w chmurze. Te artykuły dotyczą działań i usług platformy Azure, które można zaimplementować na każdym etapie cyklu życia tworzenia oprogramowania, aby ułatwić tworzenie bezpieczniejszego kodu i wdrażanie bezpieczniejszej aplikacji w chmurze.

## <a name="security-development-lifecycle"></a>Cykl projektowania zabezpieczeń

Przestrzeganie najlepszych praktyk w zakresie bezpiecznego tworzenia oprogramowania wymaga włączenia zabezpieczeń do każdej fazy cyklu życia tworzenia oprogramowania, od analizy wymagań po konserwację, niezależnie od metodologii projektu[(wodospad,](https://en.wikipedia.org/wiki/Waterfall_model) [agile](https://en.wikipedia.org/wiki/Agile_software_development)lub [DevOps).](https://en.wikipedia.org/wiki/DevOps) W następstwie głośnych naruszeń danych i wykorzystania błędów zabezpieczeń operacyjnych, więcej deweloperów rozumie, że bezpieczeństwo musi być rozwiązywane w trakcie całego procesu rozwoju.

Im później rozwiążesz problem w cyklu życia rozwoju, tym bardziej ta poprawka będzie cię kosztować. Problemy z zabezpieczeniami nie są wyjątkiem. Jeśli nie uwzględnisz problemów z zabezpieczeniami we wczesnych fazach tworzenia oprogramowania, każda następująca faza może dziedziczyć luki w zabezpieczeniach poprzedniej fazy. Twój produkt końcowy zgromadzi wiele problemów z bezpieczeństwem i możliwość naruszenia. Budowanie zabezpieczeń w każdej fazie cyklu życia rozwoju pomaga wcześnie wyłapać problemy i pomaga zmniejszyć koszty rozwoju.

Śledzimy fazy cyklu rozwoju zabezpieczeń firmy Microsoft [(SDL),](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) aby wprowadzić działania i usługi platformy Azure, których można używać do realizacji bezpiecznych praktyk w zakresie tworzenia oprogramowania na każdym etapie cyklu życia.

Fazy SDL to:

  - Szkolenia
  - Wymagania
  - Projekt
  - Wdrażanie
  - Weryfikacja
  - Release
  - Odpowiedź

![Cykl projektowania zabezpieczeń](./media/secure-dev-overview/01-sdl-phase.png)

W tych artykułach grupujemy fazy SDL w projektowaniu, opracowywaniu i wdrażaniu.

## <a name="engage-your-organizations-security-team"></a>Zaangażuj zespół zabezpieczeń swojej organizacji

Organizacja może mieć formalny program zabezpieczeń aplikacji, który pomaga w działaniach związanych z zabezpieczeniami od początku do końca w cyklu życia rozwoju. Jeśli twoja organizacja ma zespoły zabezpieczeń i zgodności, należy zaangażować je przed rozpoczęciem tworzenia aplikacji. Zapytaj ich na każdym etapie SDL, czy są jakieś zadania, które przegapiłeś.

Rozumiemy, że wielu czytelników może nie mieć zespołu zabezpieczeń lub zgodności do zaangażowania. Te artykuły mogą pomóc w pytaniu i decyzjach dotyczących zabezpieczeń, które należy wziąć pod uwagę na każdym etapie SDL.

## <a name="resources"></a>Resources

Skorzystaj z następujących zasobów, aby dowiedzieć się więcej na temat tworzenia bezpiecznych aplikacji i pomóc w zabezpieczeniu aplikacji na platformie Azure:

[Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) — SDL to proces tworzenia oprogramowania firmy Microsoft, który pomaga deweloperom tworzyć bezpieczniejsze oprogramowanie. Pomaga rozwiązać wymagania dotyczące zgodności zabezpieczeń przy jednoczesnym obniżeniu kosztów rozwoju.

[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) — OWASP to społeczność online, która produkuje swobodnie dostępne artykuły, metodologie, dokumentację, narzędzia i technologie w dziedzinie zabezpieczeń aplikacji sieci Web.

[Wypychanie w lewo, jak szef](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) — seria artykułów online, która przedstawia różne typy działań zabezpieczeń aplikacji, które deweloperzy powinni wykonać, aby utworzyć bardziej bezpieczny kod.

[Platforma tożsamości firmy Microsoft](../../active-directory/develop/index.yml) — platforma tożsamości firmy Microsoft to ewolucja usługi tożsamości usługi Azure AD i platformy dewelopera. Jest to w pełni funkcjonalna platforma, która składa się z usługi uwierzytelniania, bibliotek open source, rejestracji i konfiguracji aplikacji, pełnej dokumentacji dewelopera, przykładów kodu i innej zawartości dewelopera. Platforma tożsamości firmy Microsoft obsługuje standardowe protokoły branżowe, takie jak OAuth 2.0 i OpenID Connect.

[Najważniejsze rozwiązania dotyczące zabezpieczeń dotyczące rozwiązań platformy Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) — zbiór najlepszych rozwiązań w zakresie zabezpieczeń, których można używać podczas projektowania, wdrażania i zarządzania rozwiązaniami w chmurze przy użyciu platformy Azure. Ten dokument ma być zasobem dla profesjonalistów IT. Może to obejmować projektantów, architektów, deweloperów i testerów, którzy twórz i wdrażają bezpieczne rozwiązania platformy Azure.

[Plany zabezpieczeń i zgodności na platformie Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) — plany zabezpieczeń i zgodności platformy Azure to zasoby, które mogą pomóc w tworzeniu i uruchamianiu aplikacji opartych na chmurze, które są zgodne z rygorystycznymi przepisami i standardami.

## <a name="next-steps"></a>Następne kroki
W poniższych artykułach zaleca się kontrolki zabezpieczeń i działania, które mogą pomóc w projektowaniu, opracowywaniu i wdrażaniu bezpiecznych aplikacji.

- [Projektowanie bezpiecznych aplikacji](secure-design.md)
- [Tworzenie bezpiecznych aplikacji](secure-develop.md)
- [Wdrażanie bezpiecznych aplikacji](secure-deploy.md)
