---
title: Zasoby dotyczące migrowania aplikacji do usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Zasoby pomagające w migracji do usługi Azure Active Directory (Azure AD) dostęp do aplikacji i uwierzytelniania.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/19/2018
ms.author: celested
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92ce62d745bfb0299dc8f3eb401f5a66e38580f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440666"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Zasoby dotyczące migrowania aplikacji do usługi Azure Active Directory

Zasoby pomagające w migracji do usługi Azure Active Directory (Azure AD) dostęp do aplikacji i uwierzytelniania. Wypełnienie tej krótkiej ankiety (https://aka.ms/AppsMigrationFeedback) się opinią na temat doświadczeń migrowanie aplikacji do usługi Azure AD (w tym blockery migracji, należy dla narzędzia / wskazówki lub powody Zachowywanie lokalnego dostawcy tożsamości). 

| Resource  | Opis  |
|:-----------|:-------------|
|[Migrowanie aplikacji do usługi Azure AD](https://aka.ms/migrateapps/whitepaper) | Ten oficjalny dokument przedstawia korzyści wynikające z migracji i opisuje sposób planowania migracji w czterech fazach jasno opisane: Odnajdywanie, klasyfikacja, migracji i bieżące zarządzanie. Poprowadzą Cię kolejne kroki sposobu myślenia o procesie i podzielić projekt na łatwe gotowego do konsumpcji części. W całym dokumencie podano linki do ważnych zasobów, które pomogą Ci po drodze. |
|[Przewodnik po rozwiązaniu: Migrowanie aplikacji z usługi Active Directory Federation Services (AD FS) do usługi Azure AD](https://aka.ms/migrateapps/adfssolutionguide) | Ten przewodnik przeprowadzi Cię przez ten sam cztery fazy planowania i wykonywania opisanych na wyższym poziomie w oficjalnym dokumencie migracji projektu migracji aplikacji. W tym przewodniku dowiesz się, jak zastosować te etapy do określonego celu, przenoszenie aplikacji z usług federacyjnych Azure Directory (AD FS) do usługi Azure AD.|
| [Narzędzie: Active Directory Federation Services Migration Readiness Script](https://aka.ms/migrateapps/adfstools) | To jest skrypt można uruchomić na serwerze usługi Active Directory Federation Services (AD FS) w środowisku lokalnym w celu określenia gotowości aplikacji pod kątem migracji do usługi Azure AD.|
| [Plan wdrożenia: Migrowanie z usług AD FS do synchronizacji skrótów haseł](https://aka.ms/ADFSTOPHSDPDownload) | Z synchronizacji skrótów haseł skróty haseł użytkowników są synchronizowane z usługi Active Directory środowiska lokalnego do usługi Azure AD. Dzięki temu usługa Azure AD do uwierzytelniania użytkowników bez interakcji z usługą Active Directory w środowisku lokalnym.| 
| [Plan wdrożenia: Migrowanie z usług AD FS do uwierzytelniania przekazywanego](https://aka.ms/ADFSTOPTADPDownload)|Uwierzytelnianie przekazywane usługi Azure AD pomaga użytkownikom na logowanie się zarówno lokalnie, jak i aplikacji działających w chmurze przy użyciu tego samego hasła. Ta funkcja zapewnia użytkownikom lepsze środowisko, ponieważ mają one mniej jedno hasło do zapamiętania. Zmniejsza to także koszty pomocy technicznej IT, ponieważ użytkownicy są mniej prawdopodobne, należy zapominać, jak mają logować się, gdy potrzebują do zapamiętania hasło. Gdy użytkownicy logują się za pomocą usługi Azure AD, ta funkcja weryfikuje ich hasła bezpośrednio w lokalnej usłudze Active Directory.|
| [Plan wdrożenia: Włączanie logowania jednokrotnego do aplikacji SaaS z usługą Azure AD](https://aka.ms/SSODPDownload) | Pojedynczego logowania jednokrotnego (SSO) ułatwia dostęp wszystkie aplikacje i zasoby niezbędne do prowadzenia firmy, podczas logowania się tylko raz, za pomocą jednego konta użytkownika. Na przykład po zalogowaniu się użytkownika, użytkownik może przechodzić z Microsoft Office, do usługi SalesForce do usługi Box bez uwierzytelniania (na przykład wpisanie hasła) po raz drugi. 
| [Plan wdrożenia: Rozszerzanie aplikacji do usługi Azure AD przy użyciu serwera Proxy aplikacji](https://aka.ms/AppProxyDPDownload)| Dostęp z komputerów przenośnych pracowników i innych urządzeń do lokalnych aplikacji został użyty tradycyjnie, wirtualnych sieci prywatnych (VPN) lub stref zdemilitaryzowaną (stref DMZ). Te rozwiązania nie tylko są skomplikowane i trudne do zabezpieczenia, ale też mają wysokie koszty konfigurowania i zarządzania. Serwer Proxy aplikacji usługi Azure AD ułatwia dostęp do aplikacji lokalnych. |
| [Plany wdrożenia](../fundamentals/active-directory-deployment-plans.md) | Znajdź więcej planów wdrożenia dotyczące wdrażania funkcji, takich jak uwierzytelnianie wieloskładnikowe, dostęp warunkowy, aprowizacji użytkowników, bezproblemowego logowania jednokrotnego, samoobsługowego resetowania haseł i więcej! |


