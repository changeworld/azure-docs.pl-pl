---
title: Dodawanie lub zmienianie Administratorzy subskrypcji platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak dodać lub zmienić administratora subskrypcji platformy Azure przy użyciu kontroli dostępu opartej na rolach (RBAC).
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
ms.openlocfilehash: 6cc965f8e775e02e9dec9f610516739a9a2c1936
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127721"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Dodawanie lub zmienianie Administratorzy subskrypcji platformy Azure

Zarządzanie dostępem do zasobów platformy Azure wymaga odpowiedniej roli administratora. Platforma Azure oferuje system autoryzacji o nazwie kontroli dostępu opartej na rolach (RBAC) z kilku wbudowanych ról, których mogą wybierać. Można przypisać te role w różnych zakresach, takich jak grupy zarządzania, subskrypcji lub grupy zasobów.

Firma Microsoft zaleca zarządzanie dostępem do zasobów przy użyciu funkcji RBAC. Jednak jeśli nadal przy użyciu klasycznego modelu wdrażania i zarządzania klasycznymi zasobami przy użyciu [moduł programu PowerShell Azure usługi](https://docs.microsoft.com/en-us/powershell/module/servicemanagement/azure), będziesz musiał użyć klasycznego administratora. 

> [!TIP]
> Jeśli używasz tylko witryny Azure portal do zarządzania zasobami klasycznymi, nie trzeba używać klasycznego administratora.

Aby uzyskać więcej informacji, zobacz [usługi Azure Resource Manager, a wdrożeniem klasycznym](../azure-resource-manager/resource-manager-deployment-model.md) i [Administratorzy subskrypcji platformy Azure classic](../role-based-access-control/classic-administrators.md).

W tym artykule opisano jak dodać lub zmienić roli administratora dla użytkownika przy użyciu funkcji RBAC w zakresie subskrypcji.

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Przypisywanie użytkownika jako administratora subskrypcji

Aby użytkownik jest administratorem subskrypcji platformy Azure, należy przypisać je [właściciela](../role-based-access-control/built-in-roles.md#owner) roli (rola RBAC) w zakresie subskrypcji. Rola właściciela zapewnia pełny dostęp użytkownika do wszystkich zasobów w ramach subskrypcji, łącznie z prawej strony można delegować dostępu do innych osób. Te kroki są takie same jak inne przypisania roli.

1. W witrynie Azure portal Otwórz [subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Kliknij subskrypcję, w którym chcesz udzielić dostępu.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij przycisk **przypisań ról** kartę, aby wyświetlić wszystkie przypisania roli dla tej subskrypcji.

    ![Zrzut ekranu pokazujący przypisań ról](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. Kliknij przycisk **Dodaj** > **Dodaj przypisanie roli** otworzyć **Dodaj przypisanie roli** okienka.

    Jeśli nie masz uprawnienia do przypisywania ról, opcja zostanie wyłączona.

1. W **roli** listy rozwijanej wybierz **właściciela** roli.

1. W **wybierz** , wybierz użytkownika na liście. Jeśli nie widzisz użytkownika na liście, należy wpisać w **wybierz** pole, aby przeszukać katalog nazw wyświetlanych i adresów e-mail.

    ![Zrzut ekranu przedstawiający rolę właściciela, zaznaczone](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. Kliknij przycisk **Zapisz** Aby przypisać rolę.

    Po kilku chwilach użytkownik przypisany do roli właściciela w zakresie subskrypcji.

## <a name="next-steps"></a>Kolejne kroki

* [Czym jest kontrola dostępu oparta na rolach (RBAC)?](../role-based-access-control/overview.md)
* [Understand the different roles in Azure (Omówienie ról na platformie Azure)](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Instrukcje: Kojarzenie lub dodawanie subskrypcji platformy Azure do usługi Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Uprawnienia ról administratorów w usłudze Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
