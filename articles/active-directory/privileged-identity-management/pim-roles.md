---
title: Role nie może zarządzać w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa5fb632ee5fd9c18bde7443e81fe2ef6e5335e4
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579550"
---
# <a name="roles-you-cannot-manage-in-pim"></a>Role nie można zarządzać w usłudze PIM

Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) umożliwia zarządzanie wszystkimi [ról usługi Azure AD](../users-groups-roles/directory-assign-admin-roles.md) i wszystkie [role zasobów platformy Azure](../../role-based-access-control/built-in-roles.md). Te role są także niestandardowe role dołączone do grupy zarządzania, subskrypcji, grupy zasobów i zasobów. Istnieje jednak kilka ról, które nie może zarządzać. W tym artykule opisano role, których nie można zarządzać w usłudze PIM.

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

- [Przypisz role usługi Azure AD w usłudze PIM](pim-how-to-add-role-to-user.md)
- [Przypisz role zasobów platformy Azure w usłudze PIM](pim-resource-roles-assign-roles.md)
