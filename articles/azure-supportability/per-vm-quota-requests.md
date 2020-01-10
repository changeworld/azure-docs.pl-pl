---
title: Poproś o zwiększenie limitów przydziału vCPU na serię maszyn wirtualnych platformy Azure | Microsoft Docs
description: W tym artykule omówiono sposób zwiększania limitu przydziału na maszynę wirtualną vCPU.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: fc59ffda245834c716067bc63e6c3745fa5d23b3
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750197"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Przydział standardowy: Zwiększ limity według serii maszyn wirtualnych

Azure Resource Manager obsługuje dwa typy przydziałów vCPU dla maszyn wirtualnych:
* *Maszyny wirtualne z opcją płatność zgodnie z rzeczywistym* użyciem i *zarezerwowane wystąpienia maszyn wirtualnych* podlegają *standardowym limitowi przydziału vCPU*.
* *Dodatkowe maszyny wirtualne* podlegają *limitowi przydziału vCPU*. 

Standardowy przydział vCPU dla wystąpień z opcją płatność zgodnie z rzeczywistym użyciem i zarezerwowanych maszyn wirtualnych jest wymuszany w dwóch warstwach dla każdej subskrypcji w każdym regionie:
* Pierwsza warstwa to *całkowity limit procesorów wirtualnych vCPU regionalnych* (dla wszystkich serii maszyn wirtualnych).
* Druga warstwa to *Limit procesorów wirtualnych vCPU serii dla maszyn wirtualnych* (na przykład Dv3 serii procesorów wirtualnych vCPU). 

Za każdym razem, gdy wdrażasz nową maszynę wirtualną, całkowite i istniejące użycie vCPU dla tej serii maszyn wirtualnych nie mogą przekroczyć zatwierdzonego limitu przydziału vCPU dla danej serii maszyn wirtualnych. Ponadto całkowita liczba nowych i istniejących procesorów wirtualnych vCPU, które są wdrożone na wszystkich seriach maszyn wirtualnych, nie powinna przekraczać całkowitego zatwierdzonego przydziału regionalnego vCPU dla subskrypcji. Jeśli jeden z tych limitów przydziału zostanie przekroczony, wdrożenie maszyny wirtualnej jest niedozwolone.

Można zażądać zwiększenia limitu przydziału vCPU dla serii maszyn wirtualnych przy użyciu Azure Portal. Zwiększenie limitu przydziału serii maszyn wirtualnych powoduje automatyczne zwiększenie łącznej granicy vCPU w regionie o tej samej wartości. 

Aby dowiedzieć się więcej na temat standardowych przydziałów usługi vCPU, zobacz limity [przydziału vCPU maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) i [usługi Azure Subscription and Service limit](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests). 

Aby dowiedzieć się więcej na temat zwiększania limitu przydziału vCPU według regionu dla standardowego limitu, zobacz [przydział standardowy: Zwiększ limity według regionów](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests). 

