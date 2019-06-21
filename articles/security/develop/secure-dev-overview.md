---
title: Zabezpieczanie rozwoju najlepszych rozwiązań w systemie Microsoft Azure
description: Najlepsze rozwiązania ułatwiające tworzenie bezpieczniejszego kodu i wdrażanie aplikacji bardziej bezpieczne w chmurze.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: ee5c043038fbb747e90bca9530cbe2e94c8a95c2
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144466"
---
# <a name="secure-development-best-practices-on-azure"></a>Zabezpieczanie rozwoju najlepszych rozwiązań na platformie Azure
Ta seria artykułów zawiera działania dotyczące zabezpieczeń i kontroli należy wziąć pod uwagę podczas opracowywania aplikacji dla chmury. Znajdują się w fazach pytania cykl projektowania zabezpieczeń (SDL) i zabezpieczeń oraz założeniach do uwzględnienia w poszczególnych fazach cyklu życia. Celem jest pomagającym w zdefiniowaniu działań i usług platformy Azure, które umożliwia w poszczególnych fazach cyklu życia projektowania, opracowywania i wdrażania aplikacji bardziej bezpieczne.

Zalecenia w artykułach pochodzą z naszego środowiska Dzięki zabezpieczeniom platformy Azure i procesy, przez naszych klientów. Te artykuły można używać jako odwołanie, na jakie należy rozważyć w fazie określonego projektu rozwoju, ale zaleca się również przeczytanie artykułu za pośrednictwem wszystkich artykułów od początku do końca co najmniej raz. Odczytywanie wszystkich artykułów poznasz pojęcia, które mogły zostać pominięte w wcześniejsze fazy projektu. Wdrażanie tych pojęć przed udostępnieniem produktu może ułatwić tworzenie bezpiecznego oprogramowania, spełnienie wymagań zgodności zabezpieczeń i zmniejszyć koszty rozwoju.

Te artykuły są przeznaczone do zasobu dla oprogramowania projektantów oraz deweloperów i testerów na wszystkich poziomach, kto twórz i wdrażaj bezpieczne aplikacje platformy Azure.

## <a name="overview"></a>Omówienie

Bezpieczeństwo jest jednym z najważniejszych aspektów dowolnej aplikacji, a nie jest proste rzeczy, aby uzyskać odpowiednie. Na szczęście system Azure udostępnia wiele usług, które mogą pomóc Ci zabezpieczenia aplikacji w chmurze. Te artykuły dotyczą działań i usług platformy Azure, można wdrożyć na każdym etapie cyklu życia tworzenia oprogramowania do tworzenia bezpieczniejszego kodu i wdrażanie bardziej bezpieczną aplikację w chmurze.

## <a name="security-development-lifecycle"></a>Cykl projektowania zabezpieczeń

