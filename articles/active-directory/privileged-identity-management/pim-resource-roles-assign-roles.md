---
title: Przypisywanie ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się, jak przypisać role zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34051a31c6ccf69356f330d7c5ecb009f760857a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266561"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Przypisywanie ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi

Zarządzanie tożsamościami uprzywilejowanymi usługi Azure Directory (Azure AD) (PIM) może zarządzać wbudowanymi rolami zasobów platformy Azure, a także rolami niestandardowymi, w tym (ale nie ograniczając się do):

- Właściciel
- Administrator dostępu użytkowników
- Współautor
- Administrator zabezpieczeń
- Menedżer zabezpieczeń

> [!NOTE]
> Użytkownicy lub członkowie grupy przypisanej do ról subskrypcji Administratora dostępu właściciela lub użytkownika oraz administratorzy usługi Azure AD Global, którzy włączą zarządzanie subskrypcjami w usłudze Azure AD, mają domyślnie uprawnienia administratora zasobów. Administratorzy ci mogą przypisywać role, konfigurować ustawienia ról i przeglądać dostęp przy użyciu zarządzania tożsamościami uprzywilejowanymi dla zasobów platformy Azure. Użytkownik nie może zarządzać zarządzaniem tożsamościami uprzywilejowanymi dla zasobów bez uprawnień administratora zasobów. Wyświetl listę [wbudowanych ról zasobów platformy Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Przypisywanie roli

Wykonaj następujące kroki, aby użytkownik kwalifikował się do roli zasobów platformy Azure.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest członkiem roli [administratora ról uprzywilejowanych.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

    Aby uzyskać informacje dotyczące udzielania innym administratorom dostępu do zarządzania zarządzaniem uprzywilejowanym zarządzaniem tożsamościami, zobacz [Udzielanie dostępu innym administratorom w celu zarządzania zarządzaniem uprzywilejowanym zarządzaniem tożsamościami](pim-how-to-give-access-to-pim.md).

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **zasoby platformy Azure**.

1. Filtr **Zasobów** służy do filtrowania listy zasobów zarządzanych.

    ![Lista zasobów platformy Azure do zarządzania](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Wybierz zasób, który chcesz zarządzać, taki jak subskrypcja lub grupa zarządzania.

1. W obszarze Zarządzanie wybierz pozycję **Role,** aby wyświetlić listę ról zasobów platformy Azure.

    ![Role zasobów platformy Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Wybierz **pozycję Dodaj element członkowski,** aby otworzyć okienko Nowe przypisanie.

1. Wybierz **pozycję Wybierz rolę,** aby otworzyć okienko Wybierz rolę.

    ![Nowe okienko przydziału](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Wybierz rolę, którą chcesz przypisać, a następnie kliknij przycisk **Wybierz**.

    Zostanie otwarte okienko Wybierz członka lub grupę.

1. Wybierz członka lub grupę, którą chcesz przypisać do roli, a następnie kliknij przycisk **Wybierz**.

    ![Wybieranie okienka członka lub grupy](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Zostanie otwarte okienko Ustawienia członkostwa.

1. Na liście **Typ przydziału** wybierz pozycję **Kwalifikujące się** lub **Aktywne**.

    ![Okienko ustawień członkostwa](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Zarządzanie tożsamościami uprzywilejowanymi dla zasobów platformy Azure zapewnia dwa różne typy przydziałów:

    - **Kwalifikujące się** przypisania wymagają od członka roli wykonania akcji w celu użycia tej roli. Akcje te mogą obejmować przeprowadzenie uwierzytelniania wieloskładnikowego (MFA), podanie uzasadnienia biznesowego lub żądanie zatwierdzenia od wyznaczonych osób zatwierdzających.

    - **Aktywne** przypisania nie wymagają od członka wykonywania żadnej akcji w celu użycia tej roli. Członkowie przypisani jako aktywni mają uprawnienia przypisane do roli przez cały czas.

1. Jeśli przypisanie powinno być stałe (trwale kwalifikujące się lub przypisane na stałe), zaznacz pole wyboru **Trwale.**

    W zależności od ustawień roli pole wyboru może nie być wyświetlane lub może być niemodyfikowalne.

1. Aby określić określony czas trwania przydziału, wyczyść pole wyboru i zmodyfikuj pola daty i godziny rozpoczęcia i/lub zakończenia.

    ![Ustawienia członkostwa - data i godzina](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Po zakończeniu wybierz pozycję **Gotowe**.

    ![Nowe przypisanie — dodawanie](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Aby utworzyć nowe przypisanie roli, wybierz pozycję **Dodaj**. Zostanie wyświetlone powiadomienie o stanie.

    ![Nowe przypisanie — powiadomienie](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualizowanie lub usuwanie istniejącego przypisania roli

Wykonaj następujące kroki, aby zaktualizować lub usunąć istniejące przypisanie roli.

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **zasoby platformy Azure**.

1. Wybierz zasób, który chcesz zarządzać, taki jak subskrypcja lub grupa zarządzania.

1. W obszarze Zarządzanie wybierz pozycję **Role,** aby wyświetlić listę ról zasobów platformy Azure.

    ![Role zasobów platformy Azure — wybierz rolę](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Wybierz rolę, którą chcesz zaktualizować lub usunąć.

1. Znajdź przypisanie roli na kartach **Kwalifikujące się role** lub **Aktywne role.**

    ![Aktualizowanie lub usuwanie przypisania roli](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Wybierz **pozycję Aktualizuj** lub **Usuń,** aby zaktualizować lub usunąć przypisanie roli.

    Aby uzyskać informacje dotyczące rozszerzania przydziału ról, zobacz [Rozszerzanie lub odnawianie ról zasobów platformy Azure w dziale Zarządzanie tożsamościami uprzywilejowanymi](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Następne kroki

- [Rozszerzanie lub odnawianie ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-renew-extend.md)
- [Konfigurowanie ustawień roli zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-configure-role-settings.md)
- [Przypisywanie ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-add-role-to-user.md)
