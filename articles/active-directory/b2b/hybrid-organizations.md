---
title: Współpraca B2B dla organizacji hybrydowych — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Przyznać partnerom prawa dostępu do swoich lokalnych i zasobów przy użyciu funkcji współpracy B2B usługi Azure AD w chmurze.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a95b9bc6637526ef0d975815cc88dd084360397
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60412597"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Współpracy usługi Azure Active Directory B2B dla organizacji hybrydowych

Współpracy w usłudze Azure Active Directory (Azure AD) B2B ułatwia udostępnianie zewnętrzne partnerów do aplikacji i zasobów w Twojej organizacji. Jest to wartość true, nawet w przypadku konfiguracji hybrydowych, gdzie mają zarówno lokalnych, jak i zasobów w chmurze. Nie ma znaczenia, jeśli obecnie zarządzasz kont zewnętrznych partnerów lokalnie w systemie tożsamości lokalnych lub jeśli zarządzasz konta zewnętrzne w chmurze jako użytkownicy usługi Azure AD B2B. Możesz teraz nadawać te użytkownikom dostęp do zasobów w jednej z tych lokalizacji przy użyciu tych samych poświadczeń logowania dla obu środowiskach.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Użytkowników B2B przydział w usłudze Azure AD dostęp do aplikacji w środowisku lokalnym

Jeśli Twoja organizacja korzysta z funkcji współpracy B2B usługi Azure AD można zaprosić użytkowników-gości z organizacji partnerskich do usługi Azure AD, teraz możesz podać te użytkownikom B2B dostępu do aplikacji lokalnych.

W przypadku aplikacji korzystających z uwierzytelniania opartego na SAML można udostępnić te aplikacje do użytkowników B2B w witrynie Azure portal, za pomocą serwera Proxy aplikacji usługi Azure AD do uwierzytelniania.

W przypadku aplikacji używających zintegrowanego Windows Authentication (Zintegrowane) przy użyciu ograniczonego delegowania protokołu Kerberos (KCD) możesz także użyć serwera Proxy usługi Azure AD do uwierzytelniania. Jednak na potrzeby autoryzacji do pracy obiektu użytkownika jest wymagana w Active Directory serwera systemu Windows w środowisku lokalnym. Istnieją dwie metody używane do tworzenia obiektów użytkownika lokalnego, reprezentujących użytkowników gości B2B.

- Można użyć programu Microsoft Identity Manager (MIM) 2016 z dodatkiem SP1 i agenta zarządzania programu MIM dla programu Microsoft Graph.
- Można użyć skryptu programu PowerShell. (To rozwiązanie nie wymaga programu MIM).

Aby uzyskać szczegółowe informacje o tym, jak wdrożyć te rozwiązania, zobacz [użytkowników B2B przydział w usłudze Azure AD dostęp do aplikacji w środowisku lokalnym](hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Udziel lokalnie zarządzanych przez partnera kont dostępu do zasobów w chmurze

Organizacje z lokalnymi systemami tożsamości tradycyjnie zarządzane partnera kont w jego katalogu lokalnego przed Azure AD. Jeśli organizacja ma upewnij się, że partnerów nadal mieć dostęp, jak przenieść swoje aplikacje i inne zasoby do chmury. Najlepiej, jeśli chcesz przyznać tym użytkownikom, aby użyć tego samego zestawu poświadczeń dostępu do zasobów w chmurze i lokalnych. 

Firma Microsoft teraz oferty metody, których można użyć program Azure AD Connect do synchronizowania tych kont lokalnych do chmury "użytkowników-gości", gdzie kont zachowują się tak, takich jak użytkownicy B2B usługi Azure AD.

Aby lepiej chronić dane firmowe, można kontrolować dostęp do odpowiednich zasobów i konfigurowanie zasad autoryzacji, które traktowania tych użytkowników-gości, różni się od pracowników.

Aby uzyskać szczegóły implementacji, zobacz [konta zarządzane lokalnie partnera udzielanie dostępu do zasobów w chmurze przy użyciu współpracy B2B usługi Azure AD](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Kolejne kroki

- [Użytkowników B2B przydział w usłudze Azure AD dostęp do aplikacji w środowisku lokalnym](hybrid-cloud-to-on-premises.md)
- [Udzielanie lokalnie zarządzanych przez partnera kont dostępu do zasobów w chmurze przy użyciu współpracy B2B usługi Azure AD](hybrid-on-premises-to-cloud.md)


