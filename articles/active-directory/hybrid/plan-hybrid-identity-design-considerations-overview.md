---
title: Usługa Azure Active Directory hybrydowego zagadnienia dotyczące projektowania tożsamości — omówienie | Dokumentacja firmy Microsoft
description: Omówienie i zawartości mapy Przewodnik po zagadnieniach dotyczących projektowania tożsamości hybrydowej
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7f8dd49f3668b8f68753681123a04d21edac46c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381493"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Zagadnienia dotyczące projektowania tożsamości hybrydowej usługi Azure Active Directory
Urządzenia z systemem odbiorcy są proliferating świecie korporacyjnym, a oprogramowanie jako usługa (SaaS) aplikacji działających w chmurze są łatwe do przyjęcia. W rezultacie zachowaniu kontroli nad użytkownikom aplikacji wewnętrznych platformach centrów danych i chmury może być trudne.  

Rozwiązania firmy Microsoft do obsługi tożsamości obejmują zarówno funkcje lokalne, jak i chmurowe, tworząc jedną tożsamość użytkownika na potrzeby uwierzytelniania i autoryzacji w kontekście wszystkich zasobów, niezależnie od lokalizacji. Takie podejście jest określany jako tożsamość hybrydowa. Istnieją różne projektowania i opcji konfiguracji dla tożsamości hybrydowej za pomocą rozwiązania firmy Microsoft, a w niektórych przypadkach może być trudno określić, która kombinacja będzie najlepiej spełnia wymagania danej organizacji. 

Ten podręcznik zagadnienia dotyczące projektowania tożsamości hybrydowej pomoże Ci zrozumieć, jak projektować rozwiązania z tożsamością hybrydową, najlepiej dostosowane do potrzeb firmy i technologiczne organizacji.  Ten przewodnik zawiera szczegóły serii kroków i zadań, które można wykonać, aby ułatwić projektowanie rozwiązania z tożsamością hybrydową spełniające unikatowe wymagania danej organizacji. W ramach zadań i kroków przewodnik będą obecne odpowiednich technologii i opcje dostępne dla organizacji, aby zaspokoić potrzeby funkcjonalności i jakości usługi (np. dostępność, skalowalność, wydajność, możliwości zarządzania i bezpieczeństwo) są wyposażone w poziomie wymagania. 

W szczególności celów przewodnik zagadnienia dotyczące projektowania tożsamości hybrydowej są odpowiedzieć na następujące pytania: 

* Na jakie pytania muszę pytania i odpowiedzi, aby zaprojektować specyficzne dla tożsamości hybrydowej technologii lub dziedziny problemu, najlepiej odpowiadają wymaganiom?
* Jakie sekwencję działań należy wykonać do projektowania rozwiązania tożsamości hybrydowej dla technologii lub dziedziny problemu? 
* Jakie hybrydowego tożsamości technologii i konfiguracji opcje są dostępne, które ułatwią spełnienie moich wymagań? Co to są kompromis między tymi opcjami, dzięki czemu mogę wybrać najlepszym rozwiązaniem dla mojej firmy?

## <a name="who-is-this-guide-intended-for"></a>Kogo jest przeznaczony ten przewodnik?
 CIO CITO, dyrektor tożsamości architektów, architektów w przedsiębiorstwach i architektów IT, które odpowiadają za projektowanie rozwiązania z tożsamością hybrydową dla średnich i dużych organizacji.

## <a name="how-can-this-guide-help-you"></a>Jak ten przewodnik pomoże Ci?
Aby dowiedzieć się, jak zaprojektować rozwiązanie tożsamości hybrydowej, które jest w stanie integracji systemu zarządzania tożsamościami oparta na chmurze z bieżącego rozwiązania tożsamości lokalnych, można użyć tego przewodnika. 

Na poniższym rysunku przedstawiono przykład rozwiązania tożsamości hybrydowej, która umożliwia administratorom IT do zarządzania zintegrować ich bieżącego Windows Server Active Directory rozwiązanie znajduje się w środowisku lokalnym za pomocą usługi Microsoft Azure Active Directory umożliwiające użytkownikom na używanie logowania jednokrotnego ( Logowanie Jednokrotne) w aplikacjach, które znajduje się w chmurze i lokalnych.

![Przykład](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

Ilustracja powyżej jest przykładem rozwiązania tożsamości hybrydowej, która polega na wykorzystaniu usług w chmurze do integracji z funkcjami lokalnymi w celu zapewnienia jednego środowiska do procesu uwierzytelniania użytkowników końcowych i ułatwienia IT zarządzanie tymi zasoby. Chociaż w tym przykładzie może być to typowy scenariusz, projektowania tożsamości hybrydowej każda organizacja jest może być inna niż przykładzie pokazano na rysunku 1 z powodu różnych wymagań. 

Ten przewodnik zawiera serię kroków i zadań, które można wykonać w celu projektowania rozwiązania tożsamości hybrydowej, który spełnia unikatowe wymagania danej organizacji. W następujących kroków i zadań przewodnik przedstawia informacje o odpowiednich technologiach i opcjach funkcji dostępnych możesz zapewnić wymaganą funkcjonalność i dotyczące poziomu jakości usługi dla swojej organizacji.

**Założenia**: Użytkownik ma pewne doświadczenie z systemu Windows Server, Active Directory Domain Services i Azure Active Directory. W tym dokumencie zakłada się, że szukasz jak te rozwiązania mogą spełniać Twoje potrzeby biznesowe samodzielnie lub w rozwiązaniu zintegrowanym.

## <a name="design-considerations-overview"></a>Omówienie zagadnień dotyczących projektowania
Ten dokument zawiera zestaw kroków i zadań, które można wykonać w celu projektowania rozwiązania tożsamości hybrydowej, która najlepiej spełnia Twoje wymagania. Kroki są prezentowane w uporządkowanej kolejności. Zagadnienia dotyczące projektowania, których można dowiedzieć się w dalszych krokach, mogą wymagać konieczność zmiany decyzji podjętych we wcześniejszych krokach, jednak z powodu sprzecznych decyzji projektowych. Zostanie podjęta próba każdy alert potencjalnymi konfliktami projektowymi dokumentu. 

Zostanie wyświetlony projekt najlepiej spełniający wymagania dopiero po wykonaniu kroków tyle razy, ile trzeba, aby uwzględnieniu wszystkich zagadnień opisane w dokumencie. 

| Faza tożsamości hybrydowej | Na liście tematów |
| --- | --- |
| Określanie wymagań dotyczących tożsamości |[Określanie potrzeb biznesowych](plan-hybrid-identity-design-considerations-business-needs.md)<br> [Określić wymagania dotyczące synchronizacji katalogu](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Określanie wymagań dotyczących uwierzytelniania wieloskładnikowego](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definiowanie strategii wdrażania tożsamości hybrydowej](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Planowanie wzmocnienia zabezpieczeń danych za pośrednictwem rozwiązania silna tożsamość |[Określanie wymagań dotyczących ochrony danych](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Określanie wymagań dotyczących zarządzania zawartością](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Określić wymagania dotyczące kontroli dostępu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Określanie wymagań dotyczących odpowiedzi na zdarzenia](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definiowanie strategii ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Planowanie cyklu życia tożsamości hybrydowej |[Określić zadania związane z zarządzaniem tożsamości hybrydowej](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Zarządzanie synchronizacji](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Określenia strategii wdrażania zarządzania tożsamości hybrydowej](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>Następne kroki
[Określić wymagania dotyczące tożsamości](plan-hybrid-identity-design-considerations-business-needs.md)

