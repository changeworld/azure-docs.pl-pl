---
title: Scenariusze użycia i zagadnienia dotyczące wdrażania dla usługi Azure AD Join | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak Administratorzy mogą skonfigurować funkcję Azure AD Join dla użytkowników końcowych (pracowników, studentów, innych użytkowników). Również zawiera omówienie różnych scenariuszy rzeczywistych dotyczące korzystania z usługi Azure AD Join.
services: active-directory
documentationcenter: ''
author: femila
manager: mtillman
editor: ''
tags: azure-classic-portal
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: 173ad6f07699ca6bfa534dedc053663bba571382
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26602435"
---
# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Scenariusze użycia i zagadnienia dotyczące wdrażania dla usługi Azure AD Join
## <a name="usage-scenarios-for-azure-ad-join"></a>Scenariusze użycia dotyczące usługi Azure AD Join
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>Scenariusz 1: Firm przede wszystkim w chmurze
Azure Active Directory Join (Azure AD Join) mogą korzystać można obecnie działać i Zarządzanie tożsamościami dla Twojej firmy w chmurze lub wkrótce przechodzenia do chmury. Można użyć konta, które zostały utworzone w usłudze Azure AD do logowania do systemu Windows 10. Za pomocą [przy pierwszym uruchomieniu zgłaszać proces (FRX)](active-directory-azureadjoin-user-frx.md), lub przez przyłączenie usługi Azure AD z [menu ustawienia](active-directory-azureadjoin-user-upgrade.md), użytkownicy dołączyć do swoich urządzeń do usługi Azure AD.  Użytkownicy mogą również korzystać pojedynczego logowania jednokrotnego (SSO) dostęp do zasobów w chmurze jak usługi Office 365 w przeglądarce lub w aplikacjach pakietu Office.

### <a name="scenario-2-educational-institutions"></a>Scenariusz 2: Instytucji edukacyjnych
Instytucji edukacyjnych zwykle mają dwa typy użytkownika: pracowników i studentów. Elementy członkowskie nauczycieli lub wykładowców są traktowane jako członkowie dłuższy okres organizacji. Tworzenie lokalnych kont dla nich jest pożądane. Studenci są shorter-term członków organizacji, a ich kont mogą być zarządzane w usłudze Azure AD. Oznacza to, że katalog skali może zostać umieszczony w chmurze zamiast przechowywanego lokalnie. Oznacza to również, że studentów będzie można logować się do systemu Windows przy użyciu ich kont usługi Azure AD i uzyskać dostęp do zasobów usługi Office 365 w aplikacjach pakietu Office.

### <a name="scenario-3-retail-businesses"></a>Scenariusz 3: Detaliczne firmy
Przedsiębiorstwa handlowe mają okresach pracowników i pracowników długoterminowej. Zazwyczaj tworzone konta lokalnego i komputerów przyłączonych do domeny na użytek długoterminowego pełnoetatowym pracownikom. Jednak okresach pracownicy są shorter-term członków organizacji i jest pożądane, aby zarządzać kontami, ich gdzie licencji użytkownika można łatwo przenosić wokół. Podczas tworzenia kont użytkowników w chmurze za pomocą licencji usługi Office 365, ci użytkownicy uzyskiwać zalet logowanie do systemu Windows i Office aplikacji przy użyciu konta usługi Azure AD podczas Obsługa większą elastyczność i ich licencje po ich działania.

### <a name="scenario-4-additional-scenarios"></a>Scenariusz 4: Dodatkowe scenariusze
Wraz z korzyści już wspomniano, korzystać z konieczności użytkownikom dołączenie swojego urządzenia do usługi Azure AD z powodu uproszczone środowisko łącząca, zarządzanie urządzeniami wydajne rejestracji zarządzania automatyczne urządzeń przenośnych i logowanie jednokrotne z usługą Azure AD i zasobów lokalnych.  

## <a name="deployment-considerations-for-azure-ad-join"></a>Zagadnienia dotyczące wdrażania dla usługi Azure AD Join
### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>Umożliwianie użytkownikom Dołącz to urządzenie będące własnością firmy bezpośrednio do usługi Azure AD
Przedsiębiorstwa można skonfigurować tylko na chmurze kont firm partnerskich i organizacji. Partnerów te można następnie łatwo uzyskiwać dostęp do aplikacji firmowych i zasobów przy użyciu rejestracji jednokrotnej. W tym scenariuszu jest stosowane do użytkowników, którzy uzyskują dostęp do zasobów, przede wszystkim w chmurze, takich jak usługi Office 365 lub SaaS aplikacje korzystające z usługi Azure AD na potrzeby uwierzytelniania.

### <a name="prerequisites"></a>Wymagania wstępne
**Na poziomie przedsiębiorstwa (administrator)**

* Subskrypcja platformy Azure w usłudze Azure Active Directory  

**Na poziomie użytkownika**

* Windows 10 (wersje Professional i Enterprise)

### <a name="administrator-tasks"></a>Zadania administratora
* [Konfigurowanie rejestracji urządzeń](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Zadania użytkownika
* [Konfigurowanie nowego urządzenia systemu Windows 10 z usługą Azure AD podczas instalacji](active-directory-azureadjoin-user-frx.md)
* [Konfigurowanie urządzenia Windows 10 z usługą Azure AD z menu ustawień](active-directory-azureadjoin-user-upgrade.md)
* [Przyłączanie urządzenia osobistego systemu Windows 10 w organizacji](active-directory-azureadjoin-personal-device.md)

## <a name="enable-byod-in-your-organization-for-windows-10"></a>Włączyć model BYOD w organizacji dla systemu Windows 10
Można skonfigurować użytkowników i pracowników, aby korzystać z ich osobistych urządzeń z systemem Windows (BYOD), dostęp do aplikacji firmowych i zasobów. Użytkownicy mogą dodawać konta usługi Azure AD (konta firmowego lub szkolnego) osobiste urządzenia z systemem Windows, aby dostęp do zasobów w sposób bezpieczny i być zgodne.

### <a name="prerequisites"></a>Wymagania wstępne
**Na poziomie przedsiębiorstwa (administrator)**

* Subskrypcja usługi Azure AD

**Na poziomie użytkownika**

* Windows 10 (wersje Professional i Enterprise)

### <a name="administrator-tasks"></a>Zadania administratora
* [Konfigurowanie rejestracji urządzeń](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Zadania użytkownika
* [Przyłączanie urządzenia osobistego systemu Windows 10 w organizacji](active-directory-azureadjoin-personal-device.md)

## <a name="additional-information"></a>Dodatkowe informacje
* [System Windows 10 dla przedsiębiorstw: sposoby używania urządzenia do pracy](active-directory-azureadjoin-windows10-devices-overview.md)
* [Rozszerzanie możliwości chmury dla urządzeń z systemem Windows 10 za pomocą usługi Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Uwierzytelnianie tożsamości bez hasła przy użyciu Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Dowiedz się więcej na temat scenariuszy użycia funkcji Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Łączenie urządzeń przyłączonych do domeny z usługą Azure AD w celu korzystania z możliwości systemu Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurowanie funkcji Azure AD Join](active-directory-azureadjoin-setup.md)

