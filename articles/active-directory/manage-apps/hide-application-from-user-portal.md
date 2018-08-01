---
title: Ukrywanie aplikacji w środowisku użytkownika usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak ukrywać aplikacji za pomocą środowiska użytkownika w panelach dostępu usługi Azure Active Directory lub przyciski Uruchom usługi Office 365.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 55f80396df4cbfe7d0a16a6a5066b68aadc0bdd3
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369345"
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>Ukrywanie aplikacji w środowisku użytkownika usługi Azure Active Directory

Jeśli masz aplikację, która nie chcesz pokazać w panelach dostępu użytkowników lub przyciski Uruchom usługi Office 365, dostępne są opcje ukryć ten Kafelek aplikacji.  Następujące dwie opcje są dostępne ukrywania aplikacji z Przyciski Uruchom aplikacji przez użytkownika.

- Ukrywanie aplikacji innych firm w panelach dostępu użytkowników i przyciski Uruchom aplikację usługi Office 365
- Ukryj wszystkie aplikacje usługi Office 365 w panelach dostępu użytkowników

Ukrywanie użytkowników aplikacji nadal mają uprawnienia do aplikacji lecz nie będą widoczne będą pojawiać się na ich przyciski Uruchom aplikację. Musi mieć odpowiednie uprawnienia do zarządzania aplikacji przedsiębiorstwa, a musi być administratorem globalnym katalogu.


## <a name="hiding-an-application-from-users-end-user-experiences"></a>Ukrywanie aplikacji za pomocą środowiska użytkownika końcowego użytkownika
Kroki opisane poniżej, w zależności od sytuacji umożliwia Ukryj aplikacje przy użyciu panelu dostępu.

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>Jak ukrywanie aplikacji innych firm, z panelu dostępu użytkownika i przyciski Uruchom aplikację usługi Office 365?
Wykonaj następujące kroki, aby ukryć aplikacji za pomocą panelu dostępu i przyciski Uruchom aplikację usługi Office 365 przez użytkownika.

1.  Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2.  Wybierz **wszystkich usług**, wprowadź **usługi Azure Active Directory** w polu tekstowym, a następnie wybierz pozycję **Enter**.
3.  Na **usługi Azure Active Directory — *directoryname***  ekranu (oznacza to, że usługi Azure AD ekranu dla katalogu zarządzania) wybierz **aplikacje dla przedsiębiorstw**.
![Aplikacje w przedsiębiorstwie](./media/hide-application-from-user-portal/app1.png)
4.  Na **aplikacje dla przedsiębiorstw** ekranu, wybierz opcję **wszystkie aplikacje**. Zobaczysz listę aplikacji, którymi można zarządzać.
5.  Na **aplikacje w przedsiębiorstwie — wszystkie aplikacje** ekranu, wybierz aplikację.</br>
![Aplikacje w przedsiębiorstwie](./media/hide-application-from-user-portal/app2.png)
6.  Na ***appname*** ekranu (czyli ekranu o nazwie wybranej aplikacji w tytule), wybierz polecenie Właściwości.
7.  Na  ***appname* — właściwości** ekranu, wybierz opcję **tak** dla **widoczne dla użytkowników?**.
![Aplikacje w przedsiębiorstwie](./media/hide-application-from-user-portal/app3.png)
8.  Wybierz **Zapisz** polecenia.

### <a name="how-do-i-hide-office-365-applications-from-users-access-panel"></a>Jak ukryć aplikacjami usługi Office 365 z poziomu panelu dostępu użytkownika?

Wykonaj następujące kroki, aby ukryć wszystkie aplikacje usługi Office 365 z poziomu panelu dostępu. Te aplikacje nadal będą widoczne w portalu usługi Office 365.

1.  Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2.  Wybierz **wszystkich usług**, wprowadź **usługi Azure Active Directory** w polu tekstowym, a następnie wybierz pozycję **Enter**.
3.  Na **usługi Azure Active Directory — *directoryname***  ekranu (oznacza to, że usługi Azure AD ekranu dla katalogu zarządzania) wybierz **ustawienia użytkownika**.
4.  Na **ustawienia użytkownika** ekranu, w obszarze **aplikacje dla przedsiębiorstw** wybierz **tak** dla **użytkownicy widzą tylko aplikacje usługi Office 365 w portalu usługi Office 365**.

![Aplikacje dla przedsiębiorstw](./media/hide-application-from-user-portal/apps4.png)

## <a name="next-steps"></a>Kolejne kroki
* [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](assign-user-or-group-access-portal.md)
* [Usuń przypisanie użytkownika lub grupy z aplikacji przedsiębiorstwa](remove-user-or-group-access-portal.md)
* [Zmiana nazwy lub logo aplikacji przedsiębiorstwa](change-name-or-logo-portal.md)

