---
title: Role nie może zarządzać w usłudze PIM — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano role, których nie można zarządzać w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/18/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: a349b50ce0c4e7c47979a6dee5694411a1d807c2
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982254"
---
# <a name="roles-you-cannot-manage-in-pim"></a>Role nie można zarządzać w usłudze PIM

Usługa Azure AD Privileged Identity Management (PIM) umożliwia zarządzanie wszystkimi [ról katalogu usługi Azure AD](../users-groups-roles/directory-assign-admin-roles.md) i wszystkie [role zasobów platformy Azure](../../role-based-access-control/built-in-roles.md). Te role są także niestandardowe role dołączone do grupy zarządzania, subskrypcji, grupy zasobów i zasobów. Istnieje jednak kilka ról, które nie może zarządzać. W tym artykule opisano role, których nie można zarządzać w usłudze PIM.

## <a name="classic-subscription-administrator-roles"></a>Role klasycznego administratora subskrypcji

Następujące role administratora klasyczni w usłudze PIM nie da się zarządzać:

- Administrator konta
- Administrator usługi
- Współadministrator

Aby uzyskać więcej informacji o rolach administratora klasyczni zobacz [ról administratora subskrypcji klasyczne, ról RBAC platformy Azure i role administratora usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Jak wygląda role administratora usługi Office 365?

Role w usłudze Exchange Online lub SharePoint Online, z wyjątkiem administratora programu Exchange i SharePoint administratora, nie są reprezentowane w usłudze Azure AD i dlatego nie mogą być zarządzane w usłudze PIM. Aby uzyskać więcej informacji na temat tych usług Office 365, zobacz [role administratora usługi Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Administrator programu SharePoint ma dostęp administracyjny do usługi SharePoint Online za pośrednictwem Centrum administracyjnego programu SharePoint Online i mogą wykonywać niemal wszystkich zadań w usłudze SharePoint Online. Uprawnieni użytkownicy mogą wystąpić opóźnienia, po aktywowaniu w usłudze PIM przy użyciu tej roli w programie SharePoint.

## <a name="next-steps"></a>Kolejne kroki

- [Przypisywanie ról katalogu usługi Azure AD w usłudze PIM](pim-how-to-add-role-to-user.md)
- [Przypisz role zasobów platformy Azure w usłudze PIM](pim-resource-roles-assign-roles.md)
