---
title: Uaktualnienie do serwera proxy aplikacji usługi Azure AD | Dokumenty firmy Microsoft
description: Wybierz rozwiązanie proxy, które jest najlepsze w przypadku uaktualniania z usługi Microsoft Forefront lub Unified Access Gateway.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4790dc7ebeeee3407e89bcf38d7e3f25699ed328
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108405"
---
# <a name="compare-remote-access-solutions"></a>Porównywanie rozwiązań dostępu zdalnego

Usługa Azure Active Directory Application Proxy to jedno z dwóch rozwiązań dostępu zdalnego, które oferuje firma Microsoft. Drugi to serwer proxy aplikacji sieci Web, wersja lokalna. Te dwa rozwiązania zastępują wcześniejsze produkty oferowane przez firmę Microsoft: Microsoft Forefront Threat Management Gateway (TMG) i Unified Access Gateway (UAG). Ten artykuł służy do zrozumienia, jak te cztery rozwiązania porównują się ze sobą. Dla tych z Was, którzy nadal korzystają z przestarzałych rozwiązań TMG lub UAG, użyj tego artykułu, aby zaplanować migrację do jednego z serwera proxy aplikacji. 


## <a name="feature-comparison"></a>Porównanie funkcji

Ta tabela służy do zrozumienia, jak brama zarządzania zagrożeniami (TMG), brama ujednoliconego dostępu (UAG), serwer proxy aplikacji sieci Web (WAP) i serwer proxy aplikacji usługi Azure AD (AP) są ze sobą porównywane.

| Funkcja | Tmg | Uag | WAP | Interfejs API |
| ------- | --- | --- | --- | --- |
| Uwierzytelnianie certyfikatu | Tak | Tak | - | - |
| Selektywne publikowanie aplikacji przeglądarki | Tak | Tak | Tak | Tak |
| Preauthentication i logowanie jednokrotne | Tak | Tak | Tak | Tak | 
| Zapora warstwy 2/3 | Tak | Tak | - | - |
| Możliwości serwera proxy do przodu | Tak | - | - | - |
| Możliwości sieci VPN | Tak | Tak | - | - |
| Obsługa bogatego protokołu | - | Tak | Tak, jeśli działa na HTTP | Tak, jeśli działa na HTTP lub za pośrednictwem bramy pulpitu zdalnego |
| Służy jako serwer proxy usługi ADFS | - | Tak | Tak | - |
| Jeden portal dostępu do aplikacji | - | Tak | - | Tak |
| Tłumaczenie łącza treści odpowiedzi | Tak | Tak | - | Tak | 
| Uwierzytelnianie za pomocą nagłówków | - | Tak | - | Tak, z PingAccess | 
| Zabezpieczenia w skali chmury | - | - | - | Tak | 
| Dostęp warunkowy | - | Tak | - | Tak |
| Brak komponentów w strefie zdemilitaryzowanej (DMZ) | - | - | - | Tak |
| Brak połączeń przychodzących | - | - | - | Tak |

W większości scenariuszy zaleca się usługi Azure AD Application Proxy jako nowoczesne rozwiązanie. Serwer proxy aplikacji sieci Web jest preferowany tylko w scenariuszach, które wymagają serwera proxy dla usług AD FS i nie można używać domen niestandardowych w usłudze Azure Active Directory. 

Usługa Azure AD Application Proxy oferuje unikatowe korzyści w porównaniu do podobnych produktów, w tym:

- Rozszerzanie usługi Azure AD na zasoby lokalne
   - Bezpieczeństwo i ochrona w skali chmury
   - Funkcje, takie jak dostęp warunkowy i uwierzytelnianie wieloskładnikowe, są łatwe do
- Brak komponentów w strefie zdemilitaryzowanej
- Nie są wymagane połączenia przychodzące
- Jeden panel dostępu, do który użytkownicy mogą przejść dla wszystkich swoich aplikacji, w tym aplikacji O365, scalonej usługi Azure AD SaaS i lokalnych aplikacji sieci Web. 


## <a name="next-steps"></a>Następne kroki

- [Korzystanie z aplikacji usługi Azure AD w celu zapewnienia bezpiecznego dostępu zdalnego do aplikacji lokalnych](application-proxy.md)
