---
title: Używanie ról niestandardowych dla zasobów platformy Azure w Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak używać ról niestandardowych dla zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e940dd0e4c653ed4921a9e8f245aab34107ba95
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895711"
---
# <a name="use-custom-roles-for-azure-resources-in-privileged-identity-management"></a>Używanie ról niestandardowych dla zasobów platformy Azure w Privileged Identity Management

Może być konieczne zastosowanie rygorystycznych ustawień Privileged Identity Management (PIM) dla niektórych użytkowników w roli uprzywilejowanej w organizacji Azure Active Directory (Azure AD), a jednocześnie zapewnianie większej autonomii dla innych osób. Rozważmy na przykład scenariusz, w którym organizacja zatrudnia kilka jednostek kontraktu, aby pomóc w rozwoju aplikacji, która będzie działać w ramach subskrypcji platformy Azure.

Jako administrator zasobów chcesz, aby pracownicy mogli uzyskać dostęp bez konieczności zatwierdzania. Jednak wszystkie stowarzyszone kontrakty muszą zostać zatwierdzone, jeśli żądają dostępu do zasobów organizacji.

Wykonaj kroki opisane w następnej sekcji, aby skonfigurować ustawienia Privileged Identity Management ustawień dla ról zasobów platformy Azure.

## <a name="create-the-custom-role"></a>Utwórz rolę niestandardową

Aby utworzyć rolę niestandardową dla zasobu, wykonaj kroki opisane w temacie [Tworzenie ról niestandardowych dla Access Control opartej na rolach na platformie Azure](../role-based-access-control-custom-roles.md).

Podczas tworzenia roli niestandardowej należy uwzględnić opisową nazwę, aby można było łatwo zapamiętać, która wbudowana rola została zaprojektowana.

> [!NOTE]
> Upewnij się, że rola niestandardowa jest duplikatem wbudowanej roli, którą chcesz zduplikować, i że jej zakres jest zgodny z wbudowaną rolą.

## <a name="apply-pim-settings"></a>Zastosuj ustawienia PIM

Po utworzeniu roli w organizacji usługi Azure AD przejdź do strony **Privileged Identity Management — zasoby platformy Azure** w Azure Portal. Wybierz zasób, do którego zostanie zastosowana rola.

![Okienko "Privileged Identity Management — zasoby platformy Azure"](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Skonfiguruj Privileged Identity Management ustawienia roli](pim-resource-roles-configure-role-settings.md) , które powinny być stosowane dla tych członków roli.

Na koniec [Przypisz role](pim-resource-roles-assign-roles.md) do odrębnej grupy elementów członkowskich, które mają być używane z tymi ustawieniami.

## <a name="next-steps"></a>Następne kroki

- [Skonfiguruj ustawienia roli zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Role niestandardowe na platformie Azure](../../role-based-access-control/custom-roles.md)
