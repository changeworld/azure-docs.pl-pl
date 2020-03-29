---
title: Zagadnienia dotyczące projektowania tożsamości hybrydowej usługi Azure Active Directory — omówienie | Dokumenty firmy Microsoft
description: Omówienie i mapę zawartości przewodnika zagadnienia dotyczące projektowania tożsamości hybrydowej
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381493"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Zagadnienia dotyczące projektowania tożsamości hybrydowej usługi Azure Active Directory
Urządzenia konsumpcyjne mnożą się w świecie korporacyjnym, a aplikacje oparte na chmurze oprogramowania jako usługi (SaaS) są łatwe do przyjęcia. W rezultacie utrzymanie kontroli nad dostępem użytkowników do aplikacji w wewnętrznych centrach danych i platformach w chmurze jest wyzwaniem.  

Rozwiązania firmy Microsoft do obsługi tożsamości obejmują zarówno funkcje lokalne, jak i chmurowe, tworząc jedną tożsamość użytkownika na potrzeby uwierzytelniania i autoryzacji w kontekście wszystkich zasobów, niezależnie od lokalizacji. Ta koncepcja jest znana jako tożsamość hybrydowa. Istnieją różne opcje projektowania i konfiguracji tożsamości hybrydowej przy użyciu rozwiązań firmy Microsoft, a w niektórych przypadkach może być trudne do określenia, która kombinacja najlepiej spełni potrzeby twojej organizacji. 

Ten przewodnik dotyczący projektowania tożsamości hybrydowych pomoże Ci zrozumieć, jak zaprojektować rozwiązanie tożsamości hybrydowej, które najlepiej odpowiada potrzebom biznesowym i technologicznym twojej organizacji.  W tym przewodniku opisano szereg kroków i zadań, które można wykonać, aby ułatwić projektowanie rozwiązania tożsamości hybrydowej, które spełnia unikatowe wymagania organizacji. W trakcie etapów i zadań przewodnik przedstawia odpowiednie technologie i opcje funkcji dostępne dla organizacji w celu spełnienia poziomu funkcjonalności i jakości usług (takich jak dostępność, skalowalność, wydajność, łatwość zarządzania i bezpieczeństwo) Wymagania. 

W szczególności zagadnienia dotyczące projektowania tożsamości hybrydowej są celem przewodnim, aby odpowiedzieć na następujące pytania: 

* Jakie pytania należy zadać i odpowiedzieć, aby uzyskać hybrydowy projekt specyficzny dla tożsamości dla technologii lub domeny problemowej, która najlepiej spełnia moje wymagania?
* Jaką sekwencję działań należy wykonać, aby zaprojektować rozwiązanie tożsamości hybrydowej dla domeny technologii lub problemu? 
* Jakie technologie tożsamości hybrydowej i opcje konfiguracji są dostępne, aby pomóc mi spełnić moje wymagania? Jakie są kompromisy między tymi opcjami, abym mógł wybrać najlepszą opcję dla mojej firmy?

## <a name="who-is-this-guide-intended-for"></a>Dla kogo jest przeznaczony ten przewodnik?
 CIO, CITO, Chief Identity Architects, Enterprise Architects i IT Architects odpowiedzialny za projektowanie hybrydowego rozwiązania tożsamości dla średnich i dużych organizacji.

## <a name="how-can-this-guide-help-you"></a>W czym może być pomocny ten przewodnik?
Ten przewodnik służy do zrozumienia, jak zaprojektować rozwiązanie tożsamości hybrydowej, który jest w stanie zintegrować system zarządzania tożsamością w chmurze z bieżącego rozwiązania tożsamości lokalnej. 

Na poniższej ilustracji przedstawiono przykład rozwiązania tożsamości hybrydowej, które umożliwia administratorom IT zintegrowanie bieżącego rozwiązania usługi Windows Server Active Directory znajdującego się lokalnie z usługą Microsoft Azure Active Directory w celu umożliwienia użytkownikom korzystania z logowania jednokrotnego ( SSO) w aplikacjach znajdujących się w chmurze i lokalnie.

![Przykład](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

Powyższa ilustracja jest przykładem rozwiązania tożsamości hybrydowej, które wykorzystuje usługi w chmurze do integracji z możliwościami lokalnymi w celu zapewnienia jednego środowiska w procesie uwierzytelniania użytkowników końcowych i ułatwienia zarządzania tymi Zasobów. Chociaż ten przykład może być typowym scenariuszem, projekt tożsamości hybrydowej każdej organizacji może być inny niż przykład zilustrowany na rysunku 1 ze względu na różne wymagania. 

Ten przewodnik zawiera szereg kroków i zadań, które można wykonać, aby zaprojektować rozwiązanie tożsamości hybrydowej, które spełnia unikatowe wymagania organizacji. W trakcie wykonywania poniższych kroków i zadań przewodnik przedstawia odpowiednie technologie i opcje funkcji dostępne w celu spełnienia wymagań dotyczących poziomu jakości funkcjonalności i jakości usług w organizacji.

**Założenia:** Masz pewne doświadczenie z systemem Windows Server, Usługami domenowymi Active Directory i usługą Azure Active Directory. W tym dokumencie zakłada się, że szukasz, w jaki sposób te rozwiązania mogą zaspokoić Twoje potrzeby biznesowe samodzielnie lub w zintegrowanym rozwiązaniu.

## <a name="design-considerations-overview"></a>Omówienie zagadnień projektowych
Ten dokument zawiera zestaw kroków i zadań, które można wykonać, aby zaprojektować rozwiązanie tożsamości hybrydowej, które najlepiej spełnia Twoje wymagania. Kroki są prezentowane w uporządkowanej kolejności. Zagadnienia projektowe, których nauczysz się w późniejszych krokach, mogą wymagać zmiany decyzji podjętych we wcześniejszych krokach, jednak z powodu sprzecznych wyborów projektowych. Każda próba jest podejmowana, aby powiadomić o potencjalnych konfliktów projektu w całym dokumencie. 

Dojdziesz do projektu, który najlepiej spełnia twoje wymagania dopiero po iteracji przez kroki tyle razy, ile jest to konieczne, aby uwzględnić wszystkie zagadnienia w dokumencie. 

| Faza tożsamości hybrydowej | Lista tematów |
| --- | --- |
| Określanie wymagań dotyczących tożsamości |[Określanie potrzeb biznesowych](plan-hybrid-identity-design-considerations-business-needs.md)<br> [Określanie wymagań dotyczących synchronizacji katalogów](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Określanie wymagań dotyczących uwierzytelniania wieloskładnikowego](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definiowanie strategii wdrażania tożsamości hybrydowej](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Planowanie zwiększenia bezpieczeństwa danych dzięki silnemu rozwiązaniu tożsamości |[Określanie wymogów ochrony danych](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Określanie wymagań dotyczących zarządzania zawartością](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Określanie wymagań dotyczących kontroli dostępu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Określanie wymagań dotyczących odpowiadania na zdarzenia](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definiowanie strategii ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Planowanie cyklu życia tożsamości hybrydowej |[Określanie zadań zarządzania tożsamościami hybrydowymi](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Zarządzanie synchronizacją](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Określanie strategii wdrażania zarządzania tożsamościami hybrydowymi](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>Następne kroki
[Określanie wymagań dotyczących tożsamości](plan-hybrid-identity-design-considerations-business-needs.md)