Zgodnie z najlepszymi rozwiązaniami do tworzenia bezpiecznego oprogramowania wymaga integracji zabezpieczeń w poszczególnych fazach cyklu życia tworzenia oprogramowania z analizy wymaganie w tryb konserwacji, niezależnie od tego, metodologii projektu ([kaskadowe](https://en.wikipedia.org/wiki/Waterfall_model), [agile](https://en.wikipedia.org/wiki/Agile_software_development), lub [DevOps](https://en.wikipedia.org/wiki/DevOps)). Wznawiania naruszeń danych wysokiej jakości i wykorzystywania luk w zabezpieczeniach operacyjnej liczby deweloperów są zrozumienie zabezpieczeń musi być kierowane przez cały proces projektowania.

Później możesz rozwiązać problem w cyklu wdrażania więcej, które naprawić będzie koszt. Problemy z bezpieczeństwem są żaden wyjątek. Jeśli problemy z zabezpieczeniami można pominąć na wczesnych etapach procesu tworzenia oprogramowania, każda faza, która jest zgodna może dziedziczyć luk w zabezpieczeniach poprzedniego etapu. Ostateczny produkt będzie współdzielenia wiele problemów z zabezpieczeniami oraz możliwość naruszenia zabezpieczeń. Tworzenie zabezpieczeń w poszczególnych fazach cyklu życia opracowywania ułatwia wczesne wyłapywanie problemów i pomaga ograniczyć koszty programowania.

Postępujemy zgodnie z fazy Microsoft [cykl projektowania zabezpieczeń (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) wprowadzenie działań i usług platformy Azure służących do spełnienia praktyk tworzenia bezpiecznego oprogramowania w poszczególnych fazach cyklu życia.

Etapy SDL to:

  - Szkolenia
  - Wymagania
  - Projekt
  - Wdrażanie
  - Weryfikacja
  - Release
  - Odpowiedź

![Cykl projektowania zabezpieczeń](./media/secure-dev-overview/01-sdl-phase.png)

W tych artykułach możemy grupowania SDL fazy projektowania, opracowywania i wdrażania.

## <a name="engage-your-organizations-security-team"></a>Zaangażuj zespół ds. zabezpieczeń w organizacji

Twoja organizacja może mieć program zabezpieczeń formalne aplikacji pomaga działania dotyczące zabezpieczeń od początku do końca podczas cyklu rozwoju. Jeśli Twoja organizacja ma zespołów, zabezpieczeń i zgodności, należy skontaktować się z nimi przed rozpoczęciem tworzenia aplikacji. Poproś go na każdym etapie SDL czy istnieją wszystkie zadania, które pominięte.

Rozumiemy, wielu czytników może nie mieć zespołu zabezpieczeń i zgodności dzięki którym można zaangażować. Te artykuły pomocne w pytań zabezpieczających oraz decyzje, które należy wziąć pod uwagę na każdym etapie proces SDL.

## <a name="resources"></a>Zasoby

Aby dowiedzieć się więcej na temat tworzenia bezpiecznych aplikacji i do zabezpieczania aplikacji na platformie Azure, użyj następujących zasobów:

[Cykl projektowania zabezpieczeń (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) — SDL to proces projektowania oprogramowania firmy Microsoft, która ułatwia deweloperom tworzenie bezpieczniejszego oprogramowania. Ułatwia spełnienie wymagań zgodności zabezpieczeń, jednocześnie zmniejszając koszty rozwoju.

[Otwórz w sieci Web aplikacji Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) — OWASP to społeczność online, który produkuje artykuły dostępne bezpłatnie, metodologie, dokumentacji, narzędzi i technologii w dziedzinie zabezpieczeń aplikacji sieci web.

[Wypchnięcie w lewo, takich jak szefa](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) — seria artykułów online zawierający różnego rodzaju działania dotyczące zabezpieczeń aplikacji, które deweloperzy powinno zakończyć się do tworzenia bezpieczniejszego kodu.

[Platforma tożsamości usługi Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) — platformą Microsoft identity jest ewolucją platforma usługi i dla deweloperów tożsamości usługi Azure AD. Jest w pełni funkcjonalne platforma, która składa się z usługi uwierzytelniania, bibliotek typu open source, rejestrowanie aplikacji i konfiguracji, dokumentacji dla deweloperów pełną, przykłady kodu i innej zawartości dla deweloperów. Platforma tożsamości firmy Microsoft obsługuje protokoły będące standardami branżowymi, takich jak OAuth 2.0 i OpenID Connect.

[Najlepsze rozwiązania dotyczące rozwiązań platformy Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) — kolekcja najważniejsze wskazówki dotyczące zabezpieczeń do użycia podczas projektowania, wdrażania i zarządzania rozwiązaniami w chmurze za pomocą platformy Azure. Ten dokument jest przeznaczony do zasobu dla specjalistów IT. Może to obejmować projektantów, architektów, deweloperów i testerów, którzy tworzenie i wdrażanie bezpiecznych rozwiązań platformy Azure.

[Plany zabezpieczeń i zgodności na platformie Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) — plany zabezpieczeń platformy Azure i zgodności są zasoby, które mogą pomóc Ci tworzenie i uruchamianie aplikacji opartych na chmurze, zgodne ze ścisłymi regulacjami i standardami.

## <a name="next-steps"></a>Kolejne kroki
W następujących artykułach firma Microsoft zaleca środki kontroli bezpieczeństwa i działań, które mogą pomóc Ci projektowania, opracowywania i wdrażaj bezpieczne aplikacje.

- [Projektowanie bezpiecznych aplikacji](secure-design.md)
- [Tworzenie bezpiecznych aplikacji](secure-develop.md)
- [Wdrażaj bezpieczne aplikacje](secure-deploy.md)
