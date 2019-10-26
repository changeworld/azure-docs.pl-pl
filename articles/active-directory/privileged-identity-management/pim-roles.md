---
title: Role, którymi nie można zarządzać w Privileged Identity Management Azure Active Directory | Microsoft Docs
description: Opisuje role, którymi nie można zarządzać w programie Azure AD Privileged Identity Management (PIM).
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
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895204"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Role, którymi nie można zarządzać w Privileged Identity Management

Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) umożliwia zarządzanie wszystkimi [rolami usługi Azure AD](../users-groups-roles/directory-assign-admin-roles.md) i wszystkimi [rolami zasobów platformy Azure](../../role-based-access-control/built-in-roles.md). Role te obejmują również niestandardowe role dołączone do grup zarządzania, subskrypcji, grup zasobów i zasobów. Istnieje jednak kilka ról, których nie można zarządzać. W tym artykule opisano role, którymi nie można zarządzać w programie Privileged Identity Management.

## <a name="classic-subscription-administrator-roles"></a>Role klasycznego administratora subskrypcji

W Privileged Identity Management nie można zarządzać następującymi rolami klasycznych administratorów subskrypcji:

- Administrator konta
- Administrator usługi
- Współadministrator

Aby uzyskać więcej informacji na temat ról administratora klasycznej subskrypcji, zobacz Role [administratora subskrypcji klasycznej, role RBAC platformy Azure i role administratorów usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Jakie są role administratorów pakietu Office 365?

Role w usłudze Exchange Online lub SharePoint Online, z wyjątkiem administratorów programu Exchange i administrator programu SharePoint, nie są reprezentowane w usłudze Azure AD i nie mogą być zarządzane w Privileged Identity Management. Aby uzyskać więcej informacji na temat tych usług Office 365, zobacz [role administratora pakietu office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Administrator programu SharePoint ma dostęp administracyjny do usługi SharePoint Online za pomocą Centrum administracyjnego usługi SharePoint Online i może wykonywać niemal każde zadanie w usłudze SharePoint Online. Uprawnieni użytkownicy mogą napotkać opóźnienia przy użyciu tej roli w programie SharePoint po aktywowaniu w programie Privileged Identity Management.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról usługi Azure AD w Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Przypisywanie ról zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-assign-roles.md)
