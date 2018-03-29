---
title: Ukryj aplikację za pomocą środowiska użytkownika w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak ukryć aplikację za pomocą środowiska użytkownika w paneli dostępu do usługi Azure Active Directory lub przyciski Uruchom usługi Office 365.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2018
ms.author: markvi
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 7da8c60feed7cbb630e5b48653c657cc8fed9e99
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>Ukryj aplikację za pomocą środowiska użytkownika w usłudze Azure Active Directory

Jeśli masz aplikację, której nie można wyświetlić na paneli dostępu użytkowników lub przyciski Uruchom usługi Office 365, dostępne są opcje do ukrywania tego kafelka aplikacji.  Następujące dwie opcje są dostępne ukrywania aplikacji z Przyciski Uruchom aplikacji użytkownika.

- Ukryj aplikacji innych firm, panele dostępu użytkowników i przyciski Uruchom aplikację usługi Office 365
- Ukryj wszystkie aplikacje usługi Office 365 z paneli dostępu użytkowników

Ukrywanie użytkownicy aplikacji nadal mieć uprawnienia do aplikacji lecz nie będzie mógł przeglądać je na ich przyciski Uruchom aplikację. Musi mieć odpowiednie uprawnienia do zarządzania aplikacjami przedsiębiorstwa, a musi być administratorem globalnym katalogu.


## <a name="hiding-an-application-from-users-end-user-experiences"></a>Ukrywanie aplikacji za pomocą środowiska użytkownika końcowego użytkownika
Czynności, w zależności od sytuacji, umożliwia ukrywanie aplikacji w panelu dostępu.

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>Jak ukryć aplikacji innych firm, z panelu dostępu użytkownika i przyciski Uruchom aplikację usługi Office 365?
Wykonaj następujące kroki, aby ukryć aplikację za pomocą panelu dostępu użytkownika i przyciski Uruchom aplikację usługi Office 365.

1.  Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2.  Wybierz **wszystkie usługi**, wprowadź **usługi Azure Active Directory** w polu tekstowym, a następnie wybierz **Enter**.
3.  Na **usługi Azure Active Directory - *directoryname***  ekranu (to znaczy usługi Azure AD ekranu dla katalogu są używane do zarządzania), wybierz **aplikacje dla przedsiębiorstw**.
![Aplikacje przedsiębiorstwa](media/active-directory-coreapps-hide-third-party-app/app1.png)
4.  Na **aplikacje dla przedsiębiorstw** ekranu wybierz **wszystkie aplikacje**. Możesz wyświetlić listę aplikacji, którymi można zarządzać.
5.  Na **aplikacje przedsiębiorstwa — wszystkie aplikacje** ekranu, wybierz aplikację.</br>
![Aplikacje przedsiębiorstwa](media/active-directory-coreapps-hide-third-party-app/app2.png)
6.  Na ***appname*** ekranu (czyli ekranu o nazwie wybranej aplikacji w tytule), wybierz polecenie Właściwości.
7.  Na  ***appname* -właściwości** ekranu wybierz **tak** dla **widoczny dla użytkowników?**.
![Aplikacje przedsiębiorstwa](media/active-directory-coreapps-hide-third-party-app/app3.png)
8.  Wybierz **zapisać** polecenia.

### <a name="how-do-i-hide-office-365-applications-from-users-access-panel"></a>Jak ukryć aplikacji usługi Office 365 z panelu dostępu użytkownika?

Wykonaj następujące kroki, aby ukryć wszystkie aplikacje usługi Office 365 w panelu dostępu. Te aplikacje nadal będą widoczne w portalu usługi Office 365.

1.  Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2.  Wybierz **wszystkie usługi**, wprowadź **usługi Azure Active Directory** w polu tekstowym, a następnie wybierz **Enter**.
3.  Na **usługi Azure Active Directory - *directoryname***  ekranu (to znaczy usługi Azure AD ekranu dla katalogu są używane do zarządzania), wybierz **ustawienia użytkownika**.
4.  Na **ustawienia użytkownika** ekranu, w obszarze **aplikacje dla przedsiębiorstw** wybierz **tak** dla **użytkownicy widzą tylko aplikacje usługi Office 365 w portalu usługi Office 365**.

![Aplikacje przedsiębiorstwa](media/active-directory-coreapps-hide-third-party-app/apps4.png)

## <a name="next-steps"></a>Kolejne kroki
* [Zobacz wszystkie moje grupy](active-directory-groups-view-azure-portal.md)
* [Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](active-directory-coreapps-assign-user-azure-portal.md)
* [Usuń przypisanie użytkownika lub grupy z aplikacjami](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Zmiana nazwy lub logo aplikacji przedsiębiorstwa](active-directory-coreapps-change-app-logo-user-azure-portal.md)

