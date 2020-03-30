---
title: Współpraca B2B dla organizacji hybrydowych — Azure AD
description: Zapewnij partnerom dostęp do zasobów lokalnych i chmurowych dzięki współpracy usługi Azure AD B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 427f7ad4d6a1b9839b1197ef9f7ca15400ea0f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272469"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Współpraca b2b usługi Azure Active Directory dla organizacji hybrydowych

Współpraca b2b usługi Azure Active Directory (Azure AD) ułatwia udzielanie partnerom zewnętrznym dostępu do aplikacji i zasobów w organizacji. Dotyczy to nawet w konfiguracji hybrydowej, gdzie masz zarówno zasobów lokalnych i opartych na chmurze. Nie ma znaczenia, czy obecnie zarządzasz kontami partnerów zewnętrznych lokalnie w lokalnym systemie tożsamości, czy zarządzasz kontami zewnętrznymi w chmurze jako użytkownik usługi Azure AD B2B. Teraz można udzielić tym użytkownikom dostępu do zasobów w obu lokalizacjach, przy użyciu tych samych poświadczeń logowania dla obu środowisk.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Przyznawanie użytkownikom B2B w usłudze Azure AD dostępu do aplikacji lokalnych

Jeśli Twoja organizacja korzysta z funkcji współpracy usługi Azure AD B2B, aby zaprosić użytkowników-gości z organizacji partnerskich do usługi Azure AD, możesz teraz zapewnić tym użytkownikom B2B dostęp do aplikacji lokalnych.

W przypadku aplikacji korzystających z uwierzytelniania opartego na saml można udostępnić te aplikacje użytkownikom B2B za pośrednictwem witryny Azure portal przy użyciu serwera proxy aplikacji usługi Azure AD do uwierzytelniania.

W przypadku aplikacji korzystających ze zintegrowanego uwierzytelniania systemu Windows (IWA) z delegowaniem z ograniczeniami protokołu Kerberos (KCD) do uwierzytelniania należy również użyć serwera proxy usługi Azure AD. Jednak do autoryzacji do pracy, obiekt użytkownika jest wymagany w lokalnej usłudze Windows Server Active Directory. Istnieją dwie metody, których można użyć do tworzenia obiektów użytkownika lokalnego, które reprezentują użytkowników-gościa B2B.

- Dodatku SP1 dla programu Microsoft Identity Manager (MIM) 2016 i agenta zarządzania programem MIM dla programu Microsoft Graph.
- Można użyć skryptu programu PowerShell. (To rozwiązanie nie wymaga mim.)

Aby uzyskać szczegółowe informacje na temat wdrażania tych rozwiązań, zobacz [Udzielanie użytkownikom B2B w usłudze Azure AD dostępu do aplikacji lokalnych.](hybrid-cloud-to-on-premises.md)

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Udzielanie lokalnie zarządzanym kontom partnerów dostępu do zasobów w chmurze

Przed usługą Azure AD organizacje z lokalnymi systemami tożsamości tradycyjnie zarządzały kontami partnerów w swoim katalogu lokalnym. Jeśli jesteś taką organizacją, chcesz mieć pewność, że twoi partnerzy nadal mają dostęp podczas przenoszenia aplikacji i innych zasobów do chmury. W idealnym przypadku chcesz, aby ci użytkownicy używali tego samego zestawu poświadczeń, aby uzyskać dostęp zarówno do zasobów w chmurze, jak i lokalnych. 

Teraz oferujemy metody, w których można użyć usługi Azure AD Connect do synchronizacji tych kont lokalnych w chmurze jako "użytkownicy-goście", gdzie konta zachowują się tak samo jak użytkownicy usługi Azure AD B2B.

Aby chronić dane firmy, można kontrolować dostęp do odpowiednich zasobów i skonfigurować zasady autoryzacji, które traktują tych użytkowników-gościa inaczej niż twoich pracowników.

Aby uzyskać szczegółowe informacje na temat implementacji, zobacz [Udzielanie dostępu do zasobów w chmurze zarządzanych lokalnie kontach partnerów przy użyciu współpracy usługi Azure AD B2B.](hybrid-on-premises-to-cloud.md)
 
## <a name="next-steps"></a>Następne kroki

- [Przyznawanie użytkownikom B2B w usłudze Azure AD dostępu do aplikacji lokalnych](hybrid-cloud-to-on-premises.md)
- [Udzielanie dostępu do zasobów w chmurze zarządzanych lokalnie przez konta partnerów przy użyciu współpracy usługi Azure AD B2B](hybrid-on-premises-to-cloud.md)


