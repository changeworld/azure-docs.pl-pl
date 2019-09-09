---
title: Role, którymi nie można zarządzać w programie PIM-Azure Active Directory | Microsoft Docs
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
ms.date: 01/18/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: d66d433d9de537358777e54e3c7d5489c25c849b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804086"
---
# <a name="roles-you-cannot-manage-in-pim"></a>Role, którymi nie można zarządzać w usłudze PIM

Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) umożliwia zarządzanie wszystkimi [rolami usługi Azure AD](../users-groups-roles/directory-assign-admin-roles.md) i wszystkimi [rolami zasobów platformy Azure](../../role-based-access-control/built-in-roles.md). Role te obejmują również niestandardowe role dołączone do grup zarządzania, subskrypcji, grup zasobów i zasobów. Istnieje jednak kilka ról, których nie można zarządzać. W tym artykule opisano role, którymi nie można zarządzać w programie PIM.

## <a name="classic-subscription-administrator-roles"></a>Role klasycznych administratorów subskrypcji

W programie PIM nie można zarządzać następującymi rolami klasycznych administratorów subskrypcji:

- Administrator konta
- Administrator usługi
- Współadministrator

Aby uzyskać więcej informacji na temat ról administratora klasycznej subskrypcji, zobacz Role [administratora subskrypcji klasycznej, role RBAC platformy Azure i role administratorów usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Jakie są role administratorów pakietu Office 365?

Role w usłudze Exchange Online lub SharePoint Online, z wyjątkiem administratorów programu Exchange i administratora programu SharePoint, nie są reprezentowane w usłudze Azure AD i dlatego nie mogą być zarządzane w usłudze PIM. Aby uzyskać więcej informacji na temat tych usług Office 365, zobacz [role administratora pakietu office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Administrator programu SharePoint ma dostęp administracyjny do usługi SharePoint Online za pomocą Centrum administracyjnego usługi SharePoint Online i może wykonywać niemal każde zadanie w usłudze SharePoint Online. Uprawnieni użytkownicy mogą napotkać opóźnienia przy użyciu tej roli w programie SharePoint po aktywowaniu w programie PIM.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról usługi Azure AD w usłudze PIM](pim-how-to-add-role-to-user.md)
- [Przypisywanie ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-assign-roles.md)
