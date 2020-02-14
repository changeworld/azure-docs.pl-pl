---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 9054340799ac6f95a9a3064351193f0680a000c9
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193094"
---
|Name (Nazwa) |Opis |Efekt (s) |Wersja |
|---|---|---|---|
|[Allowed locations](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) (Dozwolone lokalizacje) |Ta zasada umożliwia ograniczenie lokalizacji, które organizacja może określić podczas wdrażania zasobów. Służy do wymuszania wymagań dotyczących zgodności obszarów geograficznych. Nie obejmuje grup zasobów, elementu Microsoft.AzureActiveDirectory/b2cDirectories i zasobów używających regionu „globalny”. |pozbawić |1.0.0 |
|[Dozwolone lokalizacje grup zasobów](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |Te zasady pozwalają ograniczyć lokalizacje, w których organizacja może tworzyć grupy zasobów w programie. Służy do wymuszania wymagań dotyczących zgodności obszarów geograficznych. |pozbawić |1.0.0 |
|[Allowed resource types](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) (Dozwolone typy zasobów) |Te zasady umożliwiają określenie typów zasobów, które organizacja może wdrażać. Te zasady mają wpływ tylko na typy zasobów obsługujące Tagi "Tags" i "Location". Aby ograniczyć wszystkie zasoby, należy zduplikować te zasady i zmienić wartość "Mode" na "All". |pozbawić |1.0.0 |
|[Lokalizacja zasobów inspekcji jest zgodna z lokalizacją grupy zasobów](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |Inspekcja lokalizacji zasobu odpowiadającej lokalizacji grupy zasobów |wizyjn |1.0.0 |
|[Inspekcja użycia niestandardowych reguł RBAC](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |Inspekcja wbudowanych ról, takich jak "Owner, współautorzy, czytelnik" zamiast niestandardowych ról RBAC, które są podatne na błędy. Używanie ról niestandardowych jest traktowane jako wyjątek i wymaga rygorystycznego przeglądu i modelowania zagrożeń |Inspekcja, wyłączona |1.0.0 |
|[Role niestandardowego właściciela subskrypcji nie powinny istnieć](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |Te zasady zapewniają, że nie istnieją żadne role niestandardowego właściciela subskrypcji. |Inspekcja, wyłączona |1.0.0 |
|[Not allowed resource types](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) (Niedozwolone typy zasobów) |Te zasady umożliwiają określenie typów zasobów, których organizacja nie może wdrażać. |Zablokuj |1.0.0 |
