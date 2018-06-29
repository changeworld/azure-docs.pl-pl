---
title: Wymagania dotyczące tożsamości dla hybrydowych chmury projektowania tożsamości Azure | Dokumentacja firmy Microsoft
description: Ustalenie potrzeb firmy firmy, które prowadzą do definiowania wymagań dotyczących projektowania tożsamości hybrydowej.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/27/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: a641464e04421b90de15cd953bb8a0fbf9b687d3
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084726"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Określenie wymagań dotyczących tożsamości dla rozwiązania z tożsamością hybrydową
Pierwszy krok w projektowaniu rozwiązania z tożsamością hybrydową jest ustalenie wymagań dotyczących organizacji biznesowych, która będzie wykorzystanie tego rozwiązania.  Tożsamość hybrydowa uruchamia rolę pomocniczych (obsługuje on innych rozwiązań w chmurze, zapewniając uwierzytelniania) i przechodzi do mają nowe, ciekawe funkcje, które odblokować nowych obciążeń dla użytkowników.  Te obciążenia lub usługi, które chcesz przyjąć dla użytkowników będzie określać wymagania dotyczące projektowania tożsamości hybrydowej.  Te usługi i obciążeń należy wykorzystać tożsamość hybrydowa lokalnie i w chmurze.  

Należy zapoznać się z kluczowymi aspektami biznesowe, aby zrozumieć, jakie są wymagania teraz i firma planuje w przyszłości. Jeśli nie masz widoczność długoterminowej strategii dla projektowania tożsamości hybrydowej prawdopodobnie czy rozwiązania nie będzie skalowalne miarę wzrostu i rozwoju firmy.   T on diagramie poniżej przedstawiono przykład architektura tożsamości hybrydowej i obciążeń, które są trwa odblokowywanie dla użytkowników. Jest to przykład wszystkie nowe możliwości, które można odblokować i dostarczane ze strategią tożsamość hybrydowa stałe. 

