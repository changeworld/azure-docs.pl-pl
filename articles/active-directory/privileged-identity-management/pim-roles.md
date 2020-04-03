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
ms.date: 03/31/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6eaa50f57dd8037ef0ad96b69284f565bd3558f
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607519"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Role, którymi nie można zarządzać w zarządzania tożsamościami uprzywilejowanymi

Zarządzanie tożsamościami uprzywilejowanymi usługi Azure Directory (Azure AD) (PIM) umożliwia zarządzanie wszystkimi [rolami usługi Azure AD](../users-groups-roles/directory-assign-admin-roles.md) i [wszystkimi rolami platformy Azure.](../../role-based-access-control/built-in-roles.md) Role platformy Azure mogą również zawierać role niestandardowe dołączone do grup zarządzania, subskrypcji, grup zasobów i zasobów. Istnieje jednak kilka ról, których nie można zarządzać. W tym artykule opisano role, którymi nie można zarządzać w zarządzaniu tożsamościami uprzywilejowanymi.

## <a name="classic-subscription-administrator-roles"></a>Role klasycznego administratora subskrypcji

Nie można zarządzać następującymi klasycznymi rolami administratora subskrypcji w zarządzania tożsamościami uprzywilejowanymi:

- Administrator konta
- Administrator usługi
- Współadministrator

Aby uzyskać więcej informacji na temat ról administratora subskrypcji klasycznej, zobacz [Klasyczne role administratora subskrypcji, role usługi Azure RBAC i role administratora usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Co z rolami administratora usługi Office 365?

Obsługujemy wszystkie role usługi Office365 w środowisku portalu Role usługi Azure AD i Administratorzy, takie jak Administrator programu Exchange i Administrator programu SharePoint, ale nie obsługujemy określonych ról w programie Exchange RBAC lub sharepoint RBAC. Aby uzyskać więcej informacji na temat tych usług Office 365, zobacz [Role administratora usługi Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Uprawnieni użytkownicy do roli administratora programu SharePoint, a także do wszystkich ról próbujących uzyskać dostęp do Centrum zabezpieczeń i zgodności firmy Microsoft mogą wystąpić opóźnienia do kilku godzin po aktywowaniu ich roli. Pracujemy z tymi zespołami, aby rozwiązać problemy.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-add-role-to-user.md)
- [Przypisywanie ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-assign-roles.md)
