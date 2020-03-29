---
title: Zasoby do migracji aplikacji do usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Zasoby ułatwiające migrację dostępu do aplikacji i uwierzytelniania do usługi Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: mimart
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: b30469858a5dd83f7f5f707f74466302b3000510
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968728"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Zasoby do migrowania aplikacji do usługi Azure Active Directory

Zasoby ułatwiające migrację dostępu do aplikacji i uwierzytelniania do usługi Azure Active Directory (Azure AD). Skorzystaj z tejhttps://aka.ms/AppsMigrationFeedback) krótkiej ankiety ( aby przekazać opinię na temat środowiska migracji aplikacji do usługi Azure AD (w tym blokerów do migracji, potrzeba narzędzi / wskazówek lub powodów zachowania lokalnego dpd). 

| Zasób  | Opis  |
|:-----------|:-------------|
|[Migrowanie aplikacji do usługi Azure AD](https://aka.ms/migrateapps/whitepaper) | W tym opracowaniu tym opracowaniu przedstawiono korzyści płynące z migracji i opisano sposób planowania migracji w czterech wyraźnie opisanych fazach: odnajdowanie, klasyfikacja, migracja i bieżące zarządzanie. Będziesz prowadzony przez to, jak myśleć o procesie i rozbić swój projekt na łatwe do spożycia kawałki. W całym dokumencie znajdują się łącza do ważnych zasobów, które pomogą Ci po drodze. |
|[Przewodnik po rozwiązaniach: Migrowanie aplikacji z usług federacyjnych Active Directory (AD FS) do usługi Azure AD](https://aka.ms/migrateapps/adfssolutionguide) | Ten przewodnik rozwiązania przeprowadzi Cię przez te same cztery fazy planowania i wykonywania projektu migracji aplikacji opisane na wyższym poziomie w białej księdze migracji. W tym przewodniku dowiesz się, jak zastosować te fazy do określonego celu przenoszenia aplikacji z usług Federacyjnych usługi usługi Azure Directory (AD FS) do usługi Azure AD.|
| [Narzędzie: Skrypt gotowości do migracji usług federacyjnych Active Directory](https://aka.ms/migrateapps/adfstools) | Jest to skrypt, który można uruchomić na lokalnym serwerze usług federacyjnych Active Directory (AD FS), aby określić gotowość aplikacji do migracji do usługi Azure AD.|
| [Plan wdrożenia: migracja z usług AD FS do synchronizacji skrótów haseł](https://aka.ms/ADFSTOPHSDPDownload) | Z synchronizacją skrótów haseł skróty haseł użytkowników są synchronizowane z lokalnej usługi Active Directory do usługi Azure AD. Dzięki temu usługa Azure AD może uwierzytelniać użytkowników bez interakcji z lokalną usługą Active Directory.| 
| [Plan wdrożenia: migracja z usług AD FS do uwierzytelniania przekazywanego](https://aka.ms/ADFSTOPTADPDownload)|Uwierzytelnianie przekazywane usługi Azure AD ułatwia użytkownikom logowanie się do aplikacji lokalnych i opartych na chmurze przy użyciu tego samego hasła. Ta funkcja zapewnia użytkownikom lepsze środowisko, ponieważ mają jedno hasło mniej do zapamiętania. Zmniejsza to również koszty pomocy technicznej it, ponieważ użytkownicy są mniej prawdopodobne, aby zapomnieć, jak się zalogować, gdy trzeba tylko zapamiętać jedno hasło. Gdy użytkownicy logują się za pomocą usługi Azure AD, ta funkcja weryfikuje ich hasła bezpośrednio w lokalnej usłudze Active Directory.|
| [Plan wdrażania: włączanie logowania jednokrotnego w aplikacji SaaS za pomocą usługi Azure AD](https://aka.ms/SSODPDownload) | Logowanie jednokrotne ułatwia dostęp do wszystkich aplikacji i zasobów potrzebnych do prowadzenia działalności gospodarczej, logując się tylko raz, przy użyciu jednego konta użytkownika. Na przykład po zalogowaniu się użytkownika użytkownik może przejść z pakietu Microsoft Office do SalesForce do Box bez uwierzytelniania (na przykład wpisywania hasła) po raz drugi. 
| [Plan wdrażania: rozszerzanie aplikacji na usługę Azure AD za pomocą serwera proxy aplikacji](https://aka.ms/AppProxyDPDownload)| Zapewnienie dostępu z laptopów pracowniczych i innych urządzeń do aplikacji lokalnych tradycyjnie obejmowało wirtualne sieci prywatne (VPN) lub strefy zdemilitaryzowane (DMZ). Te rozwiązania nie tylko są skomplikowane i trudne do zabezpieczenia, ale też mają wysokie koszty konfigurowania i zarządzania. Usługa Azure AD Application Proxy ułatwia dostęp do aplikacji lokalnych. |
| [Plany wdrażania](../fundamentals/active-directory-deployment-plans.md) | Znajdź więcej planów wdrażania do wdrażania funkcji, takich jak uwierzytelnianie wieloskładnikowe, dostęp warunkowy, inicjowanie obsługi administracyjnej użytkowników, bezproblemowe logowanie jednokrotne, samoobsługowe resetowanie hasła i wiele więcej! |


