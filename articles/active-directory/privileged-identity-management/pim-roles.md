---
title: Role, którymi nie można zarządzać w zarządzania tożsamościami uprzywilejowanymi — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: W tym artykule opisano role, którymi nie można zarządzać w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fac7074cf85a585c93ece60be9eea8ffb9a6345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72895204"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Role, którymi nie można zarządzać w zarządzania tożsamościami uprzywilejowanymi

Zarządzanie tożsamościami uprzywilejowanymi usługi Azure Directory (Azure AD) (PIM) umożliwia zarządzanie wszystkimi [rolami usługi Azure AD](../users-groups-roles/directory-assign-admin-roles.md) i [wszystkimi rolami zasobów platformy Azure.](../../role-based-access-control/built-in-roles.md) Te role obejmują również role niestandardowe dołączone do grup zarządzania, subskrypcji, grup zasobów i zasobów. Istnieje jednak kilka ról, których nie można zarządzać. W tym artykule opisano role, którymi nie można zarządzać w zarządzaniu tożsamościami uprzywilejowanymi.

## <a name="classic-subscription-administrator-roles"></a>Role klasycznego administratora subskrypcji

Nie można zarządzać następującymi klasycznymi rolami administratora subskrypcji w zarządzania tożsamościami uprzywilejowanymi:

- Administrator konta
- Administrator usługi
- Współadministrator

Aby uzyskać więcej informacji na temat ról administratora subskrypcji klasycznej, zobacz [Klasyczne role administratora subskrypcji, role usługi Azure RBAC i role administratora usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Co z rolami administratora usługi Office 365?

Role w usłudze Exchange Online lub SharePoint Online, z wyjątkiem administratora programu Exchange i administratora programu SharePoint, nie są reprezentowane w usłudze Azure AD, a więc nie mogą być zarządzane w dziale Zarządzanie tożsamościami uprzywilejowanymi. Aby uzyskać więcej informacji na temat tych usług Office 365, zobacz [Role administratora usługi Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Administrator programu SharePoint ma dostęp administracyjny do usługi SharePoint Online za pośrednictwem centrum administracyjnego usługi SharePoint Online i może wykonywać niemal każde zadanie w usłudze SharePoint Online. Uprawnieni użytkownicy mogą doświadczać opóźnień podczas korzystania z tej roli w programie SharePoint po aktywacji w zarządzania tożsamościami uprzywilejowanymi.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-add-role-to-user.md)
- [Przypisywanie ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-assign-roles.md)
