---
title: Azure Active Directory hybrydowego zagadnienia dotyczące projektowania tożsamości - Przegląd | Dokumentacja firmy Microsoft
description: Omówienie i Mapa zawartości z przewodnika po zagadnieniach dotyczących projektowania tożsamości hybrydowej
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: e81908e3fd77b8fde706b27c3bed305ad0436677
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801640"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Zagadnienia dotyczące projektowania tożsamości hybrydowej usługi Azure Active Directory
Konsumenckie urządzeń są proliferating world firmy i oprogramowania jako usługa (SaaS) aplikacji działających w chmurze są łatwe do przyjęcia. W związku z tym zachowaniu kontroli nad dostęp do aplikacji użytkowników wewnętrznych platformach centrów danych i w chmurze może być trudne.  

Firmy Microsoft tożsamościach span lokalnych i chmurze możliwości tworzenia tożsamością jednego użytkownika do uwierzytelniania i autoryzacji do wszystkich zasobów, niezależnie od lokalizacji. To pojęcie jest określany jako tożsamość hybrydowa. Brak różnorodności rozwiązań i opcji konfiguracji dla tożsamość hybrydowa korzystania z rozwiązań firmy Microsoft, a w niektórych przypadkach może być trudne do ustalenia, która kombinacja będzie najlepiej odpowiadać potrzebom organizacji. 

Przewodnik dla zagadnień projektowych ten hybrydowego tożsamości ułatwią zrozumienie sposobu projektowania rozwiązania z tożsamością hybrydową który najlepiej odpowiada biznesowe i technologiczne organizacji.  Ten przewodnik zawiera szczegóły serii kroków i zadań, które możesz wykonać, aby ułatwić projektowanie rozwiązania z tożsamością hybrydową spełniającego specyficzne wymagania danej organizacji. W ramach zadań i kroków przewodnik przedstawia odpowiednich technologii i funkcji opcji dostępnych dla organizacji, które spełniają funkcjonalności i jakości usługi (np. dostępność, skalowalność, wydajność, możliwości zarządzania i bezpieczeństwo) poziom wymagania. 

W szczególności celów przewodnik zagadnienia dotyczące projektowania tożsamości hybrydowego mają odpowiedzieć na następujące pytania: 

* Na jakie pytania muszę pytania i odpowiedzi do kierowania hybrydowego tożsamości specyficzne dla technologii lub dziedziny problemu czy najlepiej odpowiadają wymaganiom?
* Jaką sekwencję działań powinno zakończyć Projektowanie rozwiązania z tożsamością hybrydową dla technologii lub dziedziny problemu? 
* Jakie hybrydowego tożsamości technologii i konfiguracji są dostępne opcje ułatwiające spełnienie moich wymagań? Co to są kompromis między tymi opcjami, dzięki czemu można wybrać opcję najlepiej firmową?

## <a name="who-is-this-guide-intended-for"></a>Kogo jest przeznaczony ten przewodnik?
 CIO, CITO główny tożsamości architektów, architektów przedsiębiorstwa i architekci systemów informatycznych odpowiedzialni za projektowanie rozwiązania z tożsamością hybrydową dla średnich i dużych organizacji.

## <a name="how-can-this-guide-help-you"></a>Jak może być pomocny ten przewodnik?
Zrozumienie sposobu projektowania rozwiązania z tożsamością hybrydową, który jest w stanie zintegrować system zarządzania tożsamościami w chmurze wraz z bieżącym rozwiązaniem tożsamości lokalnych, można użyć w tym przewodniku. 

Na poniższym rysunku przedstawiono przykład hybrydowego tożsamości, która umożliwia administratorom IT zarządzanie zintegrować ich bieżącym Windows Server Active Directory rozwiązania, znajdujących się lokalnie z Microsoft Azure Active Directory, aby użytkownicy mogli korzystać z logowania jednokrotnego ( SSO) w aplikacjach znajdujących się w chmurze i lokalnie.

![](./media/hybrid-id-design-considerations/hybridID-example.png)

Powyższej ilustracji jest przykładem rozwiązania z tożsamością hybrydową, który polega na wykorzystaniu usług w chmurze do integracji z funkcjami lokalnymi w celu zapewnienia jednego środowiska do procesu uwierzytelniania użytkowników końcowych i ułatwia zarządzanie tymi IT zasoby. Chociaż w tym przykładzie może być typowy scenariusz, może być inna niż przykładzie pokazano na rysunku 1 z powodu różnych wymogów jest projektowania tożsamości hybrydowej każdej organizacji. 

Ten przewodnik zawiera serię kroków i zadań, które można wykonać w celu zaprojektowania rozwiązania z tożsamością hybrydową spełniającego specyficzne wymagania danej organizacji. Poniższe kroki i zadania przewodnik przedstawia informacje o odpowiednich technologiach i opcjach funkcji dostępnych w celu spełnienia funkcjonalne i dotyczące poziomu jakości usług dla Twojej organizacji.

**Założenia**: użytkownik ma pewne doświadczenie z systemu Windows Server, usług domenowych w usłudze Active Directory i Azure Active Directory. W tym dokumencie zakłada się, że użytkownik chce się dowiedzieć, jak te rozwiązania mogą spełnić potrzeby biznesowe samodzielnie lub w rozwiązaniu zintegrowanym.

## <a name="design-considerations-overview"></a>Omówienie zagadnień dotyczących projektowania
Ten dokument zawiera zestaw kroków i zadań, które można wykonać w celu zaprojektowania rozwiązania z tożsamością hybrydową, która najlepiej odpowiada wymaganiom. Kroki są prezentowane w uporządkowanej kolejności. Zagadnienia dotyczące projektowania, które użytkownik pozna w kolejnych krokach może wymagać zmiany decyzji podjętych we wcześniejszych krokach, jednak z powodu podjęcia sprzecznych decyzji projektowych. Staramy alert potencjalnymi konfliktami projektowymi dokumentu. 

Zostanie przyjeździe projekt najlepiej spełniający wymagania dopiero po wykonaniu kroków tyle razy, ile włączenie wszystkich zagadnień opisane w tym dokumencie. 

| Faza tożsamości hybrydowej | Lista tematów |
| --- | --- |
| Określenie wymagań dotyczących tożsamości |[Określanie potrzeb biznesowych](active-directory-hybrid-identity-design-considerations-business-needs.md)<br> [Określenie wymagań synchronizacji katalogu](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Określić wymagania dotyczące uwierzytelniania wieloskładnikowego](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definiowanie strategii wdrażania tożsamości hybrydowej](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Planowanie wzmocnienia bezpieczeństwa danych za pośrednictwem rozwiązania silnej tożsamości |[Określenie wymagań dotyczących ochrony danych](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Określenie wymagań dotyczących zarządzania zawartością](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Określenie wymagań dotyczących kontroli dostępu](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Określenie wymagań dotyczących odpowiedzi na zdarzenia](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definiowanie strategii ochrony danych](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Plan cyklu życia tożsamości hybrydowej |[Określić zadań zarządzania tożsamości hybrydowej](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Zarządzanie synchronizacji](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Określić strategii wdrażania zarządzania tożsamości hybrydowej](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="download-this-guide"></a>Pobierz w tym przewodniku
Możesz pobrać wersję pdf przewodnika zagadnienia dotyczące projektowania tożsamości hybrydowej z [galerii Technet](https://gallery.technet.microsoft.com/Azure-Hybrid-Identity-b06c8288). 

