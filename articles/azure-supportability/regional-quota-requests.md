---
title: Żądania wzrostu przydziału regionalnego platformy Azure | Microsoft Docs
description: Żądania wzrostu limitu przydziału regionalnego
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 132cf6ccfec5af9951f5dc6d6a3c6d3c81363d81
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850010"
---
# <a name="standard-quota-regional-vcpu-limit-increase"></a>Przydział standardowy: Zwiększ limit vCPU regionalny 

Menedżer zasobów obsługuje dwa typy przydziałów vCPU dla maszyn wirtualnych. **Maszyny wirtualne z opcją płatność zgodnie z rzeczywistym** użyciem i **zarezerwowane wystąpienia maszyn wirtualnych** używają standardowego limitu przydziału. **Miejsca na maszynach wirtualnych** używają limitu przydziału miejsca. 

Standardowy przydział vCPU dla wystąpień z opcją płatność zgodnie z rzeczywistym użyciem i zarezerwowane wystąpienia maszyn wirtualnych są wymuszane w dwóch warstwach dla każdej subskrypcji w każdym regionie.
 
Pierwsza warstwa to **łączny limit procesorów wirtualnych vCPU regionalnych** (dla wszystkich serii maszyn wirtualnych), a druga warstwa to **Limit procesorów wirtualnych vCPU dla serii maszyn wirtualnych** (na przykład procesorów wirtualnych vCPU serii D). Gdy zostanie wdrożona nowa maszyna wirtualna, suma nowych i istniejących procesorów wirtualnych vCPU użycie dla tej serii maszyn wirtualnych nie może przekraczać limitu przydziału vCPU zatwierdzonego dla danej serii maszyn wirtualnych. Dodatkowo całkowita i istniejąca liczba vCPU wdrożona na wszystkich seriach maszyn wirtualnych nie powinna przekraczać całkowitego przydziału regionalnego procesorów wirtualnych vCPU zatwierdzonego dla subskrypcji. Jeśli jeden z tych przydziałów zostanie przekroczony, wdrożenie maszyny wirtualnej nie będzie dozwolone. Można zażądać zwiększenia limitu przydziału procesorów wirtualnych vCPU dla serii maszyn wirtualnych z Azure Portal. Zwiększenie limitu przydziału serii maszyn wirtualnych powoduje automatyczne zwiększenie łącznej granicy procesorów wirtualnych vCPU w regionie o tej samej wartości.

Po utworzeniu nowej subskrypcji domyślna łączna liczba procesorów wirtualnych vCPU regionalnych może nie być równa sumie domyślnych przydziałów vCPU dla wszystkich poszczególnych serii maszyn wirtualnych. Może to skutkować subskrypcją z wystarczającym limitem przydziału dla każdej pojedynczej serii maszyn wirtualnych, która ma zostać wdrożona, ale nie jest wystarczająca do całkowitego procesorów wirtualnych vCPU regionalnych dla wszystkich wdrożeń. W takim przypadku musisz przesłać żądanie, aby w sposób jawny zwiększyć łączny limit procesorów wirtualnych vCPU regionalny. Łączny limit procesorów wirtualnych vCPU regionalnych nie może przekroczyć sumy zatwierdzonego przydziału dla całej serii maszyn wirtualnych dla regionu.

Dowiedz się więcej o standardowych przydziałach vCPU na [stronie limit przydziału vCPU maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) oraz na stronie [limity subskrypcji i usług platformy Azure](https://aka.ms/quotalimits) .

Dowiedz się więcej na temat **zwiększania liczby dodatkowych limitów vCPU maszyn wirtualnych** [.](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)

Możesz poprosić o zwiększenie **standardowego limitu regionalnej maszyny wirtualnej w vCPU** , za pośrednictwem bloku **Pomoc i obsługa techniczna** oraz bloku **użycie i przydział** w portalu.

## <a name="request-standard-quota-regional-vcpu-limit-increase-at-subscription-level-using-the-help--support-blade"></a>Żądaj vCPU limitu przydziału w warstwie Standardowa na poziomie subskrypcji przy użyciu bloku pomoc i obsługa techniczna

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej za pomocą bloku "pomoc i obsługa techniczna platformy Azure" dostępnego w Azure Portal. 

1. W obszarze https://portal.azure.com wybierz pozycję **Pomoc i obsługa techniczna**.

![Pomoc i obsługa techniczna](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Wybierz pozycję **Nowe żądanie obsługi**. 

![Nowe żądanie pomocy technicznej](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Z listy rozwijanej Typ problemu wybierz pozycję **usługi i limity subskrypcji (przydziały)** .

![Lista rozwijana typ problemu](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

![Wybieranie Aktualności subskrypcji](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Wybierz pozycję **inne żądania** na liście rozwijanej **Typ limitu przydziału** .

![Typ limitu przydziału](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. W okienku **szczegółów** podaj dodatkowe informacje zgodnie z poniższym przykładem, aby pomóc w przetwarzaniu żądania i kontynuować tworzenie przypadku. 
    1.  **Model wdrażania** — określ "Menedżer zasobów"
    2.  **Żądany region** — określ wymagany region, np. Wschodnie stany USA 2
    3.  **Nowa wartość limitu** — Określ limit dla nowego regionu. Ta subskrypcja nie powinna przekroczyć sumy zatwierdzonego przydziału dla poszczególnych rodzin jednostek SKU

![Szczegóły przydziału](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Zażądaj całkowitego przydziału regionalnej procesorów wirtualnych vCPU na poziomie subskrypcji przy użyciu bloku **użycia i limitu przydziału**

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej za pomocą bloku "użycie i limit przydziału" platformy Azure dostępnego w Azure Portal. 

1. W obszarze https://portal.azure.com wybierz pozycję **subskrypcje**.

![Subskrypcje](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

![Wybierz subskrypcję](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Wybierz **użycie i przydziały**

![Wybierz użycie i przydziały](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. W prawym górnym rogu wybierz pozycję **Zwiększ liczbę żądań**.

![Zwiększ liczbę żądań](./media/resource-manager-core-quotas-request/request-increase.png)

5. Wybierz pozycję **inne żądania** na liście rozwijanej **Typ limitu przydziału** .

![Typ limitu przydziału](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. W okienku **szczegółów** podaj dodatkowe informacje zgodnie z poniższym przykładem, aby pomóc w przetwarzaniu żądania i kontynuować tworzenie przypadku. 
    1.  **Model wdrażania** — określ "Menedżer zasobów"
    2.  **Żądany region** — określ wymagany region, np. Wschodnie stany USA 2
    3.  **Nowa wartość limitu** — Określ limit dla nowego regionu. Ta subskrypcja nie powinna przekroczyć sumy zatwierdzonego przydziału dla poszczególnych rodzin jednostek SKU

![Szczegóły przydziału](./media/resource-manager-core-quotas-request/regional-details.png)



