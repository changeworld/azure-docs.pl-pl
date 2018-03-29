---
title: Zmiana nazwy lub logo aplikacji przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak zmienić nazwę lub logo aplikacji niestandardowych przedsiębiorstwa w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: d01303ce-e6cb-4f3b-a4d6-ec29dfd68146
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 18e5555d4eaf49fceb5aec673eedce8ed90d46a3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Zmiana nazwy lub logo aplikacji przedsiębiorstwa w usłudze Azure Active Directory
To proste zmienić nazwę lub logo dla aplikacji niestandardowych przedsiębiorstwa w usłudze Azure Active Directory (Azure AD). Musi mieć odpowiednie uprawnienia, aby wprowadzić te zmiany, a musi być Twórca aplikacji niestandardowej.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Jak zmienić nazwę lub logo aplikacji przedsiębiorstwa?
1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz **wszystkie usługi**, wprowadź **usługi Azure Active Directory** w polu tekstowym, a następnie wybierz **Enter**.
3. Na **usługi Azure Active Directory - *directoryname***  okienku (to znaczy usługi Azure AD dla katalogu są używane do zarządzania), wybierz **aplikacje dla przedsiębiorstw**.

    ![Otwieranie aplikacji przedsiębiorstwa](./media/active-directory-coreapps-change-app-logo-azure-portal/open-enterprise-apps.png)
4. Na **aplikacje dla przedsiębiorstw** okienku wybierz **wszystkie aplikacje**. Możesz wyświetlić listę aplikacji, którymi można zarządzać.
5. Na **aplikacje przedsiębiorstwa — wszystkie aplikacje** okienku, wybierz aplikację.
6. Na ***appname*** okienku (to znaczy o nazwie wybranej aplikacji w tytule), wybierz **właściwości**.

    ![Polecenie Właściwości](./media/active-directory-coreapps-change-app-logo-azure-portal/select-app.png)
7. Na ***appname*** **-właściwości** okienka, przeglądanie w poszukiwaniu pliku do użycia jako nowe logo lub Edytuj nazwę aplikacji, lub obie.

    ![Zmiana polecenia logo lub nameproperties aplikacji](./media/active-directory-coreapps-change-app-logo-azure-portal/change-logo.png)
8. Wybierz **zapisać** polecenia.

## <a name="next-steps"></a>Kolejne kroki
* [Wyświetl wszystkie moje grupy](active-directory-groups-view-azure-portal.md)
* [Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](active-directory-coreapps-assign-user-azure-portal.md)
* [Usuń przypisanie użytkownika lub grupy z aplikacjami](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Wyłącz logowania użytkowników dla aplikacji przedsiębiorstwa](active-directory-coreapps-disable-app-azure-portal.md)
