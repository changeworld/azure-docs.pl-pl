---
title: Użyj ról niestandardowych do ustawienia Privileged Identity Management obiektu docelowego dla zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób używania ról niestandardowych dla zasobów platformy Azure za pomocą usługi PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: 03091b50eb4fb368ae0277344317b261991c4bd1
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622691"
---
# <a name="use-custom-roles-to-target-privileged-identity-management-settings"></a>Używanie ról niestandardowych do ustawienia Privileged Identity Management obiektu docelowego

Może być konieczne rygorystyczne Privileged Identity Management (PIM) dotyczą niektórych członków roli, zapewniając większą niezależność dla innych użytkowników. Rozważmy scenariusz, w którym organizacja zatrudnia kilka kojarzy umowy, aby pomóc w rozwoju aplikacji, która jest uruchamiana w subskrypcji platformy Azure.

Jako administrator zasobu ma pracownikom uprawnionych do dostępu bez konieczności zatwierdzania. Jednak wszystkie kojarzy kontraktu musi być zatwierdzony, gdy będą one żądać dostępu do zasobów organizacji.

Wykonaj kroki opisane w następnej sekcji, aby skonfigurować docelowych ustawień usługi PIM dla ról zasobów platformy Azure.

## <a name="create-the-custom-role"></a>Tworzenie roli niestandardowej

Aby utworzyć niestandardową rolę zasobu, wykonaj czynności opisane w [tworzenie ról niestandardowych dla kontroli dostępu](../role-based-access-control-custom-roles.md).

Podczas tworzenia roli niestandardowej obejmują nazwę opisową, dzięki czemu można łatwo zapamiętać wbudowaną rolę, która ma zduplikowane.

> [!NOTE]
> Upewnij się, że rola niestandardowa jest duplikatem rolę wbudowaną, którą chcesz zduplikować, i że jego zakres odpowiada wbudowana rola.

## <a name="apply-pim-settings"></a>Stosowanie ustawień usługi PIM

Po utworzeniu roli w swojej dzierżawie, w witrynie Azure portal przejdź do **Privileged Identity Management — zasoby platformy Azure** okienka. Wybierz zasób, który dotyczy roli.

!["Privileged Identity Management — zasoby platformy Azure" okienko](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[Konfigurowanie ustawień roli usługi PIM](pim-resource-roles-configure-role-settings.md) , stosuje się do tych członków roli.

Na koniec [Przypisz role](pim-resource-roles-assign-roles.md) różne grupy elementów członkowskich, które ma pod kątem przy użyciu tych ustawień.

## <a name="next-steps"></a>Kolejne kroki

[I właścicielom subskrypcji przeglądu dostępu](pim-resource-roles-perform-access-review.md)
