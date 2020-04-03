---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: 8a40f5f98117290a0c5afa53e965ba5e31628055
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80624201"
---
|Nazwa |Opis |Efekt(-y) |Wersja |GitHub |
|---|---|---|---|---|
|[Dozwolone lokalizacje](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe56962a6-4747-49cd-b67b-bf8b01975c4c) |Ta zasada umożliwia ograniczenie lokalizacji, które organizacja może określić podczas wdrażania zasobów. Służy do wymuszania wymagań dotyczących zgodności obszarów geograficznych. Nie obejmuje grup zasobów, elementu Microsoft.AzureActiveDirectory/b2cDirectories i zasobów używających regionu „globalny”. |Odmów |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedLocations_Deny.json)
|[Dozwolone lokalizacje dla grup zasobów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe765b5de-1225-4ba3-bd56-1ac6695af988) |Ta zasada umożliwia ograniczenie lokalizacji, w których organizacja może tworzyć grupy zasobów. Służy do wymuszania wymagań dotyczących zgodności obszarów geograficznych. |Odmów |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json)
|[Allowed resource types](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa08ec900-254a-4555-9bf5-e42af04b5c5c) (Dozwolone typy zasobów) |Ta zasada umożliwia określenie typów zasobów, które organizacja może wdrożyć. Ta zasada dotyczy tylko typów zasobów obsługujących "tagi" i "lokalizację". Aby ograniczyć wszystkie zasoby, zduplikuj tę zasadę i zmień "tryb" na "Wszystkie". |Odmów |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json)
|[Inspekcja lokalizacji zasobu pasuje do lokalizacji grupy zasobów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a914e76-4921-4c19-b460-a2d36003525a) |Inspekcja, czy lokalizacja zasobu jest zgodna z lokalizacją grupy zasobów |Inspekcji |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json)
|[Inspekcja użycia niestandardowych reguł RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Inspekcja wbudowanych ról, takich jak "Właściciel, Contributer, Reader" zamiast niestandardowych ról RBAC, które są podatne na błędy. Używanie ról niestandardowych jest traktowane jako wyjątek i wymaga rygorystycznego przeglądu i modelowania zagrożeń |Inspekcja, wyłączone |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json)
|[Role właściciela subskrypcji niestandardowej nie powinny istnieć](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Ta zasada gwarantuje, że nie istnieją żadne role właściciela subskrypcji niestandardowej. |Inspekcja, wyłączone |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json)
|[Niedozwolone typy zasobów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6c112d4e-5bc7-47ae-a041-ea2d9dccd749) |Ta zasada umożliwia określenie typów zasobów, których organizacja nie może wdrożyć. |Zablokuj |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json)
