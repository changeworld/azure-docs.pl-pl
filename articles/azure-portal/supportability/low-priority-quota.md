---
title: Przydział maszyny wirtualnej spot — Azure
description: Zwiększ limity przydziałów dla maszyn wirtualnych na miejscu, które zapewniają model użycia platformy Azure, który pozwala zakładać niższe koszty w zamian za umożliwienie platformy Azure usuwania maszyn wirtualnych w razie potrzeby.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 3e6dddfc458f390f5648e23a8a2af8d714de4bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842800"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Przydział punktowy: Zwiększ limity dla wszystkich serii maszyn wirtualnych

Maszyny wirtualne spot (VMs) zapewniają inny model użycia platformy Azure. Pozwalają one zakładać niższe koszty w zamian za umożliwienie platformy Azure usuwania maszyn wirtualnych zgodnie z potrzebami dla pay-as-you-go lub zastrzeżonych wdrożeń wystąpienia maszyny wirtualnej. Aby uzyskać więcej informacji na temat maszyn wirtualnych punktowych, zobacz [maszyny wirtualne spot platformy Azure dla zestawów skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/use-spot.md).

Usługa Azure Resource Manager obsługuje dwa typy przydziałów wirtualnych dla maszyn wirtualnych:

* *Maszyny wirtualne* zgodnie z rzeczywistym użyciem i *zarezerwowane wystąpienia maszyn wirtualnych* podlegają *standardowemu przydziałowi vCPU*.
* *Maszyny wirtualne punktowe* podlegają *inkcjom owym przydziałowi vCPU*.

W przypadku typu przydziału wirtualnego procesora punktowego przydziały vCPU Menedżera zasobów są wymuszane we wszystkich dostępnych seriach maszyn wirtualnych jako pojedynczy limit regionalny.

Za każdym razem, gdy wdrażasz nową maszynę wirtualną punktową, całkowite nowe i istniejące użycie procesora wirtualnego dla wszystkich wystąpień maszyn wirtualnych punktowych nie może przekraczać zatwierdzonego limitu przydziału punktu wirtualnegopuksnego. Jeśli przydział punktowy zostanie przekroczony, wdrożenie maszyny wirtualnej na miejscu jest niedozwolone.

W tym artykule omówiono sposób żądania zwiększenia limitu przydziału wirtualnego procesora punktowego przy użyciu witryny Azure portal.

