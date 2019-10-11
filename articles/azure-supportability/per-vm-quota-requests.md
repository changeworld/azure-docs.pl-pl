---
title: Żądania wzrostu przydziału vCPU na maszynie wirtualnej na platformie Azure | Microsoft Docs
description: żądania zwiększania limitu przydziału vCPU maszyny wirtualnej
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e99bdd92d4a1aab833a95943b22aaabe7f0daa1d
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248674"
---
# <a name="vm-series-vcpu-limit-increase"></a>Zwiększenie limitu procesorów wirtualnych serii maszyn wirtualnych

Menedżer zasobów przydziały vCPU dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych są wymuszane w dwóch warstwach dla każdej subskrypcji w każdym regionie. 

Pierwsza warstwa to **łączny limit procesorów wirtualnych vCPU regionalnych** (dla wszystkich serii maszyn wirtualnych), a druga warstwa to **Limit procesorów wirtualnych vCPU dla serii maszyn wirtualnych** (na przykład procesorów wirtualnych vCPU serii D). Za każdym razem, gdy zostanie wdrożona nowa maszyna wirtualna, suma nowych i istniejących procesorów wirtualnych vCPU użycie dla tej serii maszyn wirtualnych nie może przekroczyć limitu przydziału vCPU zatwierdzonego dla danej serii maszyn wirtualnych. Dodatkowo całkowita i istniejąca liczba vCPU wdrożona na wszystkich seriach maszyn wirtualnych nie powinna przekraczać całkowitego przydziału regionalnego procesorów wirtualnych vCPU zatwierdzonego dla subskrypcji. Jeśli jeden z tych przydziałów zostanie przekroczony, wdrożenie maszyny wirtualnej nie będzie dozwolone.
Można zażądać zwiększenia limitu przydziału procesorów wirtualnych vCPU dla serii maszyn wirtualnych z Azure Portal. Zwiększenie limitu przydziału serii maszyn wirtualnych powoduje automatyczne zwiększenie łącznej granicy procesorów wirtualnych vCPU w regionie o tej samej wartości. 

Dowiedz się więcej o przydziałach na [stronie limity przydziału vCPU maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) oraz na stronie [limitów subskrypcji i usług platformy Azure](https://aka.ms/quotalimits) . 

Teraz możesz zażądać wzrostu za pośrednictwem bloku **Pomoc i obsługa techniczna** lub bloku **użycie + przydział** w portalu. 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Żądanie zwiększania limitu przydziału vCPU na poziomie subskrypcji przy użyciu bloku **Pomoc i obsługa techniczna**

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej za pomocą bloku "pomoc i obsługa techniczna platformy Azure" dostępnego w Azure Portal. 

1. W https://portal.azure.com wybierz pozycję **Pomoc i obsługa techniczna**.

   ![Pomoc i obsługa techniczna](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Wybierz pozycję **Nowe żądanie obsługi**. 

     ![Nowe żądanie pomocy technicznej](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Z listy rozwijanej Typ problemu wybierz pozycję **usługi i limity subskrypcji (przydziały)** .

   ![Lista rozwijana typu problemu](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

   ![Wybieranie Aktualności subskrypcji](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Wybierz pozycję **COMPUTE-VM (rdzenie-procesorów wirtualnych vCPU) limit subskrypcji** na liście rozwijanej **Typ limitu przydziału** . 

   ![Wybierz typ limitu przydziału](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. W obszarze **Szczegóły problemu**podaj dodatkowe informacje, aby pomóc w przetwarzaniu żądania przez kliknięcie pozycji **Podaj szczegóły**.

   ![Podaj szczegóły](./media/resource-manager-core-quotas-request/provide-details.png)

7. W panelu **Szczegóły przydziału** wybierz pozycję model wdrażania i wybierz lokalizację.

   ![Szczegóły przydziału DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. Wybierz **rodziny SKU** , które wymagają zwiększenia. 

   ![Rodzina SKU](./media/resource-manager-core-quotas-request/sku-family.png)

9. Wprowadź nowe limity, które chcesz wykonać w ramach subskrypcji. Aby usunąć wiersz, usuń zaznaczenie jednostki SKU z listy rozwijanej rodzina SKU lub kliknij ikonę Odrzuć "x". Po wprowadzeniu żądanego przydziału dla każdej rodziny SKU kliknij pozycję **Zapisz i Kontynuuj** w panelu Szczegóły przydziału, aby kontynuować tworzenie żądania obsługi.

   ![Nowe limity](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Żądanie zwiększania limitu przydziału vCPU na poziomie subskrypcji przy użyciu bloku **użycia + limitu przydziału**

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej za pomocą bloku "użycie i limit przydziału" platformy Azure dostępnego w Azure Portal. 

1. W https://portal.azure.com wybierz pozycję **subskrypcje**.

   ![Subskrypcje](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

   ![Wybierz subskrypcję](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Wybierz **użycie i przydziały**

   ![Wybierz użycie i przydziały](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. W prawym górnym rogu wybierz pozycję **Zwiększ liczbę żądań**.

   ![Zwiększ liczbę żądań](./media/resource-manager-core-quotas-request/request-increase.png)

5. Wybierz pozycję **COMPUTE-VM (rdzenie-procesorów wirtualnych vCPU) limit subskrypcji rośnie** jako typ cudzysłowu. 

   ![Wypełnij formularz](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. W panelu **Szczegóły przydziału** wybierz pozycję model wdrażania i wybierz lokalizację.

   ![Blok problemu z limitem przydziału](./media/resource-manager-core-quotas-request/quota-details.png)

7. Wybierz **rodziny SKU** , które wymagają zwiększenia.

   ![Wybrana seria SKU](./media/resource-manager-core-quotas-request/sku-family.png)

8. Wprowadź nowe limity, które chcesz wykonać w ramach subskrypcji. Aby usunąć wiersz, usuń zaznaczenie jednostki SKU z listy rozwijanej rodzina SKU lub kliknij ikonę Odrzuć "x". Po wprowadzeniu żądanego przydziału dla każdej rodziny SKU kliknij pozycję **Zapisz i Kontynuuj** na stronie krok problemu, aby kontynuować tworzenie żądania obsługi.

   ![Żądanie nowego limitu przydziału jednostki SKU](./media/resource-manager-core-quotas-request/new-limits.png)
 
