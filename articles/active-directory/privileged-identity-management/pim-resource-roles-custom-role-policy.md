---
title: Używanie ról niestandardowych dla zasobów platformy Azure w usłudze PIM — Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać ról niestandardowych dla zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbe08cff2b57155f8f3315f5d3454abfbdad47a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847062"
---
# <a name="use-custom-roles-for-azure-resources-in-privileged-identity-management"></a>Używanie ról niestandardowych zasobów platformy Azure w dziale Zarządzanie tożsamościami uprzywilejowanymi

Może być konieczne zastosowanie ścisłych ustawień zarządzania tożsamością uprzywilejowaną (PIM) do niektórych użytkowników w roli uprzywilejowanej w organizacji usługi Azure Active Directory (Azure AD), zapewniając jednocześnie większą autonomię dla innych. Rozważmy na przykład scenariusz, w którym organizacja zatrudnia kilku współpracowników umowy, aby pomóc w rozwoju aplikacji, która będzie uruchamiana w ramach subskrypcji platformy Azure.

Jako administrator zasobów chcesz, aby pracownicy mogli uzyskać dostęp bez konieczności zatwierdzania. Jednak wszyscy pracownicy kontraktowi muszą zostać zatwierdzeni, gdy zażądają dostępu do zasobów organizacji.

Wykonaj kroki opisane w następnej sekcji, aby skonfigurować ukierunkowane ustawienia zarządzania tożsamościami uprzywilejowanymi dla ról zasobów platformy Azure.

## <a name="create-the-custom-role"></a>Tworzenie roli niestandardowej

Aby utworzyć rolę niestandardową dla zasobu, wykonaj kroki opisane w [obszarze Tworzenie ról niestandardowych dla kontroli dostępu opartej na rolach platformy Azure.](../role-based-access-control-custom-roles.md)

Podczas tworzenia roli niestandardowej należy dołączyć nazwę opisową, dzięki czemu można łatwo zapamiętać, która wbudowana rola ma zostać zduplikowana.

> [!NOTE]
> Upewnij się, że rola niestandardowa jest duplikatem wbudowanej roli, którą chcesz zduplikować i że jej zakres jest zgodny z wbudowaną rolą.

## <a name="apply-pim-settings"></a>Stosowanie ustawień pim

Po utworzeniu roli w organizacji usługi Azure AD przejdź do strony **Zarządzanie tożsamościami uprzywilejowanymi — zasoby platformy Azure** w witrynie Azure portal. Wybierz zasób, do których odnosi się rola.

![Okienko "Zarządzanie tożsamościami uprzywilejowanymi — zasoby platformy Azure"](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Skonfiguruj ustawienia roli Zarządzanie tożsamościami uprzywilejowanymi,](pim-resource-roles-configure-role-settings.md) które powinny mieć zastosowanie do tych członków roli.

Na koniec [przypisz role](pim-resource-roles-assign-roles.md) do odrębnej grupy członków, które mają być kierowane z tymi ustawieniami.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ustawień roli zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-configure-role-settings.md)
- [Role niestandardowe na platformie Azure](../../role-based-access-control/custom-roles.md)
