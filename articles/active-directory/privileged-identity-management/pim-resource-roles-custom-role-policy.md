---
title: Użyj niestandardowych ról do ustawienia Privileged Identity Management obiektu docelowego dla zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania niestandardowych ról dla zasobów platformy Azure przy użyciu usługi PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: e3df34e761f17aa3c1949af390e57360d84a304f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="use-custom-roles-to-target-privileged-identity-management-settings"></a>Użyj niestandardowych ról do ustawień obiektu docelowego Privileged Identity Management

Konieczne może być rygorystyczne zarządzania tożsamości uprzywilejowanych (PIM) dotyczą niektóre elementy członkowskie roli, zapewniając większą niezależność dla innych użytkowników. Rozważmy scenariusz, w którym organizacja wynajmuje kilka stowarzyszonych kontraktu w rozwoju aplikacji, który zostanie wykonany w subskrypcji platformy Azure.

Administrator zasobów ma pracownikom na kwalifikować się do dostępu bez konieczności zatwierdzenia. Jednak wszystkie skojarzone kontraktu musi być zatwierdzony próbujące uzyskać dostęp do zasobów organizacji.

Wykonaj kroki opisane w następnej sekcji, aby skonfigurować docelowe PIM ustawienia dla ról zasobów platformy Azure.

## <a name="create-the-custom-role"></a>Tworzenie niestandardowych ról

Aby utworzyć niestandardową rolę zasobu, wykonaj czynności opisane w [Tworzenie niestandardowych ról dla kontroli dostępu](../role-based-access-control-custom-roles.md).

Po utworzeniu niestandardowej roli zabezpieczeń zawierają nazwę opisową, dlatego możesz łatwe do zapamiętania wbudowanej roli mają zostać zduplikowane.

> [!NOTE]
> Upewnij się, że rola niestandardowa jest duplikatem wbudowanej roli, które mają zostać zduplikowane oraz że jej zakres zgodny z wbudowanych ról.

## <a name="apply-pim-settings"></a>Zastosuj ustawienia usługi PIM

Po utworzeniu roli w dzierżawie w portalu Azure, przejdź do **Privileged Identity Management - zasobów Azure** okienka. Wybierz zasób, którego dotyczy roli.

!["Privileged Identity Management - zasobów platformy Azure" okienko](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[Skonfiguruj ustawienia roli PIM](pim-resource-roles-configure-role-settings.md) który stosuje się do tych członków roli.

Na koniec [Przypisz role](pim-resource-roles-assign-roles.md) grupie unikatowych elementów członkowskich, które ma być docelowa przy użyciu tych ustawień.

## <a name="next-steps"></a>Kolejne kroki

[Właściciele subskrypcji przeglądu i dostępem](pim-resource-roles-perform-access-review.md)
