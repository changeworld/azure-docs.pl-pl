---
title: Odnajdywanie i zarządzania zasobami Azure przy użyciu Privileged Identity Management | Dokumentacja firmy Microsoft
description: Opisuje sposób ochrony zasobów platformy Azure przy użyciu usługi PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 51a10ea164e8bd7650ad2823281d9ed6a4c91915
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="discover-and-manage-azure-resources-by-using-privileged-identity-management"></a>Odnajdywanie i zarządzania zasobami Azure przy użyciu Privileged Identity Management

Informacje o sposobie odnajdywania i zarządzania zasobami Azure, korzystając z zarządzania tożsamości uprzywilejowanych (PIM) w usłudze Azure Active Directory (Azure AD). Te informacje mogą być pomocne w organizacji, które już używają PIM, aby chronić zasoby administratora oraz właściciele subskrypcji, którzy chcą zabezpieczanie zasobów w środowisku produkcyjnym.

Po skonfigurowaniu usługi PIM dla zasobów platformy Azure, należy odnaleźć i wybierz zasobów chronionych przez usługi PIM. Nie ma żadnego limitu liczby zasobów, którymi można zarządzać w programie PIM. Firma Microsoft zaleca jednak począwszy od najważniejszych zasobów (środowisko produkcyjne).

> [!NOTE]
> Można jedynie wyszukać i wybrać zasoby subskrypcji można zarządzać za pomocą usługi PIM. Podczas zarządzania subskrypcją w PIM można również zarządzać zasoby podrzędne w subskrypcji.

## <a name="discover-resources"></a>Odnajdywanie zasobów

W portalu Azure, przejdź do **Privileged Identity Management** okienka. W menu po lewej stronie w **ZARZĄDZAJ** zaznacz **zasobów Azure**.

!["Privileged Identity Management - zasobów platformy Azure" okienko](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Jeśli jest to pierwsza przy użyciu usługi PIM dla zasobów platformy Azure, najpierw uruchom odnajdywanie w celu odnalezienia zasobów do zarządzania. W **odnajdywania zasobów** okienku wybierz **odnajdywania zasobów** przycisk, aby uruchomić proces odnajdowania.

![W okienku "Odnajdywania zasobów"](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Jeśli inny zasób lub katalogu administrator w organizacji jest już Zarządzanie zasobem platformy Azure przy użyciu usługi PIM lub jeśli przypisania roli kwalifikujących się do zasobu, widok listy wyświetli komunikat **odnajdywania zasobów lub aktywacji Przypisanie roli kwalifikujących się, aby kontynuować**. 

![Przycisk "Odnajdywania zasobów" w "Privileged Identity Manager - zasobów platformy Azure" okienko](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

Po wybraniu **odnajdywania zasobów** przycisku, czy z górnego menu lub w środku okienku zostanie wyświetlona lista subskrypcji, którymi można zarządzać. Subskrypcje, które są wyróżnione są już chronione przez usługi PIM.

> [!NOTE]
> Aby uniemożliwić usunięcie ustawienia PIM po subskrypcji jest równa zarządzane inny administrator zasobów, subskrypcja nie może być niezarządzane.

!["Zasobów platformy Azure — odnajdywania" okienko](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

W **zasobów** kolumny, umieść wskaźnik myszy nad subskrypcji, który chcesz chronić za pomocą usługi PIM. Zaznacz pola wyboru po lewej stronie nazwy zasobu. Jednocześnie można wybrać wiele subskrypcji.

![Lista zasobów w "zasobów platformy Azure — odnajdywania" okienko](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Aby zainicjować proces dołączania, w menu górnym, wybierz **zarządzać zasobem**.

![Przycisk "Zarządzanie zasobów" w "zasobów platformy Azure — odnajdywania" okienko](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

Wybrane zasoby są teraz zarządzane przez usługi PIM. Aby zamknąć ekran odnajdywania, w prawym górnym rogu wybierz **X**. Aby rozpocząć zarządzanie ustawieniami PIM i przypisywanie elementów członkowskich, w menu u góry **Privileged Identity Management - zasobów Azure** okienku wybierz **Odśwież** przycisku.

![Przycisk "Odśwież" w górnym menu "Privileged Identity Management - zasobów platformy Azure" okienko](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie ustawień roli](pim-resource-roles-configure-role-settings.md)
- [Przypisywanie ról usługi PIM](pim-resource-roles-assign-roles.md)
