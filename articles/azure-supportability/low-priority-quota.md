---
title: Przydział o niskim priorytecie | Microsoft Docs
description: Żądania limitu przydziału o niskim priorytecie
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 49ac478330cf73dff050a3edcc15933692fa6448
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535173"
---
# <a name="low-priority-quota-limit-increase-for-all-vm-series"></a>Przydział o niskim priorytecie: zwiększenie limitu dla całej serii maszyn wirtualnych

Maszyny wirtualne o niskim priorytecie zapewniają inny model użycia platformy Azure, a także obniżają koszty związane z tym, że platforma Azure usunie maszynę wirtualną w miarę potrzeby dla wdrożeń z opcją płatność zgodnie z rzeczywistym użyciem lub zarezerwowanych wystąpień maszyn wirtualnych. Więcej informacji na temat maszyn wirtualnych o niskim priorytecie można znaleźć [tutaj](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority).

Menedżer zasobów obsługuje dwa typy przydziałów vCPU dla maszyn wirtualnych. **Maszyny wirtualne z opcją płatność zgodnie z rzeczywistym użyciem i zarezerwowane wystąpienia maszyn wirtualnych** używają standardowego limitu przydziału. **Maszyny wirtualne o niskim priorytecie** używają przydziału o niskim priorytecie. 

W przypadku typu **przydziału o niskim priorytecie** Menedżer zasobów przydziały vCPU są wymuszane na wszystkich dostępnych seriach maszyn wirtualnych jako pojedyncze limity regionalne.

W przypadku każdej maszyny wirtualnej o niskim priorytecie należy wdrożyć sumę nowych i istniejących procesorów wirtualnych vCPU użycie dla wszystkich wystąpień maszyn wirtualnych o niskim priorytecie, które nie mogą przekroczyć zatwierdzonego limitu przydziału vCPU o niskim priorytecie. W przypadku przekroczenia limitu przydziału o niskim priorytecie wdrożenie maszyny wirtualnej o niskim priorytecie nie będzie dozwolone. Można zażądać zwiększenia limitu przydziału procesorów wirtualnych vCPU o niskim priorytecie z Azure Portal. 

Dowiedz się więcej o standardowych przydziałach vCPU na stronie limit przydziału vCPU maszyny wirtualnej oraz na stronie limity subskrypcji i usług platformy Azure. Dowiedz się więcej na temat zwiększania regionalnego limitu przydziału vCPU na tej [stronie](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests).

Teraz można zażądać zwiększenia **limitów przydziału o niskim priorytecie dla wszystkich serii maszyn wirtualnych** za pośrednictwem bloku **Pomoc i obsługa techniczna** oraz bloku **użycie i przydział** w portalu.

## <a name="request-low-priority-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>Poproś o zwiększenie limitu przydziału niskiego priorytetu dla wszystkich serii maszyn wirtualnych na subskrypcję przy użyciu bloku pomoc i obsługa techniczna

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej za pomocą bloku "pomoc i obsługa techniczna platformy Azure" dostępnego w Azure Portal.

Możesz także **zażądać przydziału dla wielu regionów** za pomocą jednego przypadku pomocy technicznej. Aby uzyskać szczegółowe informacje, zobacz krok 10 poniżej. 


