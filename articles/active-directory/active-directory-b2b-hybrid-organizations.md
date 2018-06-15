---
title: Współpraca B2B dla organizacji hybrydowego — Azure Active Directory | Dokumentacja firmy Microsoft
description: Udostępnij partnerów lokalnie i w chmurze zasobów o współpracy B2B usługi Azure AD.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 943ccadbc87cd8d2345078405e2a27930634668e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33928100"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Azure współpracy B2B usługi Active Directory dla organizacji hybrydowego

Azure współpracy B2B usługi Active Directory (Azure AD) ułatwia zapewniają dostęp do aplikacji i zasobów w organizacji z partnerami zewnętrznymi. Dotyczy to nawet w konfiguracji hybrydowego którym znajduje się zarówno lokalnie, jak i zasobów w chmurze. Nie ma znaczenia, jeśli obecnie zarządzasz kont partner zewnętrznej lokalnie w systemie lokalnym tożsamości lub jeśli zarządzasz kont zewnętrznych w chmurze jako użytkowników B2B usługi Azure AD. Można teraz przyznać tym użytkownikom dostęp do zasobów w dowolnej lokalizacji przy użyciu tych samych poświadczeń logowania dla obu środowiskach.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Użytkownicy GRANT B2B w usłudze Azure AD dostęp do aplikacji lokalnych

Jeśli Twoja organizacja korzysta z możliwości współpracy B2B usługi Azure AD z zaproszeniem dla gości z organizacji partnera do usługi Azure AD, możesz podać teraz te B2B użytkownikom dostępu do aplikacji lokalnych.

W przypadku aplikacji korzystających z uwierzytelniania opartego na SAML udostępnieniem tych aplikacji użytkownikom B2B za pośrednictwem portalu Azure do uwierzytelniania przy użyciu serwera Proxy aplikacji usługi Azure AD.

W przypadku aplikacji używających zintegrowanego uwierzytelniania systemu Windows (IWA) z ograniczonego delegowania protokołu Kerberos (KCD) również użyć do uwierzytelniania serwera Proxy usługi Azure AD. Jednak do autoryzacji do pracy, obiekt użytkownika jest wymagany w lokalnej Windows Server Active Directory. Istnieją dwie metody, w którym można tworzyć obiektów użytkownika lokalnego, które reprezentują użytkownika gościa B2B.

- Można użyć programu Microsoft Identity Manager (MIM) 2016 z dodatkiem SP1 i agenta zarządzania programu MIM dla programu Microsoft Graph.
- Możesz użyć skryptu programu PowerShell. (To rozwiązanie nie wymaga MIM).

Aby uzyskać więcej informacji dotyczących sposobu wdrażania tych rozwiązań, zobacz [użytkowników Grant B2B w usłudze Azure AD dostęp do aplikacji lokalnych](active-directory-b2b-hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Udziel zarządzane lokalnie partnera kont dostępu do zasobów w chmurze

Przed usługi Azure AD organizacje z lokalnych systemów tożsamości tradycyjnie udało partnera kont w ich katalogu lokalnego. Jeśli organizacja ma upewnij się, że partnerów nadal mieć dostęp podczas przenoszenia aplikacji i innych zasobów w chmurze. Najlepiej, jeśli chcesz tych użytkowników, aby użyć tego samego zestawu poświadczeń dostępu do zasobów w chmurze, jak i dla lokalnego. 

Mamy teraz oferta metody, których można użyć Azure AD Connect do synchronizowania tych kont lokalnych do chmury "gości", gdzie kont zachowują się podobnie, takich jak użytkownicy B2B usługi Azure AD.

Aby chronić dane firmy, można kontrolować dostęp do odpowiednich zasobów i konfigurowanie zasad autoryzacji, które traktować te gości inaczej niż pracowników.

Aby uzyskać szczegóły implementacji, zobacz [Grant zarządzane lokalnie partnera kont dostępu do zasobów w chmurze przy użyciu współpracy B2B usługi Azure AD](active-directory-b2b-hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Kolejne kroki

- [Użytkownicy GRANT B2B w usłudze Azure AD dostęp do aplikacji lokalnych](active-directory-b2b-hybrid-cloud-to-on-premises.md)
- [Udziel zarządzane lokalnie partnera kont dostępu do zasobów w chmurze przy użyciu współpracy B2B usługi Azure AD](active-directory-b2b-hybrid-on-premises-to-cloud.md)


