---
title: Azure regionalnego przydziału zwiększyć żądań | Dokumentacja firmy Microsoft
description: Żądań zwiększenia limitu przydziału regionalne
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 4c5524cf450959db7055ca5d032c81f79ebac077
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083082"
---
# <a name="total-regional-vcpu-limit-increase"></a>Zwiększenie limitu całkowitej regionalnych procesorów wirtualnych 

Teraz można zażądać zwiększenia za pośrednictwem **Pomoc i obsługa techniczna** bloku lub **użycia i przydziałów** bloku w portalu. 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Żądanie całkowita regionalnych zwiększenia limitu przydziału procesorów wirtualnych na poziomie przy użyciu subskrypcji **Pomoc i obsługa techniczna** bloku

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej za pośrednictwem platformy Azure dostępne w witrynie Azure portal bloku "Pomoc i obsługa techniczna". 

1. Z https://portal.azure.com, wybierz opcję **Pomoc i obsługa techniczna**.

![Pomoc i obsługa techniczna](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Wybierz pozycję **Nowe żądanie obsługi**. 

![Nowe żądanie pomocy technicznej](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Z listy rozwijanej Typ problemu wybierz **limity usług i subskrypcji (przydziały)** .

![Menu rozwijanego Typ problemu](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

![Wybieranie subskrypcji newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Wybierz **innych żądań** w **typ limitu przydziału** listy rozwijanej.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. W **szczegóły** okienku, podaj dodatkowe informacje, jak w poniższym przykładzie, aby ułatwić proces żądania i kontynuować tworzenie przypadków. 
    1.  **Model wdrażania** — określ "Resource Manager"
    2.  **Żądany region** — Określ swój region wymagane, np. wschodnie stany USA 2
    3.  **Nowy limit wartości** — określ nowy limit regionu. Nie powinna przekraczać sumę zatwierdzonych limitu przydziału dla poszczególnych rodzin SKU dla tej subskrypcji

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Żądanie całkowita regionalnych zwiększenia limitu przydziału procesorów wirtualnych na poziomie przy użyciu subskrypcji **użycia i przydziałów** bloku

Postępuj zgodnie z instrukcjami poniżej z użyciem, aby utworzyć żądanie pomocy technicznej za pośrednictwem platformy Azure "użycie i limit przydziału" bloku dostępne w witrynie Azure portal. 

1. Z https://portal.azure.com, wybierz opcję **subskrypcje**.

![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

![Wybieranie subskrypcji](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Wybierz **użycie i przydziały**

![Wybierz opcję użycie i przydziały](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. W prawym górnym rogu wybierz **zażądać zwiększenia**.

![Zażądać zwiększenia](./media/resource-manager-core-quotas-request/request-increase.png)

5. Wybierz **innych żądań** w **typ limitu przydziału** listy rozwijanej.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. W **szczegóły** okienku, podaj dodatkowe informacje, jak w poniższym przykładzie, aby ułatwić proces żądania i kontynuować tworzenie przypadków. 
    1.  **Model wdrażania** — określ "Resource Manager"
    2.  **Żądany region** — Określ swój region wymagane, np. wschodnie stany USA 2
    3.  **Nowy limit wartości** — określ nowy limit regionu. Nie powinna przekraczać sumę zatwierdzonych limitu przydziału dla poszczególnych rodzin SKU dla tej subskrypcji

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



