---
title: Wyłącz logowania użytkowników dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak wyłączyć aplikację przedsiębiorstwa, dzięki czemu użytkownicy nie mogą Zaloguj się do niego w usłudze Azure Active Directory
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
ms.topic: article
ms.date: 08/28/2017
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: be0c8ede4a99c532aecbfbb4ee0fc51657f96886
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Wyłącz logowania użytkowników dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory
To proste wyłączyć aplikację przedsiębiorstwa, dzięki czemu użytkownicy nie mogą Zaloguj się do niego w usłudze Azure Active Directory (Azure AD). Musi mieć odpowiednie uprawnienia do zarządzania aplikacjami przedsiębiorstwa, a musi być administratorem globalnym katalogu.

## <a name="how-do-i-disable-user-sign-ins"></a>Jak wyłączyć logowania użytkowników?
1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz **wszystkie usługi**, wprowadź **usługi Azure Active Directory** w polu tekstowym, a następnie wybierz **Enter**.
3. Na **usługi Azure Active Directory** -  ***directoryname*** okienku (to znaczy usługi Azure AD dla katalogu są używane do zarządzania), wybierz **aplikacje dla przedsiębiorstw**.

    ![Otwieranie aplikacji przedsiębiorstwa](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. Na **aplikacje dla przedsiębiorstw** okienku wybierz **wszystkie aplikacje**. Możesz wyświetlić listę aplikacji, którymi można zarządzać.
5. Na **aplikacje przedsiębiorstwa — wszystkie aplikacje** okienku, wybierz aplikację.
6. Na ***appname*** okienku (to znaczy o nazwie wybranej aplikacji w tytule), wybierz **właściwości**.

    ![Polecenie wszystkie aplikacje](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. Na ***appname*** - **właściwości** okienku wybierz **nr** dla **dostępny dla użytkowników do logowania?**.
8. Wybierz **zapisać** polecenia.

## <a name="next-steps"></a>Kolejne kroki
* [Zobacz wszystkie moje grupy](active-directory-groups-view-azure-portal.md)
* [Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](active-directory-coreapps-assign-user-azure-portal.md)
* [Usuń przypisanie użytkownika lub grupy z aplikacjami](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Zmiana nazwy lub logo aplikacji przedsiębiorstwa](active-directory-coreapps-change-app-logo-user-azure-portal.md)
