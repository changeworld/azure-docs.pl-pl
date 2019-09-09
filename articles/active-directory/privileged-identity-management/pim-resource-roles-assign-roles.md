---
title: Przypisywanie ról zasobów platformy Azure w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak przypisywać role zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
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
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2850f17c96ef031f9e1b8e11558ab369e4175b0
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804288"
---
# <a name="assign-azure-resource-roles-in-pim"></a>Przypisywanie ról zasobów platformy Azure w usłudze PIM

Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) może zarządzać wbudowanymi rolami zasobów platformy Azure, a także rolami niestandardowymi, w tym (ale nie ograniczonymi do):

- Właściciel
- Administrator dostępu użytkowników
- Współautor
- Administrator zabezpieczeń
- Menedżer zabezpieczeń i inne

> [!NOTE]
> Użytkownicy lub członkowie grupy przypisani do ról administratora dostępu właściciela lub użytkownika oraz Administratorzy globalni, którzy umożliwiają zarządzanie subskrypcjami w usłudze Azure AD, są administratorami zasobów. Ci Administratorzy mogą przypisywać role, konfigurować ustawienia ról i przeglądać dostęp przy użyciu usług PIM dla zasobów platformy Azure. Oznacza to, że konto nie będzie miało uprawnień do zarządzania usługą PIM dla zasobów, jeśli użytkownik nie ma roli administratora zasobów. Wyświetl listę [ról wbudowanych dla zasobów platformy Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Przypisywanie roli

Wykonaj następujące kroki, aby użytkownik mógł kwalifikować się do roli zasobów platformy Azure.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który jest członkiem roli [administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

    Aby uzyskać informacje na temat udzielania innym administratorom dostępu do usługi PIM, zobacz [udzielanie dostępu innym administratorom w celu zarządzania usługą PIM](pim-how-to-give-access-to-pim.md).

1. Otwórz **Azure AD Privileged Identity Management**.

    Jeśli jeszcze nie uruchomiono usługi PIM w Azure Portal, przejdź do pozycji [Rozpocznij korzystanie](pim-getting-started.md)z usługi PIM.

1. Kliknij pozycję **zasoby platformy Azure**.

1. Użyj **filtru zasobów** , aby odfiltrować listę zarządzanych zasobów.

    ![Lista zasobów platformy Azure do zarządzania](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Kliknij zasób, którym chcesz zarządzać, na przykład subskrypcję lub grupę zarządzania.

1. W obszarze Zarządzanie kliknij pozycję **role** , aby wyświetlić listę ról dla zasobów platformy Azure.

    ![Role zasobów platformy Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Kliknij przycisk **Dodaj członka** , aby otworzyć okienko nowe przypisanie.

1. Kliknij pozycję **Wybierz rolę** , aby otworzyć okienko wybierz rolę.

    ![Nowe okienko przypisania](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Kliknij rolę, którą chcesz przypisać, a następnie kliknij przycisk **Wybierz**.

    Zostanie otwarte okienko Wybierz członka lub grupę.

1. Kliknij członka lub grupę, która ma zostać przypisana do roli, a następnie kliknij przycisk **Wybierz**.

    ![Wybierz okienko elementu członkowskiego lub grupy](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Zostanie otwarte okienko ustawienia członkostwa.

1. Na liście **Typ przypisania** wybierz pozycję **kwalifikujące się** lub **aktywne**.

    ![Okienko ustawień członkostw](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Usługa PIM dla zasobów platformy Azure oferuje dwa różne typy przypisania:

    - **Kwalifikujące się** przypisania wymagają, aby członek roli wykonywał akcję do korzystania z roli. Akcje te mogą obejmować przeprowadzenie uwierzytelniania wieloskładnikowego (MFA), podanie uzasadnienia biznesowego lub żądanie zatwierdzenia od wyznaczonych osób zatwierdzających.

    - **Aktywne** przypisania nie wymagają, aby członkowie mogli wykonać żadną akcję w celu korzystania z roli. Członkowie przypisani jako aktywni mają uprawnienia przypisane do roli przez cały czas.

1. Jeśli przypisanie powinno być trwałe (trwale kwalifikujące się lub trwale przypisane), zaznacz pole wyboru **trwale** .

    W zależności od ustawień roli pole wyboru może nie być wyświetlane lub może być niemodyfikowalne.

1. Aby określić określony czas trwania przypisania, usuń zaznaczenie pola wyboru i zmodyfikuj datę i godzinę rozpoczęcia i/lub zakończenia.

    ![Ustawienia członkostw — Data i godzina](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Na koniec kliknij przycisk **Gotowe**.

    ![Nowe przypisanie — Dodaj](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Aby utworzyć nowe przypisanie roli, kliknij przycisk **Dodaj**. Zostanie wyświetlone powiadomienie o stanie.

    ![Nowe przypisanie — powiadomienie](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualizowanie lub usuwanie istniejącego przypisania roli

Wykonaj następujące kroki, aby zaktualizować lub usunąć istniejące przypisanie roli.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **zasoby platformy Azure**.

1. Kliknij zasób, którym chcesz zarządzać, na przykład subskrypcję lub grupę zarządzania.

1. W obszarze Zarządzanie kliknij pozycję **role** , aby wyświetlić listę ról dla zasobów platformy Azure.

    ![Role zasobów platformy Azure — Wybieranie roli](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Kliknij rolę, którą chcesz zaktualizować lub usunąć.

1. Znajdź przypisanie roli na kartach **kwalifikujące się role** lub **role aktywne** .

    ![Aktualizowanie lub usuwanie przypisania roli](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Kliknij przycisk **Aktualizuj** lub **Usuń** , aby zaktualizować lub usunąć przypisanie roli.

    Informacje o rozszerzaniu przypisania roli znajdują się [w temacie rozszerzanie lub odnawianie ról zasobów platformy Azure w programie PIM](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Następne kroki

- [Przedłużanie lub odnowienie ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-renew-extend.md)
- [Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM](pim-resource-roles-configure-role-settings.md)
- [Przypisywanie ról usługi Azure AD w usłudze PIM](pim-how-to-add-role-to-user.md)
