---
title: Przydział na miejscu | Microsoft Docs
description: Żądania limitu przydziału miejsca
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 09c9d7940314b691e6351353e6a0076510fdcb13
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850571"
---
# <a name="spot-quota-limit-increase-for-all-vm-series"></a>Przydział punktowy: zwiększenie limitu dla całej serii maszyn wirtualnych

Maszyny wirtualne maszyn wirtualnych zapewniają inny model użycia platformy Azure, ale obniżają koszty, dzięki czemu platforma Azure usunie maszynę wirtualną w razie potrzeby w przypadku wdrożeń z opcją płatność zgodnie z rzeczywistym użyciem lub zastrzeżonych wystąpień maszyn wirtualnych. Przeczytaj więcej na [temat maszyn wirtualnych na miejscu.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)

Menedżer zasobów obsługuje dwa typy przydziałów vCPU dla maszyn wirtualnych. **Maszyny wirtualne z opcją płatność zgodnie z rzeczywistym użyciem i zarezerwowane wystąpienia maszyn wirtualnych** używają standardowego limitu przydziału. **Miejsca na maszynach wirtualnych** używają limitu przydziału miejsca. 

W przypadku typu **przydziału dodatkowego** Menedżer zasobów przydziały vCPU są wymuszane dla wszystkich dostępnych serii maszyn wirtualnych jako pojedyncze limity regionalne.

Gdy zostanie wdrożona nowa maszyna wirtualna na miejscu, suma nowych i istniejących procesorów wirtualnych vCPU użycie dla wszystkich wystąpień maszyn wirtualnych nie może przekraczać zatwierdzonego limitu przydziału miejsca vCPU. W przypadku przekroczenia limitu przydziału miejsca do wdrożenia maszyny wirtualnej nie będą dozwolone. Możesz poprosić o zwiększenie limitu przydziału procesorów wirtualnych vCPU dodatkowych z Azure Portal. 

Dowiedz się więcej o standardowych przydziałach vCPU na stronie limit przydziału vCPU maszyny wirtualnej oraz na stronie limity subskrypcji i usług platformy Azure. Dowiedz się więcej na temat zwiększania regionalnego limitu przydziału vCPU na tej [stronie](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests).

Teraz można zażądać zwiększenia **limitów przydziału miejsca dla wszystkich serii maszyn wirtualnych** za pośrednictwem bloku **Pomoc i obsługa techniczna** oraz bloku **użycie i przydział** w portalu.

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>Poproś o zwiększenie limitu przydziału miejsca dla wszystkich serii maszyn wirtualnych na subskrypcję przy użyciu bloku pomoc i obsługa techniczna

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej za pomocą bloku "pomoc i obsługa techniczna platformy Azure" dostępnego w Azure Portal.

Możesz także **zażądać przydziału dla wielu regionów** za pomocą jednego przypadku pomocy technicznej. Aby uzyskać szczegółowe informacje, zobacz krok 10 poniżej. 


1. W obszarze https://portal.azure.com wybierz pozycję **Pomoc i obsługa techniczna**.

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

8. Dla wybranej lokalizacji wybierz opcję wartość **typu** jako **"spot"** . Możesz zażądać zarówno typów limitu przydziału standardowego, jak i punktowego z pojedynczego przypadku pomocy technicznej za pomocą obsługi wieloselekcji w polu **Typ** . Dowiedz się więcej o **zwiększaniu standardowego przydziału na serię maszyn wirtualnych** na tej [stronie](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

![Podaj szczegóły](./media/resource-manager-core-quotas-request/3-8.png)

9.  Wprowadź nowy limit, który chcesz umieścić w subskrypcji. 
 
 ![Podaj szczegóły](./media/resource-manager-core-quotas-request/3-9.png)

10. Aby zażądać przydziału dla więcej niż jednej lokalizacji, możesz sprawdzić inną lokalizację z listy rozwijanej i wybrać odpowiedni typ maszyny wirtualnej. Następnie możesz wprowadzić nowe limity, które chcesz.

![Podaj szczegóły](./media/resource-manager-core-quotas-request/3-10.png)

11. Po wprowadzeniu żądanego limitu przydziału kliknij przycisk **Zapisz i Kontynuuj** w panelu Szczegóły przydziału, aby kontynuować tworzenie żądania obsługi.

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>Poproś o zwiększenie limitu przydziału miejsca na miejscu dla wszystkich serii maszyn wirtualnych na subskrypcję przy użyciu bloku użycia i limitu przydziału

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej za pomocą bloku "użycie i limit przydziału" platformy Azure dostępnego w Azure Portal.

Możesz także **zażądać przydziału dla wielu regionów** za pomocą jednego przypadku pomocy technicznej. Aby uzyskać szczegółowe informacje, zobacz krok 9 poniżej. 

1.  W obszarze https://portal.azure.com wybierz pozycję **subskrypcje**.

 ![Subskrypcje](./media/resource-manager-core-quotas-request/subscriptions.png)

2.  Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

 ![Wybierz subskrypcję](./media/resource-manager-core-quotas-request/select-subscription.png)

3.  Wybierz pozycję **użycie i przydziały**.

 ![Wybierz użycie i przydziały](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4.  W prawym górnym rogu wybierz pozycję **Zwiększ liczbę żądań.**

   ![Zwiększ liczbę żądań](./media/resource-manager-core-quotas-request/request-increase.png)

5.  Wybierz pozycję **COMPUTE-VM (rdzenie-procesorów wirtualnych vCPU) limit subskrypcji rośnie** jako typ cudzysłowu.

  ![Wypełnij formularz](./media/resource-manager-core-quotas-request/select-quota-type.png)

6.  W panelu **Szczegóły przydziału** wybierz pozycję model wdrażania i wybierz lokalizację.

  ![Wypełnij formularz](./media/resource-manager-core-quotas-request/3-2-6.png)
 
7.  Dla wybranej lokalizacji wybierz opcję wartość **typu** jako **"spot".** Możesz zażądać zarówno typów limitu przydziału standardowego, jak i punktowego z pojedynczego przypadku pomocy technicznej za pomocą obsługi wieloselekcji w polu **Typ** . Dowiedz się więcej o **zwiększaniu standardowego przydziału na serię maszyn wirtualnych** na tej [stronie](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

  ![Wypełnij formularz](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  Wprowadź nowy limit, który chcesz umieścić w subskrypcji.

  ![Wypełnij formularz](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  Aby zażądać przydziału dla więcej niż jednej lokalizacji, możesz sprawdzić inną **lokalizację** z listy rozwijanej i wybrać odpowiedni typ maszyny wirtualnej. Następnie możesz wprowadzić nowe limity, które chcesz.

  ![Wypełnij formularz](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. Po wprowadzeniu żądanego limitu przydziału kliknij przycisk **Zapisz i Kontynuuj** w panelu Szczegóły przydziału, aby kontynuować tworzenie żądania obsługi.


