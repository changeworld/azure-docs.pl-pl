---
title: Zarządzanie członkami grupy w usłudze Azure AD | Microsoft Docs
description: Jak dodać lub usunąć użytkowników i urządzenia z grupy w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 08/28/2017
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: 947b0c11aba211530e3ae25d6617079bcaf2995f
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860495"
---
# <a name="manage-group-membership-for-users-in-your-azure-active-directory-tenant"></a>Zarządzanie członkostwem w grupie dla użytkowników w dzierżawie usługi Azure Active Directory
W tym artykule wyjaśniono, jak zarządzać członkami grupy w usłudze Azure Active Directory (Azure AD).

## <a name="how-do-i-find-the-members-and-manage-them"></a>Jak mogę znaleźć członków i zarządzać nimi?
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz pozycję **Wszystkie usługi**, w polu tekstowym wprowadź filtr **Użytkownicy i grupy**, a następnie naciśnij klawisz **Enter**.

   ![Otwieranie okna zarządzania użytkownikami](./media/active-directory-groups-members-azure-portal/search-user-management.png)
3. W bloku **Użytkownicy i grupy** wybierz pozycję **Wszystkie grupy**.

   ![Otwieranie bloku grup](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)
4. W bloku **Użytkownicy i grupy — Wszystkie grupy** wybierz grupę.
5. W bloku **Grupa — *nazwa_grupy*** wybierz pozycję **Członkowie**.

   ![Otwieranie bloku Członkowie](./media/active-directory-groups-members-azure-portal/view-group-members.png)
6. Aby dodać członków do grupy, w bloku **Grupy — Członkowie** wybierz pozycję **Dodaj członków**.

   ![Polecenie Dodaj członków](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)
7. W bloku **Członkowie** wybierz co najmniej jednego użytkownika lub urządzenie w celu dodania do grupy, a następnie kliknij przycisk **Wybierz** w dolnej części bloku, aby dodać te pozycje do grupy. Pole **Użytkownik** służy do filtrowania wyświetlanych danych na podstawie dopasowania wpisanej wartości do dowolnej części nazwy użytkownika lub urządzenia. W tym polu nie są akceptowane żadne symbole wieloznaczne.
8. Aby usunąć członków z grupy, w bloku **Grupy — Członkowie** wybierz członka.
9. W bloku ***nazwa_członka*** wybierz polecenie **Usuń** i potwierdź wybór po wyświetleniu monitu.

   ![Polecenie usunięcia członków](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)
10. Po zakończeniu modyfikowania członków grupy wybierz pozycję **Zapisz**.

## <a name="additional-information"></a>Dodatkowe informacje
Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

* [Wyświetlanie istniejących grup](active-directory-groups-view-azure-portal.md)
* [Tworzenie nowej grupy i dodawanie członków](active-directory-groups-create-azure-portal.md)
* [Zarządzanie ustawieniami grupy](active-directory-groups-settings-azure-portal.md)
* [Zarządzanie członkostwem w grupie](active-directory-groups-membership-azure-portal.md)
* [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](../users-groups-roles/groups-dynamic-membership.md)
