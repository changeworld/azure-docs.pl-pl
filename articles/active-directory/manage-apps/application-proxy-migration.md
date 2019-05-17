---
title: Przeprowadź uaktualnienie do serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Wybierz, które rozwiązanie serwera proxy jest przydatna, jeśli wykonujesz uaktualnienie z programu Microsoft Forefront lub ujednoliconej bramy dostępu.
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
ms.date: 07/27/2017
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a98a0394ff2a74aa256637d3c12007374200ef22
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783462"
---
# <a name="compare-remote-access-solutions"></a>Porównanie rozwiązań dostępu zdalnego

Usługa Azure Active Directory serwera Proxy aplikacji jest jednym z dwóch rozwiązań dostępu zdalnego, które firma Microsoft oferuje. Druga to Proxy aplikacji sieci Web, — lokalną wersją. Te dwa rozwiązania Zastąp starszych produktów, które oferowane przez firmy Microsoft: Microsoft Forefront Threat Management Gateway (TMG) i jednolity dostęp do bramy (UAG). Użyj w tym artykule, aby zrozumieć, jak te cztery rozwiązania wypadają w porównaniu do siebie nawzajem. Dla osób, które nadal przy użyciu przestarzałe rozwiązania serwera TMG lub UAG należy użyć w tym artykule do zaplanowania migracji do jednego serwera Proxy aplikacji. 


## <a name="feature-comparison"></a>Porównanie funkcji

Aby dowiedzieć się, jak Threat Management Gateway (TMG), Unified dostępu do bramy (UAG), serwer Proxy aplikacji sieci Web (WAP) i serwera Proxy aplikacji usługi Azure AD (AP) wypadają w porównaniu do siebie nawzajem, należy użyć tej tabeli.

| Cecha | TMG | UAG | WAP | Interfejs API |
| ------- | --- | --- | --- | --- |
| Uwierzytelnianie certyfikatów | Tak | Tak | - | - |
| Selektywne publikowanie aplikacji przeglądarki | Tak | Yes | Yes | Tak |
| Wstępnego uwierzytelniania i pojedynczego logowania jednokrotnego | Tak | Yes | Yes | Tak | 
| Warstwy 2/3 zapory | Tak | Tak | - | - |
| Funkcje serwera proxy do przodu | Tak | - | - | - |
| Funkcje sieci VPN | Tak | Tak | - | - |
| Obsługa protokołu zaawansowane | - | Tak | Tak, jeśli uruchomiony za pośrednictwem protokołu HTTP | Tak, jeśli uruchomiony za pośrednictwem protokołu HTTP lub za pośrednictwem bramy usług pulpitu zdalnego |
| Służy jako serwer proxy usług AD FS | - | Tak | Tak | - |
| Jednego portalu, aby uzyskać dostęp do aplikacji | - | Tak | - | Tak |
| Tłumaczenie łącze treści odpowiedzi | Tak | Yes | - | Tak | 
| Uwierzytelnianie przy użyciu nagłówków | - | Tak | - | Tak, z usługą PingAccess | 
| Zabezpieczenia w skali chmury | - | - | - | Tak | 
| Dostęp warunkowy | - | Tak | - | Tak |
| Brak składników w sieci obwodowej | - | - | - | Tak |
| Nie połączeń przychodzących | - | - | - | Tak |

W przypadku większości scenariuszy zaleca się serwera Proxy aplikacji usługi Azure AD jako nowoczesnych rozwiązań. Serwer Proxy aplikacji sieci Web jest tylko preferowane w scenariuszach, które wymagają serwera proxy usług AD FS i nie można użyć domenom niestandardowym w usłudze Azure Active Directory. 

Serwer Proxy aplikacji usługi Azure AD zapewnia unikalne korzyści w porównaniu do podobne produkty, w tym:

- Rozszerzenie usługi Azure AD do zasobów lokalnych
   - Zabezpieczenia w skali chmury i ochrona
   - Funkcje, takie jak dostęp warunkowy i uwierzytelniania wieloskładnikowego można łatwo włączyć
- Brak składników w strefą zdemilitaryzowaną
- Nie połączeń przychodzących, wymagane
- Panel dostępu jednego, użytkownicy mogą przejść do dla wszystkich swoich aplikacji, w tym usługi Office 365, Azure AD zintegrowanych aplikacji SaaS i lokalnych aplikacji sieci web. 


## <a name="next-steps"></a>Kolejne kroki

- [Użyj usługi Azure AD aplikacji, aby zapewnić bezpieczny dostęp zdalny do aplikacji lokalnych](application-proxy.md)
- [Przejście z programu Forefront TMG i UAG do serwera Proxy aplikacji](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).
