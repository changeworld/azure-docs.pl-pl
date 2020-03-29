---
title: Wymagania dotyczące tożsamości dla platformy Azure projektu tożsamości w chmurze hybrydowej | Dokumenty firmy Microsoft
description: Zidentyfikuj potrzeby biznesowe firmy, które doprowadzą Cię do zdefiniowania wymagań dotyczących projektu tożsamości hybrydowej.
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
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ecc90e13f49c231d8d3ab0cff1de91443b80f21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65950900"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Określanie wymagań dotyczących tożsamości dla rozwiązania tożsamości hybrydowej
Pierwszym krokiem w projektowaniu rozwiązania tożsamości hybrydowej jest określenie wymagań dla organizacji biznesowej, która będzie wykorzystaniem tego rozwiązania.  Tożsamość hybrydowa rozpoczyna się jako rola pomocnicza (obsługuje wszystkie inne rozwiązania w chmurze, zapewniając uwierzytelnianie) i zapewnia nowe i interesujące funkcje, które odblokowują nowe obciążenia dla użytkowników.  Te obciążenia lub usługi, które chcesz przyjąć dla użytkowników będzie dyktować wymagania dotyczące projektu tożsamości hybrydowej.  Te usługi i obciążenia muszą korzystać z tożsamości hybrydowej zarówno lokalnie, jak i w chmurze.  

Musisz przejść przez te kluczowe aspekty działalności, aby zrozumieć, co to jest wymóg teraz i co firma planuje na przyszłość. Jeśli nie masz widoczności długoterminowej strategii projektowania tożsamości hybrydowej, istnieje szansa, że twoje rozwiązanie nie będzie skalowalne w miarę rozwoju i zmiany potrzeb biznesowych. Poniższy diagram przedstawia przykład architektury tożsamości hybrydowej i obciążeń, które są odblokowane dla użytkowników. Jest to tylko przykład wszystkich nowych możliwości, które można odblokować i dostarczyć z solidną strategią tożsamości hybrydowej. 

