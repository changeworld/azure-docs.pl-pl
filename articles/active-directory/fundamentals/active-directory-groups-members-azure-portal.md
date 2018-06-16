---
title: Zarządzanie członkami grupy w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dodawanie i usuwanie użytkowników i urządzeń z grupy w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: 43514a612cbc8dcba5ebf33bea369c1d7855ab58
ms.sourcegitcommit: 5821eef990c26fa045e4beacce39f6b02b83156b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/15/2018
ms.locfileid: "35664650"
---
# <a name="manage-group-membership-for-users-in-your-azure-active-directory-tenant"></a>Zarządzanie członkostwami grup użytkowników w dzierżawie usługi Azure Active Directory
W tym artykule wyjaśniono, jak można zarządzać członkami grupy w usłudze Azure Active Directory (Azure AD).

## <a name="how-do-i-find-the-members-and-manage-them"></a>Jak znaleźć członków i zarządzać nimi?
1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz **wszystkie usługi**, wprowadź **użytkowników i grup** w polu tekstowym, a następnie wybierz **Enter**.

   ![Otwieranie Zarządzanie użytkownikami](./media/active-directory-groups-members-azure-portal/search-user-management.png)
3. Na **użytkowników i grup** bloku, wybierz opcję **wszystkich grup**.

   ![Otwieranie bloku grupy](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)
4. Na **użytkowników i grup — wszystkie grupy** bloku, wybierz grupę.
5. Na **grupy - *groupname***  bloku, wybierz opcję **członków**.

   ![Otwieranie bloku elementy członkowskie](./media/active-directory-groups-members-azure-portal/view-group-members.png)
6. Aby dodać członków do grupy, na **grupy - członków** bloku, wybierz opcję **Dodaj członków**.

   ![Dodaj polecenie elementy członkowskie](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)
7. Na **członków** bloku, wybierz jeden lub więcej użytkowników lub urządzeń do dodania do grupy, a następnie wybierz **wybierz** przycisk w dolnej części bloku, aby dodać je do grupy. **Użytkownika** okno filtry wyświetlania na podstawie zgodności wpis do dowolnej części nazwy użytkownika lub urządzenia. Nie symbole wieloznaczne są akceptowane w tym polu.
8. Aby usunąć członków z grupy, na **grupy - członków** bloku, wybierz element członkowski.
9. Na ***membername*** bloku, wybierz opcję **Usuń** polecenie i Potwierdź wybór w wierszu.

   ![Usuń elementy członkowskie, polecenie](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)
10. Po zakończeniu zmiana członków grupy, wybierz **zapisać**.

## <a name="additional-information"></a>Dodatkowe informacje
Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

* [Zobacz istniejących grup](active-directory-groups-view-azure-portal.md)
* [Utwórz nową grupę i dodawanie członków](active-directory-groups-create-azure-portal.md)
* [Zarządzanie ustawieniami grupy](active-directory-groups-settings-azure-portal.md)
* [Zarządzanie członkostwami grup](active-directory-groups-membership-azure-portal.md)
* [Dynamiczne reguły dla użytkowników w grupie zarządzania](../active-directory-groups-dynamic-membership-azure-portal.md)