1. W obszarze https://portal.azure.comwybierz pozycję **Pomoc i obsługa techniczna**.

   ![Pomoc i obsługa techniczna](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Wybierz pozycję **Nowe żądanie obsługi**. 

     ![Nowe żądanie pomocy technicznej](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Z listy rozwijanej Typ problemu wybierz pozycję **usługi i limity subskrypcji (przydziały)** .

   ![Lista rozwijana typ problemu](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

   ![Wybieranie Aktualności subskrypcji](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Wybierz pozycję **COMPUTE-VM (rdzenie-procesorów wirtualnych vCPU) limit subskrypcji** na liście rozwijanej **Typ limitu przydziału** . 

   ![Wybierz typ limitu przydziału](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. W obszarze **Szczegóły problemu**podaj dodatkowe informacje, aby pomóc w przetwarzaniu żądania przez kliknięcie pozycji **Podaj szczegóły**.

   ![Podaj szczegóły](./media/resource-manager-core-quotas-request/provide-details.png)
   
7.  W panelu **Szczegóły przydziału*** wybierz pozycję **model wdrażania** i wybierz **lokalizację**.

![Podaj szczegóły](./media/resource-manager-core-quotas-request/3-7.png)

8. Dla wybranej lokalizacji wybierz wartość **typu** **"niski priorytet"** . Można zażądać zarówno typów limitu przydziału standardowego, jak i o niskim priorytecie z jednego przypadku pomocy technicznej w ramach obsługi wieloselekcji w polu **Typ** . Dowiedz się więcej o **zwiększaniu standardowego przydziału na serię maszyn wirtualnych** na tej [stronie](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

![Podaj szczegóły](./media/resource-manager-core-quotas-request/3-8.png)

9.  Wprowadź nowy limit, który chcesz umieścić w subskrypcji. 
 
 ![Podaj szczegóły](./media/resource-manager-core-quotas-request/3-9.png)

10. Aby zażądać przydziału dla więcej niż jednej lokalizacji, możesz sprawdzić inną lokalizację z listy rozwijanej i wybrać odpowiedni typ maszyny wirtualnej. Następnie możesz wprowadzić nowe limity, które chcesz.

![Podaj szczegóły](./media/resource-manager-core-quotas-request/3-10.png)

11. Po wprowadzeniu żądanego limitu przydziału kliknij przycisk **Zapisz i Kontynuuj** w panelu Szczegóły przydziału, aby kontynuować tworzenie żądania obsługi.

## <a name="request-low-priority-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>Poproś o zwiększenie limitu przydziału niskiego priorytetu dla wszystkich serii maszyn wirtualnych na subskrypcję przy użyciu bloku użycia i limitu przydziału

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej za pomocą bloku "użycie i limit przydziału" platformy Azure dostępnego w Azure Portal.

Możesz także **zażądać przydziału dla wielu regionów** za pomocą jednego przypadku pomocy technicznej. Aby uzyskać szczegółowe informacje, zobacz krok 9 poniżej. 

1.  W obszarze https://portal.azure.comwybierz pozycję **subskrypcje**.

 ![Subskrypcje](./media/resource-manager-core-quotas-request/subscriptions.png)

2.  Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

 ![Wybieranie subskrypcji](./media/resource-manager-core-quotas-request/select-subscription.png)

3.  Wybierz pozycję **użycie i przydziały**.

 ![Wybierz użycie i przydziały](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4.  W prawym górnym rogu wybierz pozycję **Zwiększ liczbę żądań.**

   ![Zwiększ liczbę żądań](./media/resource-manager-core-quotas-request/request-increase.png)

5.  Wybierz pozycję **COMPUTE-VM (rdzenie-procesorów wirtualnych vCPU) limit subskrypcji rośnie** jako typ cudzysłowu.

  ![Wypełnij formularz](./media/resource-manager-core-quotas-request/select-quota-type.png)

6.  W panelu **Szczegóły przydziału** wybierz pozycję model wdrażania i wybierz lokalizację.

  ![Wypełnij formularz](./media/resource-manager-core-quotas-request/3-2-6.png)
 
7.  Dla wybranej lokalizacji wybierz wartość **typu** **"niski priorytet".** Można zażądać zarówno typów limitu przydziału standardowego, jak i o niskim priorytecie z jednego przypadku pomocy technicznej w ramach obsługi wieloselekcji w polu **Typ** . Dowiedz się więcej o **zwiększaniu standardowego przydziału na serię maszyn wirtualnych** na tej [stronie](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

  ![Wypełnij formularz](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  Wprowadź nowy limit, który chcesz umieścić w subskrypcji.

  ![Wypełnij formularz](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  Aby zażądać przydziału dla więcej niż jednej lokalizacji, możesz sprawdzić inną **lokalizację** z listy rozwijanej i wybrać odpowiedni typ maszyny wirtualnej. Następnie możesz wprowadzić nowe limity, które chcesz.

  ![Wypełnij formularz](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. Po wprowadzeniu żądanego limitu przydziału kliknij przycisk **Zapisz i Kontynuuj** w panelu Szczegóły przydziału, aby kontynuować tworzenie żądania obsługi.

