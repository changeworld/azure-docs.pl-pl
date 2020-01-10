---
title: Poproś o zwiększenie limitów przydziału regionalnych vCPU platformy Azure | Microsoft Docs
description: Żądania wzrostu limitu przydziału regionalnego
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e262651a6e040c40dbe240ad3437eff1914aa3e5
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750851"
---
# <a name="standard-quota-increase-limits-by-region"></a>Przydział standardowy: Zwiększ limity według regionu 

Azure Resource Manager obsługuje dwa typy przydziałów vCPU dla maszyn wirtualnych:
* *Maszyny wirtualne z opcją płatność zgodnie z rzeczywistym* użyciem i *zarezerwowane wystąpienia maszyn wirtualnych* podlegają *standardowym limitowi przydziału vCPU*.
* *Dodatkowe maszyny wirtualne* podlegają *limitowi przydziału vCPU*. 

Standardowy przydział vCPU dla wystąpień z opcją płatność zgodnie z rzeczywistym użyciem i zarezerwowanych maszyn wirtualnych jest wymuszany w dwóch warstwach dla każdej subskrypcji w każdym regionie:
* Pierwsza warstwa to *całkowity limit procesorów wirtualnych vCPU regionalnych* (dla wszystkich serii maszyn wirtualnych).
* Druga warstwa to *Limit procesorów wirtualnych vCPU serii dla maszyn wirtualnych* (na przykład procesorów wirtualnych vCPU serii D).
 
Za każdym razem, gdy wdrażasz nową maszynę wirtualną, całkowite i istniejące użycie vCPU dla tej serii maszyn wirtualnych nie mogą przekroczyć zatwierdzonego limitu przydziału vCPU dla danej serii maszyn wirtualnych. Ponadto całkowita liczba nowych i istniejących procesorów wirtualnych vCPU, które są wdrożone na wszystkich seriach maszyn wirtualnych, nie powinna przekraczać całkowitego zatwierdzonego przydziału regionalnego vCPU dla subskrypcji. Jeśli jeden z tych limitów przydziału zostanie przekroczony, wdrożenie maszyny wirtualnej jest niedozwolone. 

Można zażądać zwiększenia limitu przydziału vCPU dla serii maszyn wirtualnych przy użyciu Azure Portal. Zwiększenie limitu przydziału serii maszyn wirtualnych powoduje automatyczne zwiększenie łącznej granicy vCPU w regionie o tej samej wartości.

Gdy tworzysz nową subskrypcję, domyślną łączną liczbą procesorów wirtualnych vCPU regionalnych może być nie równa całkowity limit przydziału vCPU dla wszystkich poszczególnych serii maszyn wirtualnych. Ta niezgodność może skutkować subskrypcją z wystarczającym limitem przydziału dla poszczególnych serii maszyn wirtualnych, które mają zostać wdrożone. Jednak może nie być wystarczający limit przydziału, aby pomieścić łączną procesorów wirtualnych vCPU regionalną dla wszystkich wdrożeń. W takim przypadku należy przesłać żądanie, aby jawnie zwiększyć limit łącznej liczby procesorów wirtualnych vCPU regionalnych. Łączny limit vCPU regionalnych nie może przekroczyć całkowitego zatwierdzonego przydziału dla całej serii maszyn wirtualnych dla regionu.

Aby dowiedzieć się więcej na temat standardowych przydziałów usługi vCPU, zobacz limity [przydziału vCPU maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) i [usługi Azure Subscription and Service limit](https://aka.ms/quotalimits).

Aby dowiedzieć się więcej o zwiększaniu limitów vCPU maszyn wirtualnych, zobacz limit [przydziału miejsca na miejscu: zwiększenie limitów dla całej serii maszyn wirtualnych](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

Można zażądać zwiększenia limitu przydziału vCPU standardowego według regionu na dwa sposoby, zgodnie z opisem w następnych sekcjach.

## <a name="request-a-quota-increase-by-region-from-the-help--support-pane"></a>Poproś o zwiększenie limitu przydziału w regionie w okienku "pomoc + obsługa techniczna"

Aby zażądać zwiększenia przydziału vCPU w regionie z okienka **Pomoc i obsługa techniczna** , wykonaj następujące czynności: 

1. W lewym okienku [Azure Portal](https://portal.azure.com)wybierz pozycję **Pomoc i obsługa techniczna**.

   ![Link "pomoc + obsługa techniczna"](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. W okienku **Pomoc i obsługa techniczna** wybierz pozycję **nowe żądanie obsługi**. 

    ![Nowe żądanie pomocy technicznej](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. Z listy rozwijanej **typ problemu** wybierz pozycję **usługi i limity subskrypcji (przydziały)** .

   ![Lista rozwijana "typ problemu"](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. Z listy rozwijanej **subskrypcja** wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Lista rozwijana "subskrypcja"](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. Z listy rozwijanej **typ przydziału** wybierz pozycję **inne żądania**.

   ![Lista rozwijana "typ przydziału"](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. W okienku **Szczegóły problemu** w polu **Opis** podaj następujące informacje dodatkowe: 

    a. W obszarze **model wdrażania**Określ **Menedżer zasobów**.  
    b. W **obszarze region**określ wymagany region (na przykład **Wschodnie stany USA 2**).  
    d. W polu **nowy limit**określ nowy limit vCPU dla regionu. Ta wartość nie powinna przekraczać sumy zatwierdzonych przydziałów dla poszczególnych serii SKU dla tej subskrypcji.

    ![Okienko "Szczegóły problemu"](./media/resource-manager-core-quotas-request/regional-details.png)

1. Wybierz pozycję **Zapisz i Kontynuuj** , aby kontynuować tworzenie żądania obsługi.

## <a name="request-a-quota-increase-by-region-from-the-subscriptions-pane"></a>Poproś o zwiększenie limitu przydziału w regionie z okienka "subskrypcje"

Aby zażądać zwiększenia przydziału vCPU według regionu z okienka **subskrypcje** , wykonaj następujące czynności: 

1. W lewym okienku [Azure Portal](https://portal.azure.com)wybierz pozycję **subskrypcje**.

   ![Link "subskrypcje"](./media/resource-manager-core-quotas-request/subscriptions.png)

1. Wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Okienko "subskrypcje"](./media/resource-manager-core-quotas-request/select-subscription.png)

1. W lewym okienku **nazwy subskrypcji\<** stronie wybierz pozycję **użycie + przydziały**.

   ![Link "użycie i przydziały"](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. W prawym górnym rogu wybierz pozycję **Zwiększ liczbę żądań**.

   ![Zwiększ liczbę żądań](./media/resource-manager-core-quotas-request/request-increase.png)

1. Z listy rozwijanej **typ przydziału** wybierz pozycję **inne żądania**.

   ![Lista rozwijana "typ przydziału"](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. W okienku **Szczegóły problemu** w polu **Opis** podaj następujące informacje dodatkowe: 

    a. W obszarze **model wdrażania**Określ **Menedżer zasobów**.  
    b. W **obszarze region**określ wymagany region (na przykład **Wschodnie stany USA 2**).  
    d. W polu **nowy limit**określ nowy limit vCPU dla regionu. Ta wartość nie powinna przekraczać sumy zatwierdzonych przydziałów dla poszczególnych serii SKU dla tej subskrypcji.

    ![Okienko "Szczegóły problemu"](./media/resource-manager-core-quotas-request/regional-details.png)

1. Wybierz pozycję **Zapisz i Kontynuuj** , aby kontynuować tworzenie żądania obsługi.

