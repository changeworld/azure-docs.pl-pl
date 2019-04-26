---
title: Wymagania dotyczące tożsamości dla hybrydowych w chmurze projektowania tożsamości platformy Azure | Dokumentacja firmy Microsoft
description: Ustalenie potrzeb firmy w firmie, prowadzących do definiowania wymagań dotyczących projektowania tożsamości hybrydowej.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47273bf687d88e82afba5de878f08bd53422479c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381387"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Określić wymagania dotyczące tożsamości dla Twojego rozwiązania tożsamości hybrydowej
Pierwszym krokiem podczas projektowania rozwiązania tożsamości hybrydowej jest do określenia wymagań dla organizacji biznesowej, która będzie wykorzystaniu tego rozwiązania.  Tożsamość hybrydowa startuje jako rolę pomocniczą (obsługuje on wszystkie inne rozwiązania w chmurze, zapewniając uwierzytelniania) i przechodzi do możliwości i interesujące, które uwalniają nowych obciążeń dla użytkowników.  Te obciążenia lub usługi, które chcesz przyjmuje użytkowników określają wymagania dotyczące projektowania tożsamości hybrydowej.  Te usługi i obciążeń musiał skorzystać tożsamości hybrydowej w środowisku lokalnym i w chmurze.  

Należy zapoznać się z kluczowymi aspektami biznesowe, aby zrozumieć, jakie są wymagania teraz i firma planuje w przyszłości. Jeśli nie masz widoczność długoterminowych strategii projektowania tożsamości hybrydowej jest szansa, że rozwiązanie nie będzie skalowalne potrzeb i rozwoju firmy.   T on na diagramie poniżej przedstawiono przykład architektury tożsamości hybrydowej i obciążeń, które są jest odblokowana dla użytkowników. To przykładowe wszystkich nowych możliwości, które mogą być odblokowane i dostarczane dzięki strategii w zakresie tożsamości hybrydowej stałych. 

