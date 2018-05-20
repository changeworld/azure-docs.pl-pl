---
title: Udostępnianie zewnętrzne usługi Office 365 i Azure Active Directory B2B współpracy | Dokumentacja firmy Microsoft
description: Omówienie udostępniania zasobów z partnerami zewnętrznymi za pomocą usługi Office 365 i Azure Active Directory B2B współpracy.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/24/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: d55d587022b763a6890c098dd0a1b9bef2a3b7fb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Udostępnianie zewnętrzne usługi Office 365 i Azure Active Directory B2B współpracy

Zewnętrzne udostępnianie w usłudze Office 365 (OneDrive, SharePoint Online, Unified grup itp.) i współpracy B2B usługi Azure Active Directory (Azure AD) są pod względem technicznym to samo. Udostępnianie zewnętrzne wszystkich (oprócz OneDrive/usługi SharePoint Online), łącznie z gośćmi grupy usługi Office 365, używa już zaproszenia współpracy B2B usługi Azure AD interfejsów API do udostępniania.

OneDrive/usługi SharePoint Online ma menedżera oddzielne zaproszenia. Obsługa udostępnianie zewnętrzne w usłudze OneDrive/SharePoint Online zostało uruchomione przed jego obsługę opracowany usługi Azure AD. Wraz z upływem czasu OneDrive/usługi SharePoint Online udostępnianie zewnętrzne uzyskano kilka funkcji i milionów użytkowników, którzy korzystają z produktu obiektu w zbudowany udostępnianie wzorzec. Istnieją pewne niewielkie różnice między jak OneDrive/usługi SharePoint Online udostępnianie zewnętrzne działa i jak działa współpracy B2B usługi Azure AD:

- OneDrive/usługi SharePoint Online dodaje użytkowników do katalogu po użytkowników zostały zrealizowane zaproszenia. Dlatego przed realizacji, nie widzisz użytkownika w portalu usługi Azure AD. Jeśli inna witryna zaprasza użytkownika w tym samym czasie, generowany jest nowe zaproszenie. Użycie współpracy B2B usługi Azure AD, użytkownicy zostaną jednak dodane bezpośrednio na zaproszenia, tak aby pokazywały się wszędzie.

- Środowisko realizacji OneDrive/usługi SharePoint Online wygląda inaczej niż środowisko współpracy B2B usługi Azure AD. Po użytkownika umarza zaproszenia, procesy wyglądają podobnie.

- Azure AD B2B współpracy zaproszonych użytkowników mogą być pobierane z usługi OneDrive/usługi SharePoint Online udostępnianie okien dialogowych. OneDrive/SharePoint Online zaproszonych użytkowników również wyświetlane w usłudze Azure AD po ich zrealizować zaproszenia.

- Aby zarządzać udostępnianie zewnętrzne w usłudze OneDrive/SharePoint Online z współpracy B2B usługi Azure AD, ustaw OneDrive/SharePoint Online zewnętrznego udostępnianie ustawienie, aby **Zezwalaj tylko na udostępniania użytkownikom zewnętrznym już w katalogu**. Użytkownicy mogą przejdź do witryny zewnętrznie udostępnione i wybierz z zewnętrznym współpracownikom, dodane przez administratora. Administrator może dodać zewnętrznych współpracowników za pośrednictwem interfejsów API zaproszenia współpracy B2B.

![OneDrive/SharePoint Online zewnętrznego współużytkuje](media/o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>Kolejne kroki

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Dodawanie do roli użytkownika współpracy B2B](add-guest-to-role.md)
* [Delegowanie zaproszenia współpracy B2B](delegate-invitations.md)
* [Grupami dynamicznymi i współpracy B2B](use-dynamic-groups.md)