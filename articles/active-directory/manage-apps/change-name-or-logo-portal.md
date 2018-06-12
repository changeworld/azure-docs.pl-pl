---
title: Zmiana nazwy lub logo aplikacji przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak zmienić nazwę lub logo aplikacji niestandardowych przedsiębiorstwa w usłudze Azure Active Directory
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
ms.openlocfilehash: ad424d6ca8ea8c35aa502a3d1bd98940591c38e8
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303596"
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Zmiana nazwy lub logo aplikacji przedsiębiorstwa w usłudze Azure Active Directory
To proste zmienić nazwę lub logo dla aplikacji niestandardowych przedsiębiorstwa w usłudze Azure Active Directory (Azure AD). Musi mieć odpowiednie uprawnienia, aby wprowadzić te zmiany, a musi być Twórca aplikacji niestandardowej.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Jak zmienić nazwę lub logo aplikacji przedsiębiorstwa?
1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz **wszystkie usługi**, wprowadź **usługi Azure Active Directory** w polu tekstowym, a następnie wybierz **Enter**.
3. Na **usługi Azure Active Directory - *directoryname***  okienku (to znaczy usługi Azure AD dla katalogu są używane do zarządzania), wybierz **aplikacje dla przedsiębiorstw**.

    ![Otwieranie aplikacji przedsiębiorstwa](./media/change-name-or-logo-portal/open-enterprise-apps.png)
4. Na **aplikacje dla przedsiębiorstw** okienku wybierz **wszystkie aplikacje**. Możesz wyświetlić listę aplikacji, którymi można zarządzać.
5. Na **aplikacje przedsiębiorstwa — wszystkie aplikacje** okienku, wybierz aplikację.
6. Na ***appname*** okienku (to znaczy o nazwie wybranej aplikacji w tytule), wybierz **właściwości**.

    ![Polecenie Właściwości](./media/change-name-or-logo-portal/select-app.png)
7. Na ***appname*** **-właściwości** okienka, przeglądanie w poszukiwaniu pliku do użycia jako nowe logo lub Edytuj nazwę aplikacji, lub obie.

    ![Zmiana polecenia logo lub nameproperties aplikacji](./media/change-name-or-logo-portal/change-logo.png)
8. Wybierz **zapisać** polecenia.

## <a name="next-steps"></a>Kolejne kroki
* [Wyświetl wszystkie moje grupy](../active-directory-groups-view-azure-portal.md)
* [Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](assign-user-or-group-access-portal.md)
* [Usuń przypisanie użytkownika lub grupy z aplikacjami](remove-user-or-group-access-portal.md)
* [Wyłącz logowania użytkowników dla aplikacji przedsiębiorstwa](disable-user-sign-in-portal.md)
