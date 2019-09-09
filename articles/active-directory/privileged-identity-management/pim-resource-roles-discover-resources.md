---
title: Odnajdywanie zasobów platformy Azure do zarządzania w usłudze PIM Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak odnajdywać zasoby platformy Azure do zarządzania w usłudze Azure AD Privileged Identity Management (PIM).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1f1712ab7474747829c8744a39e74b78777668
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804166"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Odnajdywanie zasobów platformy Azure do zarządzania w usłudze PIM

Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) można poprawić ochronę zasobów platformy Azure. Jest to pomocne w organizacjach, które już używają usługi PIM do ochrony ról Azure AD i grup zarządzania i właścicieli subskrypcji, którzy chcą zabezpieczyć zasoby produkcyjne.

Podczas pierwszego konfigurowania usług PIM dla zasobów platformy Azure należy odnaleźć i wybrać zasoby, które mają być chronione za pomocą usługi PIM. Nie ma limitu liczby zasobów, którymi można zarządzać za pomocą programu PIM. Zalecamy jednak rozpoczęcie od najbardziej krytycznych zasobów (produkcyjnych).

## <a name="discover-resources"></a>Odnajdź zasoby

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **zasoby platformy Azure**.

    Jeśli korzystasz z usług PIM dla zasobów platformy Azure po raz pierwszy, zobaczysz okienko odnajdywanie zasobów.

    ![Okienko odnajdywanie zasobów bez zasobów wymienionych na potrzeby pierwszego uruchomienia](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Jeśli inny zasób lub administrator katalogu w organizacji zarządza zasobami platformy Azure w usłudze PIM, zostanie wyświetlona lista zasobów, które są aktualnie zarządzane.

    ![Okienko odnajdywania zasobów zawierające listę aktualnie zarządzanych zasobów](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Kliknij pozycję **odkryj zasoby** , aby uruchomić środowisko odnajdywania.

    ![Okienko odnajdywania zawierające listę zasobów, którymi można zarządzać, takich jak subskrypcje i grupy zarządzania](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. W okienku odnajdywania Użyj **filtru stanu zasobów** i **Wybierz typ zasobu** , aby odfiltrować grupy zarządzania lub subskrypcje, do których masz uprawnienia do zapisu. Prawdopodobnie najłatwiej **zacząć od początku** .

    Możesz wyszukać i wybrać grupę zarządzania lub zasoby subskrypcji, aby zarządzać za pomocą programu PIM. W przypadku zarządzania grupą zarządzania lub subskrypcją w usłudze PIM można także zarządzać swoimi zasobami podrzędnymi.

1. Dodaj znacznik wyboru obok wszystkich niezarządzanych zasobów, którymi chcesz zarządzać.

1. Kliknij pozycję **Zarządzaj zasobem** , aby rozpocząć zarządzanie wybranymi zasobami.

    > [!NOTE]
    > Gdy grupa lub subskrypcja zarządzania jest ustawiona na wartość zarządzane, nie może być niezarządzana. Zapobiega to usunięciu ustawień usługi PIM przez innego administratora zasobów.

    ![Okienko odnajdywania z wybranym zasobem i z wyróżnioną opcją Zarządzaj zasobem](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Jeśli zostanie wyświetlony komunikat z prośbą o potwierdzenie dołączenia wybranego zasobu do zarządzania, kliknij przycisk **tak**.

    ![Komunikat potwierdzający dołączenie wybranych zasobów do zarządzania](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM](pim-resource-roles-configure-role-settings.md)
- [Przypisywanie ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-assign-roles.md)
