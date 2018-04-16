---
title: "Żądań zwiększenia przydziału vCPU usługi Azure Resource Manager | Dokumentacja firmy Microsoft"
description: "Żądań zwiększenia limitu przydziału vCPU usługi Azure Resource Manager"
author: ganganarayanan
ms.author: gangan
ms.date: 1/18/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c22a6dde0067385a1bf8d889cc76178bb44dd0ac
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2017
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Żądań zwiększenia limitu przydziału vCPU Menedżera zasobów

Przydziały Menedżera zasobów vCPU są wymuszane na poziomie regionu i poziomu rodziny SKU.
Więcej informacji na temat sposobu przydziały są wymuszane na [subskrypcji platformy Azure i ograniczenia usługi](http://aka.ms/quotalimits) strony.
Aby dowiedzieć się więcej na temat rodziny SKU, może porównania kosztów i wydajności na [cennik maszyn wirtualnych](http://aka.ms/pricingcompute) strony.

Aby zażądać zwiększenia, należy utworzyć przydział sprawy pomocy technicznej dla Vcpu w portalu Azure [https://portal.azure.com](https://portal.azure.com).

> [!NOTE]
> Dowiedz się, jak [Utwórz żądanie obsługi](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) w portalu Azure

1. Na stronie nowe żądanie pomocy technicznej wybierz typ problemu jako "Przydziału" i typ przydziału jako "Rdzeni".

    ![Blok podstawowych ustawień limitu przydziału](./media/resource-manager-core-quotas-request/Basics-blade.png)

2. Wybierz model wdrożenia jako "Resource Manager" i wybierz lokalizację.

    ![Przydział Problem bloku](./media/resource-manager-core-quotas-request/Problem-step.png)

3. Wybierz rodziny SKU, która wymaga zwiększenia.

    ![Wybrane serii jednostki SKU](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. Wprowadź nowe limity, którą chcesz dla tej subskrypcji.

    ![Nowe żądanie przydział jednostki SKU](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- Aby usunąć wiersza, usuń zaznaczenie pola wyboru z listy rozwijanej rodziny SKU jednostka SKU, lub kliknij ikonę odrzucenia "x".
Po wprowadzeniu żądany limit przydziału dla każdej rodziny SKU, kliknij przycisk Dalej, na stronie krok Problem, aby kontynuować tworzenia żądania obsługi.
