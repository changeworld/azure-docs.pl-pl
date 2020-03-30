---
title: Odkryj zasoby platformy Azure do zarządzania w pim — Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak odnajdować zasoby platformy Azure do zarządzania w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87da43100c7494937ddc842e0f903ba3a360959e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022892"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Odnajdowanie zasobów platformy Azure do zarządzania w dziale Zarządzanie tożsamościami uprzywilejowanymi

Korzystając z usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM), można poprawić ochronę zasobów platformy Azure. Jest to przydatne dla organizacji, które już używają zarządzania tożsamościami uprzywilejowanymi do ochrony ról usługi Azure AD, oraz dla właścicieli grup zarządzania i subskrypcji, które chcą zabezpieczyć zasoby produkcyjne.

Podczas pierwszej konfiguracji uprzywilejowanego zarządzania tożsamościami dla zasobów platformy Azure, należy odnajdywać i wybierać zasoby do ochrony za pomocą uprzywilejowanego zarządzania tożsamościami. Nie ma ograniczeń co do liczby zasobów, którymi można zarządzać za pomocą zarządzania tożsamościami uprzywilejowanymi. Zalecamy jednak rozpoczęcie od najbardziej krytycznych zasobów (produkcyjnych).

## <a name="discover-resources"></a>Odnajdywanie zasobów

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **zasoby platformy Azure**.

    Jeśli po raz pierwszy używasz zarządzania tożsamościami uprzywilejowanymi dla zasobów platformy Azure, zobaczysz stronę **Odnajduj zasoby.**

    ![Odkryj okienko zasobów bez zasobów wymienionych po raz pierwszy](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Jeśli inny administrator w organizacji już zarządza zasobami platformy Azure w zarządzania tożsamościami uprzywilejowanymi, zobaczysz listę zasobów, które są obecnie zarządzane.

    ![Odkryj zasoby z listą zasobów, które są obecnie zarządzane](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Wybierz **pozycję Odkryj zasoby,** aby uruchomić środowisko odnajdywania.

    ![Okienko odnajdywania z wyświetlaniem zasobów, którymi można zarządzać, takich jak subskrypcje i grupy zarządzania](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. Na stronie **Odnajdowanie** użyj **filtru stanu zasobu** i **Wybierz typ zasobu,** aby filtrować grupy zarządzania lub subskrypcje, do których masz uprawnienia do zapisu. To chyba najłatwiej zacząć od **Wszystkich** początkowo.

    Za pomocą zarządzania tożsamościami uprzywilejowanych można wyszukiwać i wybierać tylko zasoby grupy zarządzania lub subskrypcji. Podczas zarządzania grupą zarządzania lub subskrypcji w zarządzania tożsamościami uprzywilejowanymi, można również zarządzać jej zasobów podrzędnych.

1. Zaznacz to pole wyboru obok wszystkich zasobów niezarządzanych, którymi chcesz zarządzać.

1. Wybierz **pozycję Zarządzaj zasobem,** aby rozpocząć zarządzanie wybranymi zasobami.

    > [!NOTE]
    > Po zarządzaniu grupą zarządzania lub subskrypcją nie można jej odpodatkować. Uniemożliwia to innemu administratorowi zasobów usunięcie ustawień zarządzania tożsamościami uprzywilejowanymi.

    ![Okienko Odnajdowanie z zaznaczonym zasobem i wyróżnioną opcją Zarządzaj zasobem](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Jeśli zostanie wyświetlony komunikat potwierdzający dołączanie wybranego zasobu do zarządzania, wybierz opcję **Tak**.

    ![Komunikat potwierdzający dołączanie wybranych zasobów do zarządzania](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ustawień roli zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-configure-role-settings.md)
- [Przypisywanie ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-assign-roles.md)
