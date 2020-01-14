---
title: Limit przydziału maszyn wirtualnych | Microsoft Docs
description: Zwiększenie limitów przydziału przez tworzenie żądań limitu przydziału
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9567df95e4e66d9c6b82b29cb9bba9adaf39349d
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897301"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Przydział miejsca na miejscu: Zwiększ limity dla całej serii maszyn wirtualnych

Maszyny wirtualne w miejscu zapewniają inny model użycia platformy Azure. Umożliwiają one założenie obniżenia kosztów w programie Exchange, co pozwala na usuwanie maszyn wirtualnych z opcją płatność zgodnie z rzeczywistym użyciem lub zastrzeżonymi wystąpieniami maszyn wirtualnych. Aby uzyskać więcej informacji na temat maszyn wirtualnych, zobacz maszyny wirtualne [platformy Azure dla zestawów skalowania maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

Azure Resource Manager obsługuje dwa typy przydziałów vCPU dla maszyn wirtualnych:
* *Maszyny wirtualne z opcją płatność zgodnie z rzeczywistym* użyciem i *zarezerwowane wystąpienia maszyn wirtualnych* podlegają *standardowym limitowi przydziału vCPU*.
* *Dodatkowe maszyny wirtualne* podlegają *limitowi przydziału vCPU*. 

W przypadku Menedżer zasobów typu *limitu przydziału usługi vCPU* wartości przydziały vCPU są wymuszane na wszystkich dostępnych seriach maszyn wirtualnych jako pojedyncze limity regionalne.

Za każdym razem, gdy wdrażasz nową maszynę wirtualną, całkowite nowe i istniejące użycie vCPU dla wszystkich wystąpień maszyn wirtualnych nie mogą przekraczać zatwierdzonego limitu przydziału miejsca vCPU. W przypadku przekroczenia limitu przydziału miejsca do miejsca wdrożenia maszyny wirtualnej nie są dozwolone. 

W tym artykule omówiono sposób żądania zwiększenia limitu przydziału vCPU w miejscu przy użyciu Azure Portal. 

Aby dowiedzieć się więcej na temat standardowych przydziałów usługi vCPU, zobacz limity [przydziału vCPU maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) i [usługi Azure Subscription and Service limit](https://aka.ms/quotalimits). 

Aby dowiedzieć się więcej o zwiększaniu limitu vCPU według regionu, zobacz [przydział standardowy: zwiększenie limitu regionalnego vCPU](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests).

## <a name="request-a-quota-limit-increase-from-the-help--support-pane"></a>Poproś o zwiększenie limitu przydziału w okienku "pomoc i obsługa techniczna" 

Aby zażądać zwiększenia limitu przydziału miejscowego dla wszystkich serii maszyn wirtualnych z okienka **Pomoc i obsługa techniczna** , wykonaj następujące czynności:

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

1. Wybierz kartę **szczegóły** , a następnie w obszarze **Szczegóły problemu**wybierz pozycję **Podaj szczegóły**, a następnie wprowadź dodatkowe informacje, aby ułatwić przetwarzanie żądania.

   ![Link "Podaj szczegóły"](./media/resource-manager-core-quotas-request/provide-details.png)
   
1. W okienku **szczegóły limitu przydziału** w prawym górnym rogu wykonaj następujące czynności:

   ![Okienko "Szczegóły przydziału"](./media/resource-manager-core-quotas-request/3-7.png)

   a. Z listy rozwijanej **model wdrażania** wybierz odpowiedni model.

   b. Z listy rozwijanej **lokalizacje** wybierz lokalizację. Dla wybranej lokalizacji, w obszarze **typy**, w polu **Wybierz typ wprowadź wartość** miejsce. 
   
   ![Karta Szczegóły "nowe żądanie obsługi"](./media/resource-manager-core-quotas-request/3-8.png)

    W obszarze **typy**można zażądać zarówno typów limitu przydziału standardowego, jak i dodatkowego z jednego przypadku pomocy technicznej w ramach obsługi wieloselekcji. 
    
    Aby uzyskać więcej informacji, zobacz [przydział standardowy: vCPU limit przydziału dla serii maszyn wirtualnych](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

   d. Wprowadź nowy limit przydziału dla tej subskrypcji. 
 
   ![Pole tekstowe "nowy limit vCPU"](./media/resource-manager-core-quotas-request/3-9.png)

1. Aby zażądać zwiększenia limitu przydziału dla więcej niż jednej lokalizacji, wybierz dodatkową lokalizację na liście rozwijanej, a następnie wybierz odpowiedni typ maszyny wirtualnej. Następnie można wprowadzić limit dotyczący dodatkowej lokalizacji.

   ![Dodatkowe lokalizacje w okienku "Szczegóły przydziału"](./media/resource-manager-core-quotas-request/3-10.png)

1. Wybierz pozycję **Zapisz i Kontynuuj** , aby kontynuować tworzenie żądania obsługi.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Poproś o zwiększenie limitu przydziału w okienku "subskrypcje"

Aby zażądać zwiększenia limitu przydziału miejsca dla wszystkich serii maszyn wirtualnych w okienku **subskrypcje** , wykonaj następujące czynności:

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

   ![Okienko "Szczegóły przydziału"](./media/resource-manager-core-quotas-request/3-2-6.png)
 
   a. Z listy rozwijanej **model wdrażania** wybierz odpowiedni model.

   b. Z listy rozwijanej **lokalizacje** wybierz lokalizację. 
   
   d. Dla wybranej lokalizacji, w obszarze **typy**, w polu **Wybierz typ wprowadź wartość** miejsce.

   ![Okienko "Szczegóły przydziału"](./media/resource-manager-core-quotas-request/3-2-7.png)

   Aby uzyskać więcej informacji, zobacz [przydział standardowy: vCPU limit przydziału dla serii maszyn wirtualnych](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

   d. Wprowadź nowy limit przydziału dla tej subskrypcji.

   ![Pole tekstowe "nowy limit vCPU"](./media/resource-manager-core-quotas-request/3-2-8.png)
 
1. Aby zażądać zwiększenia limitu przydziału dla więcej niż jednej lokalizacji, wybierz dodatkową lokalizację na liście rozwijanej, a następnie wybierz odpowiedni typ maszyny wirtualnej. Następnie można wprowadzić limit dotyczący dodatkowej lokalizacji.

   ![Dodatkowe lokalizacje w okienku "Szczegóły przydziału"](./media/resource-manager-core-quotas-request/3-2-9.png)
 
1. Wybierz pozycję **Zapisz i Kontynuuj** , aby kontynuować tworzenie żądania obsługi.


