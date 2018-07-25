---
title: Scenariusze użycia i zagadnienia dotyczące wdrażania usługi Azure AD JOIN | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak Administratorzy mogą skonfigurować usługi Azure AD Join dla użytkowników końcowych (pracowników, studentów, innych użytkowników). Omawia także różnych scenariuszy w rzeczywistych warunkach dotyczące korzystania z usługi Azure AD Join.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
tags: azure-classic-portal
ms.component: devices
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.openlocfilehash: 29480390e6854dcedeaf8f06c078ed2e4ed2b94d
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223011"
---
# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Scenariusze użycia i zagadnienia dotyczące wdrażania usługi Azure AD JOIN
## <a name="usage-scenarios-for-azure-ad-join"></a>Scenariusze użycia usługi Azure AD JOIN
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>Scenariusz 1: Firmom w dużej mierze w chmurze
Usługa Azure Active Directory Join (Azure AD Join) korzyści z używania Jeśli obecnie działają i Zarządzanie tożsamościami dla Twojej firmy w chmurze lub przejście do chmury szybko. Można użyć konta, które zostały utworzone w usłudze Azure AD, aby zalogować się do systemu Windows 10. Za pomocą [pierwszego uruchomienia środowiska (FRX) proces](active-directory-azureadjoin-user-frx.md), lub przez dołączenie do usługi Azure AD z [menu ustawienia](active-directory-azureadjoin-user-upgrade.md), użytkownicy mogą dołączyć swoich urządzeń do usługi Azure AD.  Użytkowników można również oglądać pojedynczego logowania jednokrotnego (SSO) dostęp do zasobów w chmurze takich jak Office 365 w swoich przeglądarkach albo w aplikacjach pakietu Office.

### <a name="scenario-2-educational-institutions"></a>Scenariusz 2: Instytucji edukacyjnych
Instytucje edukacyjne, zazwyczaj obejmuje dwa typy użytkownika: wykładowcom i uczniom. Wykładowcy biorący są traktowane jako długoterminowych członków organizacji. Tworzenie kont lokalnych dla nich jest pożądane. Ale nowi studenci shorter-term członków organizacji i kont można zarządzać w usłudze Azure AD. Oznacza to, wypychania skalowania katalogu w chmurze, a nie są przechowywane lokalnie. Oznacza to również, że studentów będą mogli zalogować się do Windows za pomocą kont usługi Azure AD i Uzyskaj dostęp do zasobów usług Office 365 w aplikacjach pakietu Office.

### <a name="scenario-3-retail-businesses"></a>Scenariusz 3: Sprzedawców detalicznych
Sprzedawców detalicznych mają pracownikom sezonowym, jak i długoterminowe pracowników. Zazwyczaj utworzenie kont w środowisku lokalnym i komputerów przyłączonych do domeny na użytek długoterminowych pracownikom pełnoetatowym. Ale pracownikom sezonowym są shorter-term członków organizacji i jest zarządzać swoimi kontami, których licencje użytkowników można łatwiej przenosić wokół. Po utworzeniu ich kont użytkowników w chmurze za pomocą licencji usługi Office 365, użytkownicy ci korzyści logowania Windows i pakietu Office aplikacji przy użyciu konta usługi Azure AD, podczas gdy Ty masz większą elastyczność dzięki licencji po opuszczają.

### <a name="scenario-4-additional-scenarios"></a>Scenariusz 4: Dodatkowe scenariusze
Wraz z korzyści, o których wspomniano, można skorzystać z użytkowników dołączenia urządzeń do usługi Azure AD, ze względu na uproszczone środowisko przyłączany, zarządzanie urządzeniami wydajne, automatycznej rejestracji urządzeń przenośnych zarządzania i logowania jednokrotnego do usługi Azure AD i zasoby w środowisku lokalnym.  

## <a name="deployment-considerations-for-azure-ad-join"></a>Zagadnienia dotyczące wdrażania usługi Azure AD JOIN
### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>Umożliwianie użytkownikom dołączanie urządzenia należące do firmy bezpośrednio do usługi Azure AD
Przedsiębiorstwa można udostępnić konta tylko w chmurze firm partnerskich i organizacji. Ci partnerzy mogą uzyskiwać łatwo dostęp do firmowych aplikacji i zasobów przy użyciu logowania jednokrotnego. Ten scenariusz ma zastosowanie do użytkowników, którzy uzyskiwać dostęp do zasobów, przede wszystkim w chmurze, takich jak aplikacje usługi Office 365 lub infrastrukturą SaaS, korzystających z usługi Azure AD do uwierzytelniania.

### <a name="prerequisites"></a>Wymagania wstępne
**Na poziomie przedsiębiorstwa (administrator)**

* Subskrypcja platformy Azure w usłudze Azure Active Directory  

**Na poziomie użytkownika**

* Windows 10 (wersje Professional i Enterprise)

### <a name="administrator-tasks"></a>Zadania administratora
* [Konfigurowanie rejestracji urządzeń](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Zadania użytkownika
* [Konfigurowanie nowego urządzenia systemu Windows 10 z usługą Azure AD podczas instalacji](active-directory-azureadjoin-user-frx.md)
* [Konfigurowanie urządzenia systemu Windows 10 za pomocą usługi Azure AD z poziomu menu ustawień](active-directory-azureadjoin-user-upgrade.md)
* [Przyłączanie urządzenia osobistego systemu Windows 10 w organizacji](active-directory-azureadjoin-personal-device.md)

## <a name="enable-byod-in-your-organization-for-windows-10"></a>Włączanie modelu BYOD w Twojej organizacji dla systemu Windows 10
Aby korzystać z ich osobistych urządzeń Windows (BYOD), uzyskiwania dostępu do firmowych aplikacji i zasobów można skonfigurować użytkowników i pracowników. Użytkownicy mogą dodawać swoje konta usługi Azure AD (kont służbowych) do osobistych urządzeń Windows dostęp do zasobów w sposób umożliwiający bezpieczne i zgodne.

### <a name="prerequisites"></a>Wymagania wstępne
**Na poziomie przedsiębiorstwa (administrator)**

* Subskrypcja platformy Azure AD

**Na poziomie użytkownika**

* Windows 10 (wersje Professional i Enterprise)

### <a name="administrator-tasks"></a>Zadania administratora
* [Konfigurowanie rejestracji urządzeń](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Zadania użytkownika
* [Przyłączanie urządzenia osobistego systemu Windows 10 w organizacji](active-directory-azureadjoin-personal-device.md)

## <a name="additional-information"></a>Dodatkowe informacje
* [System Windows 10 dla przedsiębiorstw: sposoby używania urządzenia do pracy](active-directory-azureadjoin-windows10-devices-overview.md)
* [Rozszerzanie możliwości chmury dla urządzeń z systemem Windows 10 za pomocą usługi Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Uwierzytelnianie tożsamości bez haseł za pomocą Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Dowiedz się więcej na temat scenariuszy użycia funkcji Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Łączenie urządzeń przyłączonych do domeny z usługą Azure AD w celu korzystania z możliwości systemu Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurowanie funkcji Azure AD Join](active-directory-azureadjoin-setup.md)

