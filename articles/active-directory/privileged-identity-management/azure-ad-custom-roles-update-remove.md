---
title: Aktualizowanie lub usuwanie roli niestandardowej usługi Azure AD — zarządzanie tożsamościami uprzywilejowanymi (PIM)
description: Jak zaktualizować lub usunąć zarządzanie tożsamościami uprzywilejowanymi (AZURE AD) (Azure AD)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad5747be47e250cf9c623cc40d21d12c91ee16f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499104"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Aktualizowanie lub usuwanie przypisanej roli niestandardowej usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi

W tym artykule opisano, jak używać zarządzania tożsamościami uprzywilejowanymi (PIM) do aktualizowania lub usuwania przydziału tylko w czasie i czasie do ról niestandardowych utworzonych do zarządzania aplikacjami w usłudze Azure Active Directory (Azure AD) środowiska administracyjnego. 

- Aby uzyskać więcej informacji na temat tworzenia ról niestandardowych do delegowania zarządzania aplikacjami w usłudze Azure AD, zobacz [Role administratora niestandardowego w usłudze Azure Active Directory (wersja zapoznawcza)](../users-groups-roles/roles-custom-overview.md). 
- Jeśli nie używano jeszcze zarządzania tożsamościami uprzywilejowanymi, uzyskaj więcej informacji na [temat Rozpocznij korzystanie z zarządzania tożsamościami uprzywilejowanymi](pim-getting-started.md).

> [!NOTE]
> Role niestandardowe usługi Azure AD nie są zintegrowane z wbudowanymi rolami katalogów podczas podglądu. Gdy możliwości są ogólnie dostępne, zarządzanie rolami odbędzie się w doświadczeniu wbudowanych ról. Jeśli widzisz następujący baner, te role powinny być zarządzane [w doświadczeniu wbudowanych ról](pim-how-to-add-role-to-user.md) i ten artykuł nie ma zastosowania:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="update-or-remove-an-assignment"></a>Aktualizowanie lub usuwanie przydziału

Wykonaj następujące kroki, aby zaktualizować lub usunąć istniejące przypisanie roli niestandardowej.

1. Zaloguj się do [zarządzania tożsamościami uprzywilejowanymi](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) w witrynie Azure portal przy za pomocą konta użytkownika przypisanego do roli administratora ról uprzywilejowanych.
1. Wybierz **role niestandardowe usługi Azure AD (Wersja zapoznawcza)**.

    ![Wybieranie w wersji zapoznawczej ról niestandardowych usługi Azure AD, aby wyświetlić kwalifikujące się przydziały ról](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Wybierz **role,** aby wyświetlić listę **przypisań** ról niestandardowych dla aplikacji usługi Azure AD.

    ![Wybierz role zobacz listę kwalifikujących się przypisań ról](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. Wybierz rolę, którą chcesz zaktualizować lub usunąć.
1. Znajdź przypisanie roli na kartach **Kwalifikujące się role** lub **Aktywne role.**
1. Wybierz **pozycję Aktualizuj** lub **Usuń,** aby zaktualizować lub usunąć przypisanie roli.

    ![Wybierz pozycję Usuń lub zaktualizuj w przypisaniu kwalifikującej się roli](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>Następne kroki

- [Aktywowanie roli niestandardowej usługi Azure AD](azure-ad-custom-roles-assign.md)
- [Przypisywanie roli niestandardowej usługi Azure AD](azure-ad-custom-roles-assign.md)
- [Konfigurowanie przypisania niestandardowego zadania usługi Azure AD](azure-ad-custom-roles-configure.md)