Aby dowiedzieć się więcej o zwiększaniu limitów vCPU maszyn wirtualnych, zobacz limit [przydziału miejsca na miejscu: zwiększenie limitów dla całej serii maszyn wirtualnych](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

Możesz poprosić o zwiększenie limitów przydziału standardowych vCPU na serię maszyn wirtualnych na jeden z dwóch sposobów, zgodnie z opisem w następnych sekcjach.

## <a name="request-a-standard-quota-increase-from-the-help--support-pane"></a>Poproś o zwiększenie limitu przydziału standardowego z okienka "pomoc i obsługa techniczna"

Aby zażądać standardowego wzrostu przydziału vCPU na serię maszyn wirtualnych z okienka **Pomoc i obsługa techniczna** , wykonaj następujące czynności: 

> [!NOTE]
> Możesz również zażądać zwiększenia limitu przydziału dla wielu regionów za pomocą jednego przypadku pomocy technicznej. Aby uzyskać szczegółowe informacje, zobacz krok 8.

1. W lewym okienku [Azure Portal](https://portal.azure.com)wybierz pozycję **Pomoc i obsługa techniczna**.

   ![Link "pomoc + obsługa techniczna"](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. W okienku **Pomoc i obsługa techniczna** wybierz pozycję **nowe żądanie obsługi**. 

    ![Nowe żądanie pomocy technicznej](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. Z listy rozwijanej **typ problemu** wybierz pozycję **usługi i limity subskrypcji (przydziały)** .

   ![Lista rozwijana "typ problemu"](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. Z listy rozwijanej **subskrypcja** wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Lista rozwijana "subskrypcja"](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. Z listy rozwijanej **typ przydziału** wybierz pozycję **COMPUTE-VM (rdzenie-procesorów wirtualnych vCPU)** . 

   ![Lista rozwijana "typ przydziału"](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Na karcie **szczegóły** w obszarze **Szczegóły problemu**wybierz pozycję **Podaj szczegóły**, a następnie wprowadź dodatkowe informacje, aby pomóc w przetwarzaniu żądania.

   ![Link "Podaj szczegóły"](./media/resource-manager-core-quotas-request/provide-details.png)

1. W okienku **szczegóły limitu przydziału** w prawym górnym rogu wykonaj następujące czynności:

   ![Okienko "Szczegóły przydziału"](./media/resource-manager-core-quotas-request/1-7.png)

   a. Z listy rozwijanej **model wdrażania** wybierz odpowiedni model.

   b. Z listy rozwijanej **lokalizacje** wybierz lokalizację. Dla wybranej lokalizacji, w obszarze **typy**, w polu **Wybierz typ** wpisz **Standard**.

   ![Okienko "Szczegóły przydziału" — typy przydziałów](./media/resource-manager-core-quotas-request/1-8.png)

   W obszarze **typy**można zażądać zarówno typów limitu przydziału standardowego, jak i dodatkowego z jednego przypadku pomocy technicznej w ramach obsługi wieloselekcji.
   
   Aby uzyskać więcej informacji na temat zwiększania limitów przydziału punktów, zobacz [maszyny wirtualne Azure spot dla zestawów skalowania maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

   d. Poniżej listy rozwijanej **standardowa** wybierz serię SKU, której przydziały chcesz zwiększyć.

   ![Okienko "Szczegóły przydziału" — Seria SKU](./media/resource-manager-core-quotas-request/1-9.png)

   d. Wprowadź nowe limity przydziałów dla tej subskrypcji. Aby usunąć jednostkę SKU z listy, wyczyść pole wyboru obok jednostki SKU lub wybierz ikonę **usuwania** (X). 

   ![Pole tekstowe "nowy limit vCPU"](./media/resource-manager-core-quotas-request/1-10.png)

1. Aby zażądać zwiększenia limitu przydziału dla więcej niż jednej lokalizacji, wybierz dodatkową lokalizację na liście rozwijanej, a następnie wybierz odpowiedni typ maszyny wirtualnej. Następnie można wprowadzić limit dotyczący dodatkowej lokalizacji.

   ![Dodatkowe lokalizacje w okienku "Szczegóły przydziału"](./media/resource-manager-core-quotas-request/1-11.png)
   
1. Wybierz pozycję **Zapisz i Kontynuuj** , aby kontynuować tworzenie żądania obsługi.

## <a name="request-a-standard-quota-increase-from-the-subscriptions-pane"></a>Poproś o zwiększenie limitu przydziału standardowego z okienka "subskrypcje"

Aby zażądać standardowego wzrostu przydziału vCPU na serię maszyn wirtualnych z okienka **subskrypcje** , wykonaj następujące czynności:

> [!NOTE]
> Możesz również zażądać zwiększenia limitu przydziału dla wielu regionów za pomocą jednego przypadku pomocy technicznej. Aby uzyskać szczegółowe informacje, zobacz krok 7.

1. W lewym okienku [Azure Portal](https://portal.azure.com)wybierz pozycję **subskrypcje**.

   ![Link "subskrypcje"](./media/resource-manager-core-quotas-request/subscriptions.png)

1. Wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Okienko "subskrypcje"](./media/resource-manager-core-quotas-request/select-subscription.png)

1. W lewym okienku **nazwy subskrypcji\<** stronie wybierz pozycję **użycie + przydziały**.

   ![Link "użycie i przydziały"](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. W prawym górnym rogu wybierz pozycję **Zwiększ liczbę żądań**.

   ![Zwiększ liczbę żądań](./media/resource-manager-core-quotas-request/request-increase.png)

1. Z listy rozwijanej **typ przydziału** wybierz pozycję **COMPUTE-VM (rdzenie-procesorów wirtualnych vCPU)** .

   ![Lista rozwijana "typ przydziału"](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
1. W okienku **szczegóły limitu przydziału** w prawym górnym rogu wykonaj następujące czynności:

   ![Okienko "Szczegóły przydziału"](./media/resource-manager-core-quotas-request/1-1-6.png)

   a. Z listy rozwijanej **model wdrażania** wybierz odpowiedni model.

   b. Z listy rozwijanej **lokalizacje** wybierz lokalizację. 
   
   d. Dla wybranej lokalizacji w obszarze **typy**wybierz pozycję **Wybierz typ**, a następnie zaznacz pole wyboru **Standardowy** .

   ![Pole wyboru "standardowe"](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   W obszarze **typy**można zażądać zarówno typów limitu przydziału standardowego, jak i o niskim priorytecie z jednego przypadku pomocy technicznej w ramach obsługi wieloselekcji.
   
   Aby uzyskać więcej informacji na temat zwiększania limitów przydziału punktów, zobacz [maszyny wirtualne Azure spot dla zestawów skalowania maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

   d. Poniżej listy rozwijanej **standardowa** wybierz serię SKU, której przydziały chcesz zwiększyć.

   ![Okienko "Szczegóły przydziału" — Seria SKU](./media/resource-manager-core-quotas-request/1-1-8.png)

   e. Wprowadź nowe limity przydziałów dla tej subskrypcji. Aby usunąć jednostkę SKU z listy, wyczyść pole wyboru obok jednostki SKU lub wybierz pozycję **Usuń** (X). 

   ![Pole tekstowe "nowy limit vCPU"](./media/resource-manager-core-quotas-request/1-1-9.png)
   
1. Aby zażądać zwiększenia limitu przydziału dla więcej niż jednej lokalizacji, wybierz dodatkową lokalizację na liście rozwijanej, a następnie wybierz odpowiedni typ maszyny wirtualnej. 

   Ten krok umożliwia wstępne załadowanie serii SKU wybranej dla wcześniejszych lokalizacji. Wprowadź limity przydziałów, które mają być stosowane do dodatkowej serii.
   
   ![Dodatkowe lokalizacje w okienku "Szczegóły przydziału"](./media/resource-manager-core-quotas-request/1-1-10.png)
 
1. Wybierz pozycję **Zapisz i Kontynuuj** , aby kontynuować tworzenie żądania obsługi.