Niektóre składniki, które są częścią ![architektury tożsamości hybrydowej architektury tożsamości hybrydowej](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Określanie potrzeb biznesowych
Każda firma będzie miała inne wymagania, nawet jeśli te firmy są częścią tej samej branży, rzeczywiste wymagania biznesowe mogą się różnić. Nadal można wykorzystać najlepsze praktyki z branży, ale ostatecznie to potrzeby biznesowe firmy, które doprowadzą Cię do zdefiniowania wymagań dla projektu tożsamości hybrydowej. 

Aby zidentyfikować potrzeby biznesowe, należy odpowiedzieć na następujące pytania:

* Czy Twoja firma chce obniżyć koszty operacyjne IT?
* Czy Twoja firma chce zabezpieczyć zasoby chmury (aplikacje SaaS, infrastruktura)?
* Czy Twoja firma chce zmodernizować swój IT?
  * Czy użytkownicy są bardziej mobilni i wymagają od działu IT tworzenia wyjątków w strefie DMZ, aby umożliwić dostęp do różnych zasobów innego typu ruchu?
  * Czy Twoja firma ma starsze aplikacje, które musiały zostać opublikowane tym współczesnym użytkownikom, ale nie są łatwe do przepisania?
  * Czy Twoja firma musi wykonać wszystkie te zadania i zapewnić kontrolę w tym samym czasie?
* Czy Twoja firma chce zabezpieczyć tożsamość użytkowników i zmniejszyć ryzyko, wprowadzając nowe narzędzia, które wykorzystują wiedzę specjalistyczną firmy Microsoft w zakresie zabezpieczeń platformy Azure w środowisku lokalnym?
* Czy Twoja firma próbuje pozbyć się przerażających "zewnętrznych" kont w środowisku lokalnym i przenieść je do chmury, gdzie nie są już uśpionym zagrożeniem w środowisku lokalnym?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analizowanie lokalnej infrastruktury tożsamości
Teraz, gdy masz pomysł dotyczący wymagań biznesowych firmy, musisz ocenić lokalną infrastrukturę tożsamości. Ta ocena jest ważne dla definiowania wymagań technicznych, aby zintegrować bieżące rozwiązanie tożsamości do systemu zarządzania tożsamościami w chmurze. Pamiętaj, aby odpowiedzieć na następujące pytania:

* Jakie rozwiązanie uwierzytelniania i autoryzacji używa lokalnie przez Firmę? 
* Czy Twoja firma ma obecnie jakieś lokalne usługi synchronizacji?
* Czy Twoja firma korzysta z zewnętrznych dostawców tożsamości (IdP)?

Należy również pamiętać o usługach w chmurze, które firma może mieć. Przeprowadzenie oceny w celu zrozumienia bieżącej integracji z modelami SaaS, IaaS lub PaaS w twoim środowisku jest bardzo ważne. Podczas tej oceny należy odpowiedzieć na następujące pytania:

* Czy Twoja firma ma jakąś integrację z dostawcą usług w chmurze?
* Jeśli tak, które usługi są używane?
* Czy ta integracja jest obecnie w produkcji, czy jest to pilot?

> [!NOTE]
> Cloud Discovery analizuje dzienniki ruchu w katalogu aplikacji w chmurze usługi Microsoft Cloud App Security zawierającym ponad 16 000 aplikacji w chmurze, które są klasyfikowane i oceniane na podstawie ponad 70 czynników ryzyka, aby zapewnić bieżący wgląd w korzystanie z chmury, shadow IT i ryzyko, jakie shadow IT stwarza dla twojej organizacji. Aby rozpocząć, zobacz [Konfigurowanie odnajdywania chmury](/cloud-app-security/set-up-cloud-discovery).
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Ocena wymagań dotyczących integracji tożsamości
Następnie należy ocenić wymagania integracji tożsamości. Ta ocena jest ważne, aby zdefiniować wymagania techniczne dotyczące sposobu uwierzytelniania użytkowników, jak obecność organizacji będzie wyglądać w chmurze, jak organizacja zezwoli na autoryzację i jakie będzie środowisko użytkownika. Pamiętaj, aby odpowiedzieć na następujące pytania:

* Czy Twoja organizacja będzie używać federacji, uwierzytelniania standardowego lub obu tych czynów?
* Czy federacja jest wymagana?  Ze względu na następujące kwestie:
  * Logowanie jednokrotne oparte na protokole Kerberos
  * Twoja firma ma aplikacje lokalne (wbudowane w domu lub innej firmy), które używają SAML lub podobnych możliwości federacji.
  * mfa za pośrednictwem kart inteligentnych. RSA SecurID itp.
  * Reguły dostępu klienta, które odnoszą się do poniższych pytań:
    1. Czy mogę zablokować cały zewnętrzny dostęp do usługi Office 365 na podstawie adresu IP klienta?
    2. Czy mogę zablokować cały dostęp zewnętrzny do usługi Office 365, z wyjątkiem programu Exchange ActiveSync?
    3. Czy mogę zablokować cały dostęp zewnętrzny do usługi Office 365, z wyjątkiem aplikacji opartych na przeglądarce (OWA, SPO)
    4. Czy mogę zablokować cały dostęp zewnętrzny do usługi Office 365 dla członków wyznaczonych grup usług AD
* Kwestie związane z bezpieczeństwem/audytem
* Już istniejące inwestycje w uwierzytelnianie federacyjne
* Jaką nazwę będzie używać naszej organizacji dla naszej domeny w chmurze?
* Czy organizacja ma domenę niestandardową?
  1. Czy ta domena jest publiczna i łatwa do zweryfikowania za pośrednictwem systemu DNS?
  2. Jeśli tak nie jest, to czy masz domenę publiczną, która może służyć do rejestrowania alternatywnej nazwy UPN w urzędzie ad?
* Czy identyfikatory użytkownika są spójne dla reprezentacji chmury? 
* Czy organizacja ma aplikacje, które wymagają integracji z usługami w chmurze?
* Czy organizacja ma wiele domen i czy wszystkie będą używać uwierzytelniania standardowego lub federacyjnego?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Ocenianie aplikacji uruchamianych w twoim środowisku
Teraz, gdy masz pomysł dotyczący infrastruktury lokalnej i chmury, należy ocenić aplikacje, które działają w tych środowiskach. Ta ocena jest ważne, aby zdefiniować wymagania techniczne, aby zintegrować te aplikacje do systemu zarządzania tożsamościami w chmurze. Pamiętaj, aby odpowiedzieć na następujące pytania:

* Gdzie będą żyły nasze aplikacje?
* Czy użytkownicy będą uzyskiwać dostęp do aplikacji lokalnych?  W chmurze? Albo jedno i drugie?
* Czy istnieją plany podjęcia istniejących obciążeń aplikacji i przenieść je do chmury?
* Czy istnieją plany tworzenia nowych aplikacji, które będą rezydować lokalnie lub w chmurze, które będą korzystać z uwierzytelniania w chmurze?

## <a name="evaluate-user-requirements"></a>Ocena wymagań użytkownika
Należy również ocenić wymagania użytkownika. Ta ocena jest ważne, aby zdefiniować kroki, które będą potrzebne do wejścia na pokład i pomocy użytkownikom podczas przechodzenia do chmury. Pamiętaj, aby odpowiedzieć na następujące pytania:

* Czy użytkownicy będą uzyskiwać dostęp do aplikacji lokalnie?
* Czy użytkownicy będą uzyskiwać dostęp do aplikacji w chmurze?
* Jak użytkownicy zazwyczaj logują się do środowiska lokalnego?
* W jaki sposób użytkownicy będą logować się do chmury?

> [!NOTE]
> Pamiętaj, aby zanotować wszystkie odpowiedzi i zrozumieć ich uzasadnienie. [Określ wymagania dotyczące reagowania na incydenty](plan-hybrid-identity-design-considerations-incident-response-requirements.md) zostaną przejmą dostępne opcje i zalety/minusy każdej opcji.  Odpowiadając na te pytania, wybierzesz opcję, która najlepiej odpowiada Twoim potrzebom biznesowym.
> 
> 

## <a name="next-steps"></a>Następne kroki
[Określanie wymagań dotyczących synchronizacji katalogów](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień projektowych](plan-hybrid-identity-design-considerations-overview.md)

