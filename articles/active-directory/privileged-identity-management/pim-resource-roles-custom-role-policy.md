---
title: Privileged Identity Management zasobów Azure - Użyj role niestandardowe ustawienia PIM docelowy | Dokumentacja firmy Microsoft
description: Opis sposobu użycia ról niestandardowych w PIM dla zasobów platformy Azure.
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
ms.openlocfilehash: 6336d99df1bbdd71c66a9757af1d9fb356a91bf6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-custom-roles-to-target-pim-settings"></a>Użyj niestandardowych ról do ustawienia usługi PIM obiektu docelowego

Może być konieczne zastosowanie PIM rygorystyczne dla niektórych członków roli, zapewniając większą niezależność dla innych użytkowników. Rozważmy scenariusz, w którym organizacja wynajmuje sevral kontraktu współpracowników do pomocy do tworzenia aplikacji, która jest uruchamiana w subskrypcji platformy Azure. 

Administrator zasobów chcesz pracownikom organizacji mają dostęp kwalifikujących się wymagane zatwierdzenie, ale wszystkie skojarzone kontraktu muszą uzyskać zatwierdzenie, gdy żądają aktywacji. Wykonaj kroki opisane poniżej konspekt procesu Włącz docelowe PIM ustawienia dla ról zasobów platformy Azure.

## <a name="create-the-custom-role"></a>Tworzenie niestandardowych ról

[Użyj tego przewodnika, aby utworzyć niestandardową rolę zasobu](../../role-based-access-control/custom-roles.md).

Zawierają nazwę opisową, dlatego możesz łatwe do zapamiętania wbudowanej roli mają zostać zduplikowane.

>[!NOTE]
>Upewnij się, że rola niestandardowa jest duplikatem rolę, której miała, a jej zakres odpowiada wbudowanej roli.

## <a name="apply-pim-settings"></a>Zastosuj ustawienia usługi PIM

Po utworzeniu roli w dzierżawie przejdź do usługi PIM i wybierz zasób, który obejmuje rolę.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[Skonfiguruj ustawienia roli PIM](pim-resource-roles-configure-role-settings.md) , powinny być stosowane do tych elementów członkowskich

Na koniec [Przypisz role](pim-resource-roles-assign-roles.md) grupie unikatowych elementów członkowskich, które ma zostać celem przy użyciu tych ustawień.

## <a name="next-steps"></a>Kolejne kroki

[Przejrzyj właściciele subskrypcji](pim-resource-roles-perform-access-review.md)