Aby dowiedzieć się więcej o standardowych przydziałach procesora wirtualnego, zobacz [Przydziały vCPU maszyn wirtualnych oraz limity](../../virtual-machines/windows/quotas.md) [subskrypcji i usług platformy Azure, przydziały i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Aby dowiedzieć się więcej o zwiększaniu limitu vCPU według regionu, zobacz [Przydział standardowy: Zwiększanie limitów według regionu](regional-quota-requests.md).

## <a name="request-a-quota-limit-increase-from-help--support"></a>Żądanie zwiększenia limitu przydziału z pomocy + pomocy technicznej

Aby zażądać zwiększenia limitu przydziału punktowego dla wszystkich serii maszyn wirtualnych przy użyciu **pomocy + pomocy technicznej:**

> [!NOTE]
> Można również zażądać zwiększenia limitu przydziału dla wielu regionów za pośrednictwem jednego przypadku pomocy technicznej. Szczegółowe informacje można znaleźć w kroku 8.

1. Z menu [portalu platformy Azure](https://portal.azure.com) wybierz pozycję Pomoc + obsługa **.**

   ![Łącze Pomoc + pomoc](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. W **pomocy + pomocy ,** wybierz nowe żądanie pomocy **.**

    ![Tworzenie nowego żądania pomocy technicznej](./media/resource-manager-core-quotas-request/new-support-request.png)

1. W obszarze **Typ problemu**wybierz pozycję **Limity usługi i subskrypcji (przydziały).**

   ![Wybieranie typu problemu](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. W przypadku **opcji Subskrypcja**wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Wybieranie subskrypcji dla zwiększonego przydziału](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. W przypadku **typu przydziału**wybierz limit **subskrypcji Compute-VM (cores-vCPU).**

   ![Wybieranie typu przydziału](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Wybierz **dalej: Rozwiązania,** aby otworzyć **SZCZEGÓŁY PROBLEMU**. Wybierz **pozycję Podaj szczegóły,** aby wprowadzić dodatkowe informacje.

   ![Link "Podaj szczegóły"](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. W **szczegółach przydziału**wykonaj następujące czynności:

   1. W przypadku **modelu wdrażania**wybierz odpowiedni model, a w przypadku **lokalizacji**wybierz lokalizację.

      ![Podaj dodatkowe szczegóły przydziału](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Dla wybranej lokalizacji w obszarze **Typy**w **obszarze Wybierz typ**wybierz pozycję **Punkt**.

      ![Wybierz typ spotu](./media/resource-manager-core-quotas-request/select-spot-type.png)

       W obszarze **Typy**można zażądać zarówno standardowych, jak i punktowych typów przydziałów z pojedynczego przypadku pomocy technicznej za pośrednictwem obsługi wielu selekcji.

       Aby uzyskać więcej informacji, zobacz [Przydział standardowy: Zwiększanie limitów według serii maszyn wirtualnych](per-vm-quota-requests.md).

   1. Wprowadź nowy limit przydziału, który ma dla tej subskrypcji.

      ![Wybieranie nowego przydziału dla maszyny wirtualnej punktowej](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Aby zażądać zwiększenia przydziału dla więcej niż jednej lokalizacji, wybierz dodatkową lokalizację w **obszarze Lokalizacje**, a następnie wybierz odpowiedni typ maszyny Wirtualnej. Następnie można wprowadzić limit, który ma zastosowanie do dodatkowej lokalizacji.

   ![Określanie dodatkowych lokalizacji w szczegółach przydziału](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Wybierz **pozycję Zapisz i kontynuuj** tworzenie żądania pomocy technicznej.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Żądanie zwiększenia limitu przydziału w okienku Subskrypcje

Aby zażądać zwiększenia limitu przydziału punktowego dla wszystkich serii maszyn wirtualnych z okienka **Subskrypcje:**

> [!NOTE]
> Można również zażądać zwiększenia limitu przydziału dla wielu regionów za pośrednictwem jednego przypadku pomocy technicznej. Szczegółowe informacje można znaleźć w kroku 7.

1. W [witrynie Azure portal](https://portal.azure.com)wyszukaj i wybierz **pozycję Subskrypcje**.

   ![Subskrypcje w wyszukiwarce witryny Azure portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Subskrypcje do wyboru dla zmian](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. W lewym okienku wybierz **pozycję Użycie + przydziały**.

   ![Łącze "Użycie + przydziały"](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. W prawym górnym rogu wybierz pozycję **Poproś o zwiększenie**.

   ![Wybierz, aby zwiększyć przydział](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. W przypadku **typu przydziału**wybierz limit **subskrypcji Compute-VM (cores-vCPU).**

   ![Wybieranie typu przydziału](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Wybierz **dalej: Rozwiązania,** aby otworzyć **SZCZEGÓŁY PROBLEMU**. Wybierz **pozycję Podaj szczegóły,** aby wprowadzić dodatkowe informacje. W **szczegóły przydziału**wprowadź następujące informacje:

   1. W przypadku **modelu wdrażania**wybierz odpowiedni model, a w przypadku **lokalizacji**wybierz lokalizację.

      ![Podaj szczegóły przydziału](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Dla wybranej lokalizacji w obszarze **Typy**w **obszarze Wybierz typ**wybierz pozycję **Punkt**.

      ![Wybierz typ spotu](./media/resource-manager-core-quotas-request/select-spot-type.png)

      Aby uzyskać więcej informacji, zobacz [Przydział standardowy: Zwiększanie limitów według serii maszyn wirtualnych](per-vm-quota-requests.md).

   1. Wprowadź nowy limit przydziału, który ma dla tej subskrypcji.

      ![Wprowadzanie nowej wartości dla limitu vCPU](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Aby zażądać zwiększenia przydziału dla więcej niż jednej lokalizacji, wybierz dodatkową lokalizację w **obszarze Lokalizacje**, a następnie wybierz odpowiedni typ maszyny Wirtualnej. Następnie można wprowadzić limit, który ma zastosowanie do dodatkowej lokalizacji.

   ![Wybieranie dodatkowych lokalizacji w szczegółach przydziału](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Wybierz **pozycję Zapisz i kontynuuj** tworzenie żądania pomocy technicznej.