Niektóre składniki, które są częścią architektury tożsamości hybrydowej ![](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Określanie potrzeb biznesowych
Każda firma będzie miała inne wymagania, nawet jeśli te firmy są częścią tej samej branży rzeczywistych biznesowych, które wymagań może się różnić. Można wykorzystywać najlepsze rozwiązania z branży, ale ostatecznie to potrzeby biznesowe firmy, które spowoduje przejście do definiowania wymagań dotyczących projektowania tożsamości hybrydowej. 

Pamiętaj odpowiedzieć na następujące pytania, aby zidentyfikować potrzeby biznesowe:

* Firma poszukuje wycinania kosztów operacyjnych IT
* Firma poszukuje do zabezpieczania zasobów w chmurze (SaaS, aplikacji i infrastruktury)
* To firmie chcą zmodernizować dział IT?
  * Czy użytkownicy bardziej mobilni i wymagających IT, aby utworzyć wyjątki w sieci Obwodowej, aby zezwolić na różnych typów ruchu sieciowego, aby uzyskać dostęp do różnych zasobów?
  * Czy firma dysponuje starsze aplikacje, które wymagane do opublikowania dla tych użytkowników nowoczesne, ale nie są łatwe do ponownego zapisywania?
  * Czy firma potrzebuje do wykonywania tych zadań i przenieś ją pod kontrolą w tym samym czasie?
* Firma poszukuje do zabezpieczania tożsamości użytkowników i zmniejszyć ryzyko, dodając nowe narzędzia, korzystających z doświadczeń firmy Microsoft zabezpieczeń platformy Azure doświadczenie w środowisku lokalnym
* Firma próbuje pozbyć się dreaded kont "external" w środowisku lokalnym i przenieść je do chmury, w którym nie są już nieaktywni zagrożeń wewnątrz usługi w środowisku lokalnym?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analizowanie lokalnej infrastruktury tożsamości
Teraz, gdy masz pomysł dotyczące Twoje wymagania biznesowe firmy, należy ocenić swoją lokalną infrastrukturę tożsamości. Ocena jest ważne w przypadku definiowania wymagań technicznych, aby zintegrować z bieżącego rozwiązania tożsamości do systemu zarządzania tożsamościami w chmurze. Pamiętaj odpowiedzieć na następujące pytania:

* Twoja firma działa to jakie rozwiązanie uwierzytelniania i autoryzacji używany lokalny? 
* Czy firma dysponuje obecnie żadnych usług synchronizacji w środowisku lokalnym?
* Firma używa dostawcy tożsamości (IdP) żadnych innych firm?

Należy również mieć świadomość usług w chmurze, które firma może polegać. Bardzo ważne jest wykonanie oceny zrozumienie bieżącego integracji z modelami SaaS, IaaS i PaaS w środowisku. Pamiętaj odpowiedzieć na następujące pytania podczas tej oceny:

* Czy firma dysponuje zintegrowany z dostawcy usług w chmurze?
* Jeśli tak, jakie usługi są używane?
* Trwa tej integracji w środowisku produkcyjnym, czy jest pilotażu?

> [!NOTE]
> Rozwiązanie cloud Discovery analizuje dzienniki ruchu względem wykazu aplikacji w chmurze Microsoft Cloud App Security ponad 16 000 chmury, aplikacje, które są pozycjonowane i oceniane na podstawie których ponad 70 czynników ryzyka, aby zapewnić ciągły wgląd w chmurze używasz, niezatwierdzonych przez dział IT oraz ryzykiem W tle stwarza IT w Twojej organizacji. Aby uzyskać wstępne informacje, zobacz [konfigurowanie rozwiązania Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Oceń wymagania dotyczące integracji tożsamości
Następnie należy ocenić wymagania dotyczące integracji tożsamości. Ocena ważne jest określenie wymagań technicznych dotyczących uwierzytelnianiu użytkowników, wygląd obecność organizacji w chmurze, jak dozwolony autoryzacji w organizacji i środowisko użytkownika ma być. Pamiętaj odpowiedzieć na następujące pytania:

* Twojej organizacji używać federacyjnego uwierzytelniania standardowego lub oba?
* Jest wymagana, federacyjnej?  Ze względu na następujący:
  * Usługa rejestracji Jednokrotnej z opartego na protokole Kerberos
  * Twoja firma ma lokalne aplikacje (albo wbudowany w wewnętrznych lub 3 firmy), których używa podobne możliwości federation lub SAML.
  * Uwierzytelnianie wieloskładnikowe za pomocą kart inteligentnych. RSA SecurID, etc.
  * Reguły dostępu klienta, które rozwiązują poniższe pytania:
    1. Czy można zablokować cały zewnętrzny dostęp do usługi Office 365 na podstawie adresu IP klienta?
    2. Czy można zablokować cały zewnętrzny dostęp do usługi Office 365, z wyjątkiem programu Exchange ActiveSync?
    3. Czy można zablokować cały zewnętrzny dostęp do usługi Office 365, z wyjątkiem aplikacji opartych na przeglądarce (program OWA, SPO)
    4. Dla członków grup usługi Active Directory wyznaczonym można zablokować cały zewnętrzny dostęp do usługi Office 365
* Dotyczy/inspekcji zabezpieczeń
* Już istniejących inwestycji w uwierzytelniania federacyjnego
* Jaką nazwę naszej organizacji będą korzystać z naszych domeny w chmurze?
* Czy organizacja ma niestandardową domenę?
  1. Jest tej domeny publicznej i łatwe do sprawdzenia przy użyciu usługi DNS?
  2. Jeśli nie jest dostępne, następnie masz domenę publiczną, który może służyć do rejestrowania alternatywne nazwy UPN w AD?
* Identyfikatory użytkowników są spójne dla reprezentacji chmury? 
* Czy organizacja ma aplikacje, które wymagają integracji z usługami w chmurze?
* Czy organizacja ma wiele domen i będą wszystkie używają standardowych lub federacyjnego uwierzytelniania?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Ocena aplikacji działających w Twoim środowisku
Skoro masz pomysł, dotyczące lokalnych i infrastruktury chmury, należy ocenić aplikacje, które działają w tych środowiskach. Ocena ważne jest, aby zdefiniować wymagania techniczne, aby zintegrować te aplikacje do systemu zarządzania tożsamościami w chmurze. Pamiętaj odpowiedzieć na następujące pytania:

* Gdzie będą znajdować naszych aplikacji
* Będą użytkownikom uzyskiwać dostęp do aplikacji lokalnych?  W chmurze? Lub oba?
* Czy istnieją plany, aby przejąć kontrolę istniejących obciążeń aplikacji i przenieść je do chmury?
* Czy istnieją plany, aby tworzyć nowe aplikacje, które będą znajdować się lokalnie lub w chmurze, która będzie używana w chmurze uwierzytelniania?

## <a name="evaluate-user-requirements"></a>Ocena wymagań użytkownika
Należy również ocenić wymagania użytkownika. Ocena ważne jest, aby zdefiniować czynności, które będą potrzebne do wdrażania i wspomaganie użytkowników, ponieważ mogą przejść do chmury. Pamiętaj odpowiedzieć na następujące pytania:

* Będą użytkownikom uzyskiwać dostęp do aplikacji w środowisku lokalnym?
* Będą użytkownikom uzyskiwać dostęp do aplikacji w chmurze?
* W jaki sposób użytkownicy zazwyczaj Zaloguj się do ich w środowisku lokalnym?
* Jak będzie logowania użytkownika do chmury?

> [!NOTE]
> Pamiętaj zanotować wszystkie odpowiedzi i zrozumieć uzasadnienie. [Określanie wymagań dotyczących odpowiedzi na zdarzenia](plan-hybrid-identity-design-considerations-incident-response-requirements.md) zostanie umieszczona nad dostępnych opcji i specjaliści/wady każdej opcji.  Po udzieleniu odpowiedzi na te pytania można wybrać opcję, która najlepiej pasujące do działalności potrzebuje.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
[Określić wymagania dotyczące synchronizacji katalogu](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Zobacz także
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)

