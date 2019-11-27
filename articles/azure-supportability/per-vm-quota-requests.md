---
title: Żądania wzrostu przydziału vCPU na maszynie wirtualnej na platformie Azure | Microsoft Docs
description: żądania zwiększania limitu przydziału vCPU maszyny wirtualnej
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: ccd0c88c95ae9a752ef8ea2387bbde4f8559bc68
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74531678"
---
# <a name="standard-quota-per-vm-series-vcpu-limit-increase"></a>Przydział standardowy: vCPU limit limitu przydziału serii maszyny wirtualnej

Menedżer zasobów obsługuje dwa typy przydziałów vCPU dla maszyn wirtualnych. **Maszyny wirtualne z opcją płatność zgodnie z rzeczywistym użyciem i zarezerwowane wystąpienia maszyn wirtualnych** używają standardowego limitu przydziału. **Maszyny wirtualne o niskim priorytecie** używają przydziału o niskim priorytecie. Standardowy przydział vCPU dla wystąpień z opcją płatność zgodnie z rzeczywistym użyciem i zarezerwowane wystąpienia maszyn wirtualnych są wymuszane w dwóch warstwach dla każdej subskrypcji w każdym regionie

Pierwsza warstwa to **całkowity limit procesorów wirtualnych vCPU** (dla wszystkich serii maszyn wirtualnych), a druga warstwa to **Limit procesorów wirtualnych vCPU serii maszyny wirtualnej** (na przykład procesorów wirtualnych vCPU serii Dv3). Gdy zostanie wdrożona nowa maszyna wirtualna, suma nowych i istniejących procesorów wirtualnych vCPU użycie dla tej serii maszyn wirtualnych nie może przekraczać limitu przydziału vCPU zatwierdzonego dla danej serii maszyn wirtualnych. Dodatkowo całkowita i istniejąca liczba vCPU wdrożona na wszystkich seriach maszyn wirtualnych nie powinna przekraczać całkowitego przydziału regionalnego procesorów wirtualnych vCPU zatwierdzonego dla subskrypcji. Jeśli jeden z tych przydziałów zostanie przekroczony, wdrożenie maszyny wirtualnej nie będzie dozwolone.
Można zażądać zwiększenia limitu przydziału procesorów wirtualnych vCPU dla serii maszyn wirtualnych z Azure Portal. Zwiększenie limitu przydziału serii maszyn wirtualnych powoduje automatyczne zwiększenie łącznej granicy procesorów wirtualnych vCPU w regionie o tej samej wartości. 

Dowiedz się więcej o standardowych przydziałach vCPU na [stronie limit przydziału vCPU maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) oraz na [stronie limity subskrypcji i usług platformy Azure](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests). 

Dowiedz się więcej o tym, jak w [tym miejscu](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)zwiększyć limit przydziału dla ustawień regionalnych w warstwie Standardowa. 

Dowiedz się więcej na temat **wzrostu limitów vCPU maszyn wirtualnych o niskim priorytecie** [.](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)

Możesz zażądać zwiększenia **limitów przydziału standardowych vCPU na serię maszyn wirtualnych** za pośrednictwem bloku **Pomoc i obsługa techniczna** oraz bloku **użycie i przydział** w portalu.

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-the-help--support-blade"></a>Żądaj standardowego wzrostu przydziału vCPU na serię maszyn wirtualnych na poziomie subskrypcji przy użyciu bloku pomoc i obsługa techniczna

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej za pomocą bloku "pomoc i obsługa techniczna platformy Azure" dostępnego w Azure Portal. 

Możesz także zażądać przydziału dla wielu regionów za pomocą jednego przypadku pomocy technicznej. Aby uzyskać szczegółowe informacje, zobacz Krok 11 poniżej.

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

7. W panelu **Szczegóły przydziału** wybierz pozycję **model wdrażania** i wybierz **lokalizację.**

   ![Szczegóły przydziału DM](./media/resource-manager-core-quotas-request/1-7.png)

