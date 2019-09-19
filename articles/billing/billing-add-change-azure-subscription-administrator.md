---
title: Dodawanie lub zmienianie administratorów subskrypcji platformy Azure | Microsoft Docs
description: Opisuje sposób dodawania lub zmieniania administratora subskrypcji platformy Azure przy użyciu kontroli dostępu opartej na rolach (RBAC).
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 000315a2d751a05d3e401ee1bb9f593c6e321194
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "64922895"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Dodawanie lub zmienianie administratorów subskrypcji platformy Azure

Zarządzanie dostępem do zasobów platformy Azure wymaga odpowiedniej roli administratora. Platforma Azure ma system autoryzacji zwany kontrolą dostępu opartą na rolach (RBAC) z kilkoma wbudowanymi rolami do wyboru. Te role można przypisywać do różnych zakresów, takich jak grupa zarządzania, subskrypcja lub grupa zasobów.

Firma Microsoft zaleca, aby zarządzać dostępem do zasobów przy użyciu ról RBAC. Jeśli jednak nadal korzystasz z klasycznego modelu wdrażania i zarządzasz zasobami klasycznymi przy użyciu [modułu programu PowerShell do zarządzania usługami platformy Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure), musisz użyć administratora klasycznego. 

> [!TIP]
> Jeśli do zarządzania zasobami klasycznymi używasz tylko witryny Azure Portal, nie musisz używać administratora klasycznego.

Aby uzyskać więcej informacji, zobacz [Porównanie usługi Azure Resource Manager i wdrożenia klasycznego](../azure-resource-manager/resource-manager-deployment-model.md) oraz [Klasyczni administratorzy subskrypcji platformy Azure](../role-based-access-control/classic-administrators.md).

W tym artykule opisano sposób dodawania lub zmieniania roli administratora dla użytkownika przy użyciu funkcji RBAC w zakresie subskrypcji.

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Przypisywanie użytkownika jako administratora subskrypcji

Aby nadać użytkownikowi uprawnienia administratora subskrypcji platformy Azure, przypisz do niego rolę [Właściciel](../role-based-access-control/built-in-roles.md#owner) (rolę RBAC) w zakresie subskrypcji. Rola właściciela daje użytkownikowi pełen dostęp do wszystkich zasobów w subskrypcji, w tym prawo do przydzielania dostępu innym osobom. Te kroki są takie same jak w przypadku innych przypisań ról.

1. W witrynie Azure Portal otwórz pozycję [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Kliknij subskrypcję, do której chcesz udzielić dostępu.

1. Kliknij pozycję **Kontrola dostępu (IAM)** .

1. Kliknij kartę **Przypisania ról**, aby wyświetlić wszystkie przypisania ról dla tej subskrypcji.

    ![Zrzut ekranu pokazujący przypisania ról](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. Kliknij pozycję **Dodaj** > **Dodaj przypisanie roli**, aby otworzyć okienko **Dodawanie przypisania roli**.

    Jeśli nie masz uprawnień do przypisywania ról, ta opcja będzie wyłączona.

1. Z listy rozwijanej **Rola** wybierz rolę **Właściciel**.

1. Z listy **Wybierz** wybierz użytkownika. Jeśli nie widzisz użytkownika na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane i adresy e-mail.

    ![Zrzut ekranu przedstawiający wybraną rolę właściciela](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. Kliknij przycisk **Zapisz**, aby przypisać rolę.

    Po kilku chwilach użytkownikowi zostanie przypisana rola właściciela w zakresie subskrypcji.

## <a name="next-steps"></a>Następne kroki

* [Czym jest kontrola dostępu oparta na rolach (RBAC)?](../role-based-access-control/overview.md)
* [Understand the different roles in Azure (Omówienie ról na platformie Azure)](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Instrukcje: Kojarzenie lub dodawanie subskrypcji platformy Azure do usługi Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Uprawnienia ról administratorów w usłudze Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.
