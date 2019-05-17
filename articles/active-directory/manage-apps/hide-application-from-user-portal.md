---
title: Ukrywanie aplikacji w środowisku użytkownika usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak ukrywać aplikacji za pomocą środowiska użytkownika w panelach dostępu usługi Azure Active Directory lub przyciski Uruchom usługi Office 365.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dd98aa974f2adcd363c04c10b7a10cef6ca8ce7
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824535"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Ukryj aplikacje od użytkowników końcowych w usłudze Azure Active Directory

Instrukcje dotyczące sposobu ukryć MyApps panel użytkowników końcowych i usługi Office 365 w obszarze uruchamiania aplikacji. Gdy aplikacja jest ukryty, użytkownicy nadal mają uprawnienia do aplikacji. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukryć aplikacji za pomocą panelu MyApps i uruchamiania usługi Office 365 są wymagane uprawnienia administratora aplikacji.

Aby ukryć wszystkie aplikacje usługi Office 365, wymagane są uprawnienia administratora globalnego.


## <a name="hide-an-application-from-the-end-user"></a>Ukrywanie aplikacji użytkownika końcowego
Wykonaj następujące kroki, aby ukryć aplikacji za pomocą panelu MyApps i uruchamiania aplikacji usługi Office 365.

1.  Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny dla katalogu.
2.  Wybierz pozycję **Azure Active Directory**.
3.  Wybierz **aplikacje dla przedsiębiorstw**. **Aplikacje w przedsiębiorstwie — wszystkie aplikacje** zostanie otwarty blok.
4.  W obszarze **typ aplikacji**, wybierz opcję **aplikacje dla przedsiębiorstw**, jeśli nie została jeszcze wybrana.
5.  Wyszukaj aplikację, którą chcesz ukryć, a następnie kliknij żądaną aplikację.  Omówienie aplikacji zostanie otwarta.
6.  Kliknij pozycję **Właściwości**. 
7.  Aby uzyskać **widoczne dla użytkowników?** zapytania, kliknij przycisk **nie**.
8.  Kliknij pozycję **Zapisz**.


## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Ukryj aplikacje usługi Office 365 z poziomu panelu MyApps

Wykonaj następujące kroki, aby ukryć wszystkie aplikacje usługi Office 365 z poziomu panelu MyApps. Aplikacje są nadal widoczne w portalu usługi Office 365.

1.  Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny dla katalogu.
2.  Wybierz pozycję **Azure Active Directory**.
3.  Wybierz **ustawienia użytkownika**.
4.  W obszarze **aplikacje dla przedsiębiorstw**, kliknij przycisk **zarządzania, jak użytkownicy końcowi uruchamiania i wyświetlania ich aplikacji.**
5.  Aby uzyskać **użytkownicy widzą tylko aplikacje usługi Office 365 w portalu usługi Office 365**, kliknij przycisk **tak**.
6.  Kliknij pozycję **Zapisz**.


## <a name="next-steps"></a>Kolejne kroki
* [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](assign-user-or-group-access-portal.md)
* [Usuń przypisanie użytkownika lub grupy z aplikacji przedsiębiorstwa](remove-user-or-group-access-portal.md)
* [Zmiana nazwy lub logo aplikacji przedsiębiorstwa](change-name-or-logo-portal.md)

