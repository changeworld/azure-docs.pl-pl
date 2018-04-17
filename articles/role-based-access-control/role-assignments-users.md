---
title: Wyświetlić przypisania dostępu do zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Wyświetl i zarządzaj nimi wszystkie przypisania opartego na rolach kontroli dostępu dla dowolnego użytkownika lub grupę w portalu Azure
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: jeffsta
ms.assetid: e6f9e657-8ee3-4eec-a21c-78fe1b52a005
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: rolyon
ms.openlocfilehash: 35838ed0f86dc4e063c943087c88d19195df72fd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="view-access-assignments-for-users-and-groups-in-the-azure-portal"></a>Wyświetl przypisania dostępu dla użytkowników i grup w portalu Azure
> [!div class="op_single_selector"]
> * [Zarządzanie dostępem użytkowników lub grup](role-assignments-users.md)
> * [Zarządzanie dostępem do zasobów](role-assignments-portal.md)

Z kontroli dostępu opartej na rolach (RBAC) w usłudze Azure Active Directory (Azure AD) aby zarządzać dostępem do zasobów platformy Azure. 

Dostęp z RBAC przypisaną jest szczegółowych, ponieważ można ograniczyć uprawnienia na dwa sposoby:

* **Zakres:** przypisań ról RBAC ograniczone do określonej subskrypcji, grupy zasobów lub zasobów. Użytkownik uzyskać dostęp do jednego zasobu nie może uzyskać dostępu żadnych innych zasobów w tej samej subskrypcji.
* **Rola:** zakresem przypisania jest zawężony dostępu nawet dalsze przez przypisanie roli. Role można wysokiego poziomu, takich jak właściciela lub określonych, takich jak czytnik maszyny wirtualnej.

Role można przypisać tylko od w ramach subskrypcji, grupy zasobów lub zasobów, która jest zakresem przypisania. Jednak można wyświetlić wszystkie przypisania dostępu dla określonego użytkownika lub grupy w jednym miejscu. Można mieć maksymalnie 2000 przypisania roli w każdej subskrypcji. 

Uzyskaj więcej informacji o sposobie [zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](role-assignments-portal.md).

## <a name="view-access-assignments"></a>Wyświetl dostępu przypisania
Aby wyszukać przypisań dostępu dla jednego użytkownika lub grupę, uruchom w usłudze Azure Active Directory w [portalu Azure](http://portal.azure.com).

1. Wybierz pozycję **Azure Active Directory**. Jeśli ta opcja nie jest widoczna na liście nawigacji, wybierz **wszystkie usługi** , a następnie przewiń w dół, aby znaleźć **usługi Azure Active Directory**.
2. Wybierz **użytkowników i grup**, a następnie albo **wszyscy użytkownicy** lub **wszystkich grup**. Na przykład możemy skupić się na poszczególnych użytkowników.
    ![Zarządzaj użytkownikami i grupami w usłudze Azure Active Directory — zrzut ekranu](./media/role-assignments-users/rbac_users_groups.png)
3. Wyszukaj użytkownika według nazwy lub nazwy użytkownika.
4. Wybierz pozycję **Zasoby platformy Azure** w bloku użytkownika. Zostaną wyświetlone wszystkie przypisania dostępu dla danego użytkownika.

### <a name="read-permissions-to-view-assignments"></a>Aby wyświetlić przypisania uprawnienia do odczytu
Ta strona zawiera tylko przypisania dostępu, które mają uprawnienia do odczytu. Na przykład mieć dostęp do odczytu do subskrypcji A i przejdź do bloku zasobów platformy Azure, aby sprawdzić przypisania użytkownika. Można wyświetlać swoje przydziały dostępu dla subskrypcji A, ale nie widzi ona również ma dostęp do przypisania na subskrypcji B.

## <a name="delete-access-assignments"></a>Usuwanie przypisania dostępu
Z tego bloku można usunąć przypisania dostępu, które zostały przypisane bezpośrednio do użytkownika lub grupy. Przypisywanie dostępu została odziedziczona z grupy nadrzędnej, należy przejść do zasobów lub subskrypcji i zarządzanie przypisywaniem istnieje.

1. Z listy wszystkie przypisania dostępu dla użytkownika lub grupy wybierz ten, który chcesz usunąć.
2. Wybierz **Usuń** , a następnie **tak** o potwierdzenie.
    ![Usuń przypisanie dostępu — zrzut ekranu](./media/role-assignments-users/delete_assignment.png)

## <a name="next-steps"></a>Kolejne kroki

* Rozpoczynanie pracy z opartej na rolach kontroli dostępu do [zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](role-assignments-portal.md)
* Zobacz [Wbudowane role RBAC](built-in-roles.md)

