---
title: Zarządzanie grupami w usłudze Azure Active Directory należy grupy | Dokumentacja firmy Microsoft
description: Grupy mogą zawierać inne grupy w usłudze Azure Active Directory. Oto jak zarządzać tymi członkostwa.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 9865f871f925b4ab1dd290b0afba3258581f0bf6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714327"
---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>Zarządzania do grupy, które grupy należy w dzierżawie usługi Azure Active Directory
Grupy mogą zawierać inne grupy w usłudze Azure Active Directory. Oto jak zarządzać tymi członkostwa.

## <a name="how-do-i-find-the-groups-of-which-my-group-is-a-member"></a>Jak znaleźć grup, których członkiem jest mojej grupy?
1. Zaloguj się w [centrum administracyjnym usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz **użytkowników i grup**.

   ![Otwieranie użytkowników i grup obrazu](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
1. Wybierz **wszystkich grup**.

   ![Wybieranie obrazu grup](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
1. Wybierz grupę.
2. Wybierz **członkostw**.

   ![Otwieranie grupy członkostwa obrazu](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
1. Aby dodać grupy jako członka innej grupy, na **Group - członkostw** bloku, wybierz opcję **Dodaj** polecenia.
2. Wybierz grupę z **Wybieranie grupy** bloku, a następnie wybierz **wybierz** przycisk w dolnej części bloku. Grupy można dodać tylko do jednej grupy naraz. **Użytkownika** okno filtry wyświetlania na podstawie zgodności wpis do dowolnej części nazwy użytkownika lub urządzenia. Nie symbole wieloznaczne są akceptowane w tym polu.

   ![Dodaj członkostwa w grupie](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. Aby usunąć grupy jako członka innej grupy, na **Group - członkostw** bloku, wybierz grupę.
9. Wybierz **Usuń** polecenie i Potwierdź wybór w wierszu.

   ![Usuń polecenie członkostwa](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. Po zakończeniu zmiany członkostwa w grupach dla Twojej grupy zaznacz **zapisać**.

## <a name="additional-information"></a>Dodatkowe informacje
Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

* [Zobacz istniejących grup](active-directory-groups-view-azure-portal.md)
* [Utwórz nową grupę i dodawanie członków](active-directory-groups-create-azure-portal.md)
* [Zarządzanie ustawieniami grupy](active-directory-groups-settings-azure-portal.md)
* [Elementy członkowskie grupy zarządzania](active-directory-groups-members-azure-portal.md)
* [Dynamiczne reguły dla użytkowników w grupie zarządzania](active-directory-groups-dynamic-membership-azure-portal.md)