Niektóre składniki, które są częścią architektury tożsamości hybrydowej ![](./media/hybrid-id-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Określanie potrzeb biznesowych
Każda firma będzie miała inne wymagania, nawet jeśli firmy działają w tej samej branży, organizację rzeczywiste wymagania mogą się różnić. Oczywiście można wykorzystywać najlepsze rozwiązania z branży, ale ostatecznie to potrzeby biznesowe firmy, które prowadzą do definiowania wymagań dotyczących projektowania tożsamości hybrydowej. 

Upewnij się, że odpowiedzieć na następujące pytania na ustalenie potrzeb firmy:

* Firma poszukuje wycinania kosztów operacyjnych IT?
* Firma potrzebuje do zabezpieczania zasobów chmury (aplikacji SaaS, infrastruktury)
* Firma chce modernizacji dział IT?
  * Czy użytkownicy bardziej mobilni i wymagających IT, aby utworzyć wyjątki w sieci Obwodowej, aby umożliwić innego typu ruchu na dostęp do różnych zasobów?
  * Czy firma dysponuje starsze aplikacje, które wymagane do opublikowania dla tych użytkowników nowoczesnych, ale nie są łatwe do ponownego zapisywania?
  * Czy firma musi wykonać te zadania i przełączyć go pod kontrolą w tym samym czasie?
* Firma potrzebuje do zabezpieczania tożsamości użytkowników i zmniejszyć ryzyko przełączając nowe narzędzia korzystających z doświadczenia firmy Microsoft Azure zabezpieczeń doświadczenia z lokalnej firmy?
* Firma chce się pozbyć się dreaded "external" kont lokalnie i przenieś je do chmury, w którym nie są one nieaktywni zagrożeń w środowisku lokalnym?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analizowanie lokalnej infrastruktury tożsamości
Teraz, gdy masz pomysł dotyczące wymagania biznesowe firmy, musisz ocenić lokalnej infrastruktury tożsamości. Tej oceny jest ważne w przypadku definiowania wymagania techniczne do integracji tożsamości rozwiązanie do systemu zarządzania tożsamości chmury. Upewnij się, że należy odpowiedzieć na następujące pytania:

* Jakie rozwiązania uwierzytelniania i autoryzacji jest firma użyć lokalnego? 
* Czy firma ma obecnie żadnych lokalnych usług synchronizacji?
* Czy firma korzysta żadnych innych dostawców tożsamości (IdP)?

Należy mieć świadomość usługi w chmurze, które firma może mieć. Bardzo ważne jest wykonanie oceny zrozumienie bieżącego integracji z modelami SaaS, IaaS i PaaS w środowisku. Upewnij się, że należy odpowiedzieć na następujące pytania podczas oceny:

* Czy firma dysponuje zintegrowany z dostawcą usług w chmurze?
* Jeśli tak, usług, które są używane?
* Trwa tej integracji w środowisku produkcyjnym lub jest on pilotażu?

> [!NOTE]
> Rozwiązania cloud Discovery analizuje dzienników ruchu sieciowego dla wykazu aplikacji w chmurze firmy Microsoft Cloud App Security przez ponad 16 000 chmury, które aplikacje, które są pozycjonowane i oceniane w oparciu więcej niż 70 czynników ryzyka, aby zapewnić trwającą wgląd w chmurze użyć, cień IT oraz ryzykiem Cienia stanowi IT w Twojej organizacji. Aby uzyskać wstępne informacje, zobacz [konfigurowanie rozwiązania Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Ocena wymagań integracja tożsamości
Następnie należy do oceny wymagań dotyczących integracji tożsamości. Ocena ważne jest, aby zdefiniować wymagania techniczne dotyczące sposobu uwierzytelniania użytkowników, wygląd obecności organizacji w chmurze, jak dozwolony autoryzacji w organizacji i środowisko użytkownika ma być. Upewnij się, że należy odpowiedzieć na następujące pytania:

* Twoja organizacja użyje federacyjnych, standardowe uwierzytelnianie lub obu?
* Jest to wymagane federacyjnej?  Z następującej przyczyny:
  * Usługa rejestracji Jednokrotnej opartego na protokole Kerberos
  * Twoja firma ma aplikacji lokalnych (albo utworzona strona wewnętrznych lub 3), które używa SAML lub podobne możliwości federacji.
  * Usługi MFA za pomocą kart inteligentnych. RSA SecurID, etc.
  * Reguły dostępu klienta, które rozwiązują pytań poniżej:
    1. Czy można zablokować zewnętrzny dostęp do usługi Office 365 na podstawie adresu IP klienta
    2. Czy można zablokować zewnętrzny dostęp do usługi Office 365, z wyjątkiem programu Exchange ActiveSync?
    3. Czy można zablokować zewnętrzny dostęp do usługi Office 365, z wyjątkiem aplikacji opartych na przeglądarce (program OWA, SPO)
    4. Dla członków wyznaczonych grup usługi Active Directory można zablokować zewnętrzny dostęp do usługi Office 365
* Inspekcja zabezpieczeń/problemy
* Już istniejących inwestycji w uwierzytelniania federacyjnego
* Jaką nazwę naszej organizacji będą korzystać z naszych domeny w chmurze?
* Czy organizacja ma niestandardową domenę?
  1. Jest tej domeny publicznej i łatwy do zweryfikowania przy użyciu usługi DNS?
  2. Jeśli nie, następnie masz domeny publicznej, który może służyć do rejestrowania alternatywne nazwy UPN w usłudze AD?
* Identyfikatory użytkowników są spójne dla reprezentacji chmury? 
* Czy organizacja ma aplikacje, które wymagają do działania integracji z usługami w chmurze?
* Organizacja ma wiele domen i użyje wszystkie standardowe lub federacyjnych uwierzytelniania?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Ocena aplikacji działających w środowisku
Teraz, gdy masz pomysł dotyczące lokalną i infrastruktury w chmurze, należy ocenić aplikacje, które uruchamiane w tych środowiskach. Ocena ważne jest, aby zdefiniować wymagania techniczne, aby zintegrować te aplikacje do systemu zarządzania tożsamości chmury. Upewnij się, że należy odpowiedzieć na następujące pytania:

* Gdy będzie funkcjonować nasze aplikacje?
* Uzyskują użytkownikom dostęp do lokalnych aplikacji?  W chmurze? Czy oba?
* Czy istnieją plany wziąć istniejących obciążeń aplikacji i przenieś je do chmury?
* Czy istnieją plany tworzyć nowe aplikacje, które będą znajdować się lokalnie lub w chmurze, która będzie używana w chmurze uwierzytelniania?

## <a name="evaluate-user-requirements"></a>Ocena wymagań użytkownika
Należy również ocenić wymagań użytkownika. Ocena ważne jest, aby zdefiniować czynności, które będą potrzebne do dołączania i wspomaganie użytkowników, zgodnie z ich przejście do chmury. Upewnij się, że należy odpowiedzieć na następujące pytania:

* Uzyskują użytkownikom dostęp do aplikacji lokalnych?
* Uzyskują użytkownikom dostęp do aplikacji w chmurze?
* Jak użytkownicy zazwyczaj logowanie do ich w środowisku lokalnym?
* Jak będzie użytkowników logowania w chmurze?

> [!NOTE]
> Upewnij się zanotować wszystkie odpowiedzi i zrozumieć ich uzasadnienie. [Określenie wymagań dotyczących odpowiedzi na zdarzenia](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) będą przekazywane dostępne opcje oraz zalety/wady każdej opcji.  Po udzieleniu odpowiedzi na te pytania można wybrać opcji najlepiej pasujące do firmy wymaga.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
[Określenie wymagań synchronizacji katalogu](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Zobacz także
[Omówienie zagadnień dotyczących projektowania](active-directory-hybrid-identity-design-considerations-overview.md)