8. Dla wybranej lokalizacji wybierz wartość **typu** **"Standardowa"** . Można zażądać zarówno typów limitu przydziału standardowego, jak i o niskim priorytecie z jednego przypadku pomocy technicznej w ramach obsługi wieloselekcji w polu **Typ** . Dowiedz się więcej o **zwiększaniu limitów przydziału o niskim priorytecie** na **stronie > <** .

   ![Rodzina SKU](./media/resource-manager-core-quotas-request/1-8.png)

9. Wybierz **rodziny SKU** , które wymagają wzrostu

   ![Rodzina SKU](./media/resource-manager-core-quotas-request/1-9.png)

10. Wprowadź nowe limity, które chcesz wykonać w ramach subskrypcji. Aby usunąć wiersz, usuń zaznaczenie jednostki SKU z listy rozwijanej rodzina SKU lub kliknij ikonę Odrzuć "x". 

   ![Nowe limity](./media/resource-manager-core-quotas-request/1-10.png)

11. Aby zażądać przydziału dla więcej niż jednej lokalizacji, możesz sprawdzić inną **lokalizację** z listy rozwijanej i wybrać odpowiedni typ maszyny wirtualnej. Ten krok umożliwia wstępne załadowanie rodzin jednostek SKU wybranych dla wcześniejszej **lokalizacji** do nowej lokalizacji. możesz po prostu wprowadzić nowe limity, które chcesz.

   ![Wiele lokalizacji](./media/resource-manager-core-quotas-request/1-11.png)
   
12. Po wprowadzeniu żądanego przydziału dla każdej rodziny SKU kliknij pozycję **Zapisz i Kontynuuj** w panelu Szczegóły przydziału, aby kontynuować tworzenie żądania obsługi.

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-usages--quota-blade"></a>Żądaj standardowego wzrostu przydziału vCPU na serię maszyn wirtualnych na poziomie subskrypcji przy użyciu bloku użycia i limitu przydziału

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej za pomocą bloku "użycie i limit przydziału" platformy Azure dostępnego w Azure Portal.

Możesz także **zażądać przydziału dla wielu regionów** za pomocą jednego przypadku pomocy technicznej. Aby uzyskać szczegółowe informacje, zobacz krok 10 poniżej.

1. W obszarze https://portal.azure.com wybierz pozycję **subskrypcje**.

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

   ![Blok problemu z limitem przydziału](./media/resource-manager-core-quotas-request/1-1-6.png)

7. Dla wybranej lokalizacji wybierz wartość **typu** **"Standardowa"** . Można zażądać zarówno typów limitu przydziału standardowego, jak i o niskim priorytecie z jednego przypadku pomocy technicznej w ramach obsługi wieloselekcji w polu **Typ** . Dowiedz się więcej o **zwiększaniu limitów procesorów wirtualnych vCPU o niskim priorytecie** na tej [stronie](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

   ![Wybrana seria SKU](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   
8. Wybierz **rodziny SKU** , które wymagają wzrostu

   ![Wybrana seria SKU](./media/resource-manager-core-quotas-request/1-1-8.png)

9. Wprowadź nowe limity, które chcesz wykonać w ramach subskrypcji. Aby usunąć wiersz, usuń zaznaczenie jednostki SKU z listy rozwijanej rodzina SKU lub kliknij ikonę Odrzuć "x". 

   ![Żądanie nowego limitu przydziału jednostki SKU](./media/resource-manager-core-quotas-request/1-1-9.png)
   

10. Aby zażądać przydziału dla więcej niż jednej lokalizacji, możesz sprawdzić inną **lokalizację** z listy rozwijanej i wybrać odpowiedni typ maszyny wirtualnej. Ten krok umożliwia wstępne załadowanie rodzin jednostek SKU wybranych dla wcześniejszej **lokalizacji** do nowej lokalizacji. możesz po prostu wprowadzić nowe limity, które chcesz.
   
    ![Żądanie nowego limitu przydziału jednostki SKU](./media/resource-manager-core-quotas-request/1-1-10.png)
 
