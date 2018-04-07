---
title: Privileged Identity Management zasobów Azure - odnajdywania i zarządzania zasobami Azure | Dokumentacja firmy Microsoft
description: Opis sposobu ochrony zasobów platformy Azure.
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
ms.openlocfilehash: 78650e47ec92aa144e4ccc8c57f309240bf31ee3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="discover-and-manage-azure-resources"></a>Odnajdywanie i zarządzania zasobami Azure

Jeśli Twoja organizacja jest już chroniony za pomocą usługi Azure AD PIM administratorów w katalogu lub jesteś właścicielem subskrypcji wyszukiwania w celu zabezpieczenia zasobów produkcji jesteś w odpowiednim miejscu.

Po pierwszym włączeniu PIM dla zasobów platformy Azure, należy odnaleźć i wybierz zasobów chronionych przez usługi PIM. Nie ma żadnego limitu liczby zasobów, którymi można zarządzać z PIM, ale zaleca się najpierw uruchomić najważniejszych zasobów (środowisko produkcyjne).

> [!Note]
> Można przeszukiwane tylko zasoby subskrypcji i wybranych do zarządzania. Wybieranie do zarządzania subskrypcją w PIM spowoduje włączenie zarządzania, jak również wszystkich zasobów podrzędnych.

## <a name="discover-resources"></a>Odnajdywanie zasobów

Przejdź do usługi Azure AD PIM i w sekcji Zarządzanie menu nawigacji po lewej stronie wybierz zasobów platformy Azure.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Jeśli jest to pierwsza dla zasobów platformy Azure przy użyciu usługi PIM musisz uruchomić odnajdywanie w celu odnalezienia zasobów do zarządzania.
Kliknij przycisk "Odnajdywania zasobów" w Centrum ekranu, aby uruchomić proces odnajdowania.

![](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Jeśli inny zasób lub katalogu administrator w organizacji zarządza już zasobem platformy Azure z PIM lub przypisania roli kwalifikujących się do zasobu, widok listy będzie zawierać komunikat: "odnajdywania zasobów lub aktywować rolę kwalifikujących się Przypisanie kontynuowanie". 

![](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

Po wybraniu przycisku w pasku akcji lub w środku ekranu do odnajdowania zasobów zobaczysz listę dostępnych do zarządzania subskrypcji. W tym momencie Jeśli widzisz wyróżnione subskrypcje go wskazuje, że są one chronione przez usługi PIM.

> [!Note]
> Aby zapobiec inny administrator zasobów usuwanie ustawień PIM gdy subskrypcja jest zarządzany nie może być niezarządzane.

![](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

Umieść kursor nad subskrypcji, które chcesz chronić za pomocą usługi PIM i zaznacz pole przy lewej wiersza. Jednocześnie można wybrać wiele subskrypcji.

![](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Do zainicjowania procesu dołączania procesu kliknij przycisk "Zarządzanie zasobu" na pasku w górnej części ekranu.

![](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

Wybrane zasoby są teraz zarządzane przez usługi PIM. Zamknij ekranu odnajdywania przy użyciu "X" w prawym górnym rogu strony, a następnie kliknij przycisk Odśwież na pasku w górnej części ekranu zasobów zarządzania Azure, aby rozpocząć zarządzanie ustawieniami PIM i przypisywanie elementów członkowskich.

![](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie ustawień roli](pim-resource-roles-configure-role-settings.md)

[Przypisywanie ról usługi PIM](pim-resource-roles-assign-roles.md)
