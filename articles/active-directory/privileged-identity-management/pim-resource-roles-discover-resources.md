---
title: Odnajdź zasoby platformy Azure do zarządzania w usłudze PIM | Dokumentacja firmy Microsoft
description: Dowiedz się, jak odnajdywać zasoby platformy Azure do zarządzania w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: b5d48b3f854afaa79574e0ec13cff91f60396ac6
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190662"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Odnajdź zasoby platformy Azure do zarządzania w usłudze PIM

Dowiedz się, jak odnajdywanie i zarządzanie zasobami platformy Azure, korzystając z Privileged Identity Management (PIM) w usłudze Azure Active Directory (Azure AD). Te informacje mogą być pomocne, aby organizacje, które już używają usługi PIM do ochrony zasobów administratora, a do właścicieli subskrypcji, którzy chcą zabezpieczanie zasobów w środowisku produkcyjnym.

Po skonfigurowaniu usługi PIM dla zasobów platformy Azure, należy odnaleźć i wybrać zasoby, które mają być chronione przez usługę PIM. Nie ma żadnego limitu liczby zasobów, którymi można zarządzać za pomocą usługi PIM. Firma Microsoft zaleca jednak począwszy od najbardziej krytycznych zasobów (produkcja).

> [!NOTE]
> Można jedynie Wyszukaj i wybierz zasoby do grupy lub subskrypcji zarządzania można zarządzać za pomocą usługi PIM. Jeśli zarządzasz grupę zarządzania lub subskrypcji w usłudze PIM, możesz zarządzać jego zasoby podrzędne.

## <a name="discover-resources"></a>Odnajdź zasoby

W witrynie Azure portal przejdź do **Privileged Identity Management** okienka. W menu po lewej stronie w **ZARZĄDZAJ** zaznacz **zasobów platformy Azure**.

!["Privileged Identity Management — zasoby platformy Azure" okienko](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Jeśli po raz pierwszy używasz usługi PIM dla zasobów platformy Azure, należy najpierw uruchomić odnajdywanie w celu odnalezienia zasobów do zarządzania. W **odnajdywania zasobów** okienku wybierz **odnajdywania zasobów** przycisk, aby uruchomić proces odnajdowania.

![W okienku "Odnajdź zasoby"](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Czy innego zasobu lub katalogu administratora w Twojej organizacji zarządza już zasób platformy Azure za pomocą usługi PIM, w przypadku kwalifikujące się przypisanie roli dla zasobu w widoku listy wyświetla komunikat **odnajdź zasoby lub Aktywuj kwalifikujące się przypisanie roli kontynuować**. 

![Przycisk "Odnajdź zasoby" w "Privileged Identity Manager — zasoby platformy Azure" okienko](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

Po wybraniu **odnajdywania zasobów** przycisk, czy z górnego menu lub w środku okienku zostanie wyświetlona lista subskrypcji, którymi można zarządzać. Subskrypcje, które są wyróżnione są już chronione przez usługę PIM.

> [!NOTE]
> Aby uniemożliwić usunięcie ustawień usługi PIM, po subskrypcji jest równa zarządzane inny administrator zasobów, subskrypcji nie może być niezarządzane.

!["Zasobów platformy Azure — odnajdywanie" okienko](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

W **zasobów** kolumny, umieść wskaźnik myszy na subskrypcję, która ma być chroniony za pomocą usługi PIM. Następnie zaznacz pole wyboru po lewej stronie nazwy zasobu. Możesz wybrać wiele subskrypcji w danym momencie.

![Lista zasobów w "zasobów platformy Azure — odnajdywanie" okienko](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Aby zainicjować proces dołączania w górnym menu wybierz **zarządzanie zasobami**.

![Przycisk "Manage zasobów" w "zasobów platformy Azure — odnajdywanie" okienko](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

Wybrane zasoby są teraz zarządzane przez usługę PIM. Aby zamknąć ekran odnajdywania, w prawym górnym rogu wybierz **X**. Aby rozpocząć zarządzanie ustawień usługi PIM i przypisywanie elementów członkowskich, w menu w górnej części **Privileged Identity Management — zasoby platformy Azure** okienku wybierz **Odśwież** przycisku.

![Przycisk "Odśwież" na "Privileged Identity Management — zasoby platformy Azure" w menu u góry okienka](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM](pim-resource-roles-configure-role-settings.md)
- [Przypisz role zasobów platformy Azure w usłudze PIM](pim-resource-roles-assign-roles.md)
