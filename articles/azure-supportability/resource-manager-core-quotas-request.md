---
title: Limit przydziału usługi Azure Resource Manager procesorów vCPU zwiększyć żądań | Dokumentacja firmy Microsoft
description: Żądań zwiększenia limitu przydziału usługi Azure Resource Manager procesorów wirtualnych.
author: ganganarayanan
ms.author: gangan
ms.date: 6/13/2018
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 204deaf3a67984c0dd5eca5352686719e7767885
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60649353"
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Żądań zwiększenia limitu przydziału procesorów wirtualnych usługi Resource Manager

Limity przydziału procesorów wirtualnych Menedżera zasobów są wymuszane na poziomie regionu oraz poziomu rodziny SKU.
Dowiedz się więcej o jak przydziały są wymuszane na [limity usług i subskrypcji platformy Azure](https://aka.ms/quotalimits) strony.
Aby dowiedzieć się więcej na temat rodzin SKU, może być porównywanie kosztów i wydajności na [cennik usługi Virtual Machines](https://aka.ms/pricingcompute) strony.

Aby poprosić o zwiększenie, postępuj zgodnie z instrukcjami poniżej z użyciem, aby utworzyć żądanie pomocy technicznej za pośrednictwem platformy Azure "użycie i limit przydziału" bloku dostępne w witrynie Azure Portal. 

## <a name="request-quota-increase-at-subscription-level"></a>Zażądać zwiększenia limitu przydziału na poziomie subskrypcji

1. Z https://portal.azure.com, wybierz opcję **subskrypcje**.

   ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

   ![Wybieranie subskrypcji](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Wybierz **użycie i przydziały**

   ![Wybierz opcję użycie i przydziały](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. W prawym górnym rogu wybierz **zażądać zwiększenia**.

   ![Zażądaj zwiększenia](./media/resource-manager-core-quotas-request/request-increase.png)

5. Krok: 1 - wybierz **rdzeni** jako typ oferty. 

   ![Wypełnij formularz](./media/resource-manager-core-quotas-request/forms.png)
   
6. Krok: 2 — Wybieranie pozycji wdrożenie modelu jako "Menedżera zasobów" i wybierz lokalizację.

    ![Blok Problem limitu przydziału](./media/resource-manager-core-quotas-request/Problem-step.png)

3. Wybierz rodzin SKU, która wymaga zwiększenia.

    ![Serie jednostek SKU wybrane](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. Wprowadź nowe wartości graniczne, które Twoim zdaniem w ramach subskrypcji.

    ![Nowe żądanie limitu przydziału jednostki SKU](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- Aby usunąć wiersz, usuń zaznaczenie pola wyboru jednostki SKU z listy rozwijanej rodziny SKU, lub kliknij ikonę odrzucenia "x".
Po wprowadzeniu żądany limit przydziału dla każdej rodziny SKU, kliknij przycisk "Dalej" na stronie krok Problem, aby kontynuować tworzenie żądania pomocy technicznej.

