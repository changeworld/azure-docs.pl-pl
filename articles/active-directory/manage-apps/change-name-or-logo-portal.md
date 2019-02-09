---
title: Zmiana nazwy lub logo aplikacji przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak zmienić nazwę lub logo aplikacji niestandardowych organizacji w usłudze Azure Active Directory
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
ms.openlocfilehash: 7b3d9558c11f1fc137e939e99dbf544bb22d4063
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962489"
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Zmiana nazwy lub logo aplikacji przedsiębiorstwa w usłudze Azure Active Directory
To proste zmienić nazwę lub logo aplikacji niestandardowych organizacji w usłudze Azure Active Directory (Azure AD). Musi mieć odpowiednie uprawnienia, aby wprowadzić te zmiany, a musi być twórcą aplikacji niestandardowej.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Jak zmienić nazwę lub logo aplikacji przedsiębiorstwa
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz **wszystkich usług**, wprowadź **usługi Azure Active Directory** w polu tekstowym, a następnie wybierz pozycję **Enter**.
3. Na **usługi Azure Active Directory — *directoryname***  okienku (oznacza to, że usługi Azure AD dla katalogu zarządzasz), wybierz **aplikacje dla przedsiębiorstw**.

    ![Otwieranie aplikacji dla przedsiębiorstw](./media/change-name-or-logo-portal/open-enterprise-apps.png)
4. Na **aplikacje dla przedsiębiorstw** okienku wybierz **wszystkie aplikacje**. Zobaczysz listę aplikacji, którymi można zarządzać.
5. Na **aplikacje w przedsiębiorstwie — wszystkie aplikacje** okienku, wybierz aplikację.
6. Na ***appname*** okienku (czyli nazwę wybranej aplikacji w tytule), wybierz **właściwości**.

    ![Wybierając polecenie Właściwości](./media/change-name-or-logo-portal/select-app.png)
7. Na ***appname*** **— właściwości** okienko, przeglądanie w poszukiwaniu pliku pełnić nowe logo lub Edytuj nazwę aplikacji lub obu.

    ![Zmienianie logo lub nameproperties polecenie aplikacji](./media/change-name-or-logo-portal/change-logo.png)
8. Wybierz **Zapisz** polecenia.

## <a name="next-steps"></a>Kolejne kroki
* [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](assign-user-or-group-access-portal.md)
* [Usuń przypisanie użytkownika lub grupy z aplikacji przedsiębiorstwa](remove-user-or-group-access-portal.md)
* [Wyłączanie logowania użytkowników dla aplikacji przedsiębiorstwa](disable-user-sign-in-portal.md)
