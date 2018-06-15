---
title: Uaktualnij do serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Wybierz, jakie rozwiązanie do serwera proxy jest najlepsze w przypadku uaktualniania z programu Microsoft Forefront lub Unified bramy dostępu.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: cf1c39d0270dcb7de3ae8ff487b21874b23db74a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161959"
---
# <a name="compare-remote-access-solutions"></a>Porównanie rozwiązań dostępu zdalnego

Azure Active Directory serwera Proxy aplikacji jest jednym z dwóch rozwiązań dostępu zdalnego, które firma Microsoft oferuje. Druga to Proxy aplikacji sieci Web, wersji lokalnej. Te dwa rozwiązania Zastąp starsze produkty, które oferowane przez firmy Microsoft: Microsoft Forefront Threat Management bramy (TMG) i Unified Access Gateway (UAG). Użyj w tym artykule, aby zrozumieć, jak te rozwiązania cztery porównać ze sobą. Dla osób, które nadal przy użyciu przestarzałe rozwiązań TMG lub UAG należy użyć w tym artykule do planowania migracji do jednego serwera proxy aplikacji. 


## <a name="feature-comparison"></a>Porównanie funkcji

Użyj tej tabeli, aby zrozumieć, jak Threat Management Gateway (TMG), Unified Access Gateway (UAG), serwer Proxy aplikacji sieci Web (WAP) i serwera Proxy aplikacji usługi Azure AD (AP) porównać ze sobą.

| Cecha | TMG | UAG | WAP | Interfejs API |
| ------- | --- | --- | --- | --- |
| Uwierzytelnianie certyfikatu | Yes | Yes | - | - |
| Selektywnie publikować aplikacje przeglądarki | Yes | Yes | Yes | Yes |
| Wstępnego uwierzytelniania i pojedynczego logowania jednokrotnego | Yes | Yes | Yes | Yes | 
| Warstwy 2/3 zapory | Yes | Yes | - | - |
| Funkcje serwera proxy do przodu | Yes | - | - | - |
| Funkcje sieci VPN | Yes | Yes | - | - |
| Obsługa protokołu sformatowanego | - | Yes | Tak, jeśli uruchomiony za pośrednictwem protokołu HTTP | Tak, jeśli uruchomiony za pośrednictwem protokołu HTTP lub za pośrednictwem bramy usług pulpitu zdalnego |
| Służy jako serwer proxy usług AD FS | - | Yes | Yes | - |
| Jednego portalu dostępu do aplikacji | - | Yes | - | Yes |
| Tłumaczenie łącze treści odpowiedzi | Yes | Yes | - | Yes | 
| Uwierzytelnianie za pomocą nagłówków | - | Yes | - | Tak, z PingAccess | 
| Zabezpieczenia w skali chmury | - | - | - | Yes | 
| Dostęp warunkowy | - | Yes | - | Yes |
| Brak składników w sieci obwodowej | - | - | - | Yes |
| Nie połączenia przychodzące | - | - | - | Yes |

W przypadku większości scenariuszy zalecamy aplikacji usługi Azure AD jako nowoczesnego rozwiązania. Serwer Proxy aplikacji sieci Web jest tylko preferowane w scenariuszach wymagających serwera proxy usług AD FS i nie można używać niestandardowych domen w usłudze Azure Active Directory. 

Serwer Proxy aplikacji usługi Azure AD oferują unikatowych zalet w porównaniu do podobnych produktów, w tym:

- Rozszerzenie usługi Azure AD z lokalnymi zasobami
   - Skali chmury zabezpieczenia i ochrona
   - Funkcje, takie jak dostęp warunkowy i uwierzytelniania wieloskładnikowego można łatwo włączyć
- Nie componenet w strefą zdemilitaryzowaną
- Nie połączenia przychodzące wymagane
- Panel dostępu jednego użytkownika można przejść do dla wszystkich aplikacji, włącznie z usługi Office 365, Azure AD zintegrowanych aplikacji SaaS i lokalnej sieci web aplikacji. 


## <a name="next-steps"></a>Kolejne kroki

- [Użyj aplikacji usługi Azure AD, aby zapewnić bezpieczny zdalny dostęp do aplikacji lokalnych](application-proxy.md)
- [Przejście z programu Forefront TMG i UAG do serwera Proxy aplikacji](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).
