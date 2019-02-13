---
title: Wyłączanie logowania użytkowników dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak wyłączyć aplikacji dla przedsiębiorstw, dzięki czemu użytkownicy mogą zalogować się w niej w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdbabc1a1ccf4bf27172a4db53255eeb1576def9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180508"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Wyłączanie logowania użytkowników dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory
To proste, można wyłączyć aplikacji dla przedsiębiorstw, dzięki czemu użytkownicy mogą zalogować się w niej w usłudze Azure Active Directory (Azure AD). Musi mieć odpowiednie uprawnienia do zarządzania aplikacji przedsiębiorstwa, a musi być administratorem globalnym katalogu.

## <a name="how-do-i-disable-user-sign-ins"></a>Jak wyłączyć logowania użytkowników?
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz **wszystkich usług**, wprowadź **usługi Azure Active Directory** w polu tekstowym, a następnie wybierz pozycję **Enter**.
3. Na **usługi Azure Active Directory** -  ***directoryname*** okienku (oznacza to, że usługi Azure AD dla katalogu zarządzasz), wybierz **aplikacje dla przedsiębiorstw**.

    ![Otwieranie aplikacji dla przedsiębiorstw](./media/disable-user-sign-in-portal/open-enterprise-apps.png)
4. Na **aplikacje dla przedsiębiorstw** okienku wybierz **wszystkie aplikacje**. Zobaczysz listę aplikacji, którymi można zarządzać.
5. Na **aplikacje w przedsiębiorstwie — wszystkie aplikacje** okienku, wybierz aplikację.
6. Na ***appname*** okienku (czyli nazwę wybranej aplikacji w tytule), wybierz **właściwości**.

    ![Wybierając polecenie wszystkie aplikacje](./media/disable-user-sign-in-portal/select-app.png)
7. Na ***appname*** - **właściwości** okienku wybierz **nie** dla **włączono dla użytkowników do logowania?**.
8. Wybierz **Zapisz** polecenia.

## <a name="next-steps"></a>Kolejne kroki
* [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](assign-user-or-group-access-portal.md)
* [Usuń przypisanie użytkownika lub grupy z aplikacji przedsiębiorstwa](remove-user-or-group-access-portal.md)
* [Zmiana nazwy lub logo aplikacji przedsiębiorstwa](change-name-or-logo-portal.md)
