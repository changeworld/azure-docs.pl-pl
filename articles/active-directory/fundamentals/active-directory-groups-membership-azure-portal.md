---
title: Zarządzanie grupami zawierającymi daną grupę w usłudze Azure AD | Microsoft Docs
description: Grupy mogą zawierać inne grupy w usłudze Azure Active Directory. Poniżej przedstawiono sposób zarządzania członkostwem w tych grupach.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 10/10/2017
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: 8a71677ae3ceb5617f0a817a8eff438d5e3f2774
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860447"
---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>Zarządzanie grupami, do których grupa należy w dzierżawie usługi Azure Active Directory
Grupy mogą zawierać inne grupy w usłudze Azure Active Directory. Poniżej przedstawiono sposób zarządzania członkostwem w tych grupach.

## <a name="how-do-i-find-the-groups-of-which-my-group-is-a-member"></a>Jak znaleźć grupy, których członkiem jest moja grupa?
1. Zaloguj się w [centrum administracyjnym usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz pozycję **Użytkownicy i grupy**.

   ![Obraz przedstawiający otwieranie użytkowników i grup](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
1. Wybierz pozycję **Wszystkie grupy**.

   ![Obraz przedstawiający wybieranie grup](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
1. Wybierz grupę.
2. Wybierz pozycję **Członkostwo w grupach**.

   ![Otwieranie członkostwa w grupach, obraz](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
1. Aby dodać grupę jako członek innej grupy, w bloku **Grupa — Członkostwa w grupach** wybierz polecenie **Dodaj**.
2. Wybierz grupę w bloku **Wybierz grupę**, a następnie wybierz przycisk **Wybierz** znajdujący się u dołu bloku. Grupę można dodać tylko do jednej grupy naraz. Pole **Użytkownik** służy do filtrowania wyświetlanych danych na podstawie dopasowania wpisanej wartości do dowolnej części nazwy użytkownika lub urządzenia. W tym polu nie są akceptowane żadne symbole wieloznaczne.

   ![Dodawanie członkostwa w grupie](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. Aby usunąć grupę jako członek innej grupy, w bloku **Grupa — Członkostwa w grupach** wybierz grupę.
9. Wybierz polecenie **Usuń** i potwierdź wybór po wyświetleniu monitu.

   ![Polecenie usunięcia członkostwa](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. Po zakończeniu zmiany członkostw w grupach dla danej grupy wybierz pozycję **Zapisz**.

## <a name="additional-information"></a>Dodatkowe informacje
Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

* [Wyświetlanie istniejących grup](active-directory-groups-view-azure-portal.md)
* [Tworzenie nowej grupy i dodawanie członków](active-directory-groups-create-azure-portal.md)
* [Zarządzanie ustawieniami grupy](active-directory-groups-settings-azure-portal.md)
* [Zarządzanie członkami grupy](active-directory-groups-members-azure-portal.md)
* [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](../users-groups-roles/groups-dynamic-membership.md)
