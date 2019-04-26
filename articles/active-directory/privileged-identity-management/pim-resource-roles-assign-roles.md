---
title: Przypisz role zasobów platformy Azure w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przypisać role zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 610aeec9e4c40d0aad0c28f02697e2cf01edbe4a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60438765"
---
# <a name="assign-azure-resource-roles-in-pim"></a>Przypisz role zasobów platformy Azure w usłudze PIM

Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) można zarządzać ról wbudowanych zasobów platformy Azure, a także niestandardowe role, w tym (między innymi):

- Właściciel
- Administrator dostępu użytkowników
- Współautor
- Administrator zabezpieczeń
- Menedżer zabezpieczeń i nie tylko

> [!NOTE]
> Użytkownicy lub członkowie grupy przypisane do roli właściciel lub Administrator dostępu użytkowników i administratorów globalnych, które umożliwiają Zarządzanie subskrypcją w usłudze Azure AD są administratorami zasobów. Administratorzy mogą Przypisz role, konfigurowanie ustawień roli i przeglądu dostępu przy użyciu usługi PIM dla zasobów platformy Azure. Wyświetl listę [wbudowane role zasobów platformy Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Przypisywanie roli

Wykonaj następujące kroki, aby Ustaw użytkownika z uprawnieniami dla ról zasobów platformy Azure.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest elementem członkowskim [Administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) roli.

    Aby dowiedzieć się, jak udzielić innym dostęp administratora umożliwiający zarządzanie usługą PIM, zobacz [udzielić dostępu do innych administratorów do zarządzania usługą PIM](pim-how-to-give-access-to-pim.md).

1. Otwórz **usługi Azure AD Privileged Identity Management**.

    Jeśli jeszcze nie rozpoczęto jeszcze PIM w witrynie Azure portal, przejdź do strony [rozpocząć korzystanie z usługi PIM](pim-getting-started.md).

1. Kliknij przycisk **zasobów platformy Azure**.

1. Użyj **filtr zasobu** Aby filtrować listę zarządzanych zasobów.

    ![Lista zasobów platformy Azure do zarządzania](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Kliknij zasób, który chcesz zarządzać, takich jak subskrypcję lub grupę zarządzania.

1. W obszarze Zarządzanie, kliknij przycisk **role** Aby wyświetlić listę ról dla zasobów platformy Azure.

    ![Role zasobów platformy Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Kliknij przycisk **Dodawanie elementu członkowskiego** otworzyć nowe okienko przypisania.

1. Kliknij przycisk **wybierz rolę** otworzyć wybierz w okienku roli.

    ![Nowe okienko przypisania](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Kliknij rolę, którą chcesz przypisać, a następnie kliknij przycisk **wybierz**.

    Zostanie otwarty wybierz panel lub grupę.

1. Kliknij element lub grupę, o których chcesz przypisać do roli, a następnie kliknij przycisk **wybierz**.

    ![Wybierz panel lub grupę](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Zostanie otwarte okienko ustawienia członkostwa.

1. W **typ przypisania** listy wybierz **kwalifikujących się** lub **Active**.

    ![Okienko ustawień w grupach](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Usługa PIM dla zasobów platformy Azure udostępnia dwa typy distinct przypisania:

    - **Kwalifikujące się** przypisania wymagają członek roli do wykonania działania, aby korzystać z roli. Akcje te mogą obejmować przeprowadzenie uwierzytelniania wieloskładnikowego (MFA), podanie uzasadnienia biznesowego lub żądanie zatwierdzenia od wyznaczonych osób zatwierdzających.

    - **Aktywne** przypisania nie wymagają elementu członkowskiego do wykonywania dowolnych akcji, aby użyć roli. Członkowie przypisani jako aktywny mają uprawnienia przypisane do roli przez cały czas.

1. Jeśli przypisanie powinno być stałe (trwale zakwalifikowane lub trwale przypisanych), wybierz **trwale** pole wyboru.

    W zależności od ustawień roli pola wyboru mogą nie być wyświetlane lub może być niemodyfikowalnych.

1. Aby określić czas trwania określone przypisanie, wyczyść pole wyboru, a następnie zmodyfikować początkowy lub końcowy pola daty i godziny.

    ![Ustawienia członkostwa — Data i godzina](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Na koniec kliknij przycisk **Gotowe**.

    ![Nowe przypisanie — Dodawanie](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Aby utworzyć nowe przypisanie roli, kliknij przycisk **Dodaj**. Wyświetlane jest powiadomienie o stanie.

    ![Nowe przypisanie — powiadomienie](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualizowanie lub usuwanie istniejącego przypisania roli

Wykonaj następujące kroki, aby zaktualizować lub usunąć istniejące przypisania roli.

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **zasobów platformy Azure**.

1. Kliknij zasób, który chcesz zarządzać, takich jak subskrypcję lub grupę zarządzania.

1. W obszarze Zarządzanie, kliknij przycisk **role** Aby wyświetlić listę ról dla zasobów platformy Azure.

    ![Role zasobów platformy Azure — wybierz rolę](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Kliknij rolę, którą chcesz zaktualizować lub usunąć.

1. Znajdź przypisania roli na **kwalifikujące się role** lub **aktywnych ról** karty.

    ![Aktualizowanie lub usuwanie przypisania roli](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Kliknij przycisk **aktualizacji** lub **Usuń** zaktualizować lub usunąć przypisanie roli.

    Aby uzyskać informacje o rozszerzaniu przypisania roli, zobacz [rozszerzanie lub odnawianie ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Kolejne kroki

- [Rozszerzanie lub odnawianie ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-renew-extend.md)
- [Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM](pim-resource-roles-configure-role-settings.md)
- [Przypisz role usługi Azure AD w usłudze PIM](pim-how-to-add-role-to-user.md)
