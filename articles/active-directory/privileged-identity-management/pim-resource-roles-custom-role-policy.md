---
title: Używanie ról niestandardowych dla zasobów platformy Azure w programie PIM-Azure Active Directory | Microsoft Docs
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
ms.date: 03/30/2018
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d36514c97cf1f45ee0a435d3b716019d2762e5a
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804177"
---
# <a name="use-custom-roles-for-azure-resources-in-pim"></a>Używanie ról niestandardowych dla zasobów platformy Azure w usłudze PIM

Może być konieczne zastosowanie rygorystycznych ustawień usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) do niektórych członków roli, a jednocześnie zapewnianie większej autonomii dla innych osób. Rozważmy scenariusz, w którym organizacja zatrudnia kilka jednostek kontraktu, aby pomóc w rozwoju aplikacji, która będzie działać w ramach subskrypcji platformy Azure.

Jako administrator zasobów chcesz, aby pracownicy mogli uzyskać dostęp bez konieczności zatwierdzania. Jednak wszystkie stowarzyszone kontrakty muszą zostać zatwierdzone, jeśli żądają dostępu do zasobów organizacji.

Wykonaj kroki opisane w następnej sekcji, aby skonfigurować ustawienia usługi PIM dla ról zasobów platformy Azure.

## <a name="create-the-custom-role"></a>Utwórz rolę niestandardową

Aby utworzyć rolę niestandardową dla zasobu, wykonaj kroki opisane w temacie [Tworzenie ról niestandardowych dla Access Control opartej na rolach na platformie Azure](../role-based-access-control-custom-roles.md).

Podczas tworzenia roli niestandardowej należy uwzględnić opisową nazwę, aby można było łatwo zapamiętać, która wbudowana rola została zaprojektowana.

> [!NOTE]
> Upewnij się, że rola niestandardowa jest duplikatem wbudowanej roli, którą chcesz zduplikować, i że jej zakres jest zgodny z wbudowaną rolą.

## <a name="apply-pim-settings"></a>Zastosuj ustawienia PIM

Po utworzeniu roli w dzierżawie w Azure Portal przejdź do okienka **zasoby Privileged Identity Management — Azure** . Wybierz zasób, do którego zostanie zastosowana rola.

![Okienko "Privileged Identity Management — zasoby platformy Azure"](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Skonfiguruj ustawienia roli PIM](pim-resource-roles-configure-role-settings.md) , które powinny być stosowane do tych członków roli.

Na koniec [Przypisz role](pim-resource-roles-assign-roles.md) do odrębnej grupy elementów członkowskich, które mają być używane z tymi ustawieniami.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM](pim-resource-roles-configure-role-settings.md)
- [Role niestandardowe na platformie Azure](../../role-based-access-control/custom-roles.md)
