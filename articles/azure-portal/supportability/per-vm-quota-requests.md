---
title: Żądanie zwiększenia limitów przydziału procesora wirtualnego na serię maszyn wirtualnych platformy Azure
description: Jak zażądać zwiększenia limitu przydziału procesora wirtualnego dla serii maszyn wirtualnych w witrynie Azure portal, co zwiększa całkowity regionalny limit vCPU o tę samą kwotę.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: de06375dad5999a29691435317e62585a2ea7f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843749"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Przydział standardowy: zwiększanie limitów według serii maszyn wirtualnych

Usługa Azure Resource Manager obsługuje dwa typy przydziałów wirtualnych dla maszyn wirtualnych:

* *Maszyny wirtualne* zgodnie z rzeczywistym użyciem i *zarezerwowane wystąpienia maszyn wirtualnych* podlegają *standardowemu przydziałowi vCPU*.
* *Maszyny wirtualne punktowe* podlegają *inkcjom owym przydziałowi vCPU*.

Standardowy przydział vCPU dla wystąpień płatności zgodnie z rzeczywistym użyciem i zarezerwowanej maszyny wirtualnej jest wymuszany w dwóch warstwach dla każdej subskrypcji w każdym regionie:

* Pierwsza warstwa to *całkowity regionalny limit procesorów wirtualnych*we wszystkich seriach maszyn wirtualnych.
* Druga warstwa to *limit procesorów wirtualnych serii na maszynę wirtualną dla maszyn wirtualnych,* taki jak procesory wirtualne z serii Dv3.

Za każdym razem, gdy wdrażasz nową maszynę wirtualną punktową, całkowite nowe i istniejące użycie procesora wirtualnego dla tej serii maszyn wirtualnych nie może przekraczać zatwierdzonego przydziału vCPU dla danej serii maszyn wirtualnych. Ponadto całkowita liczba nowych i istniejących procesorów wirtualnych, które są wdrażane we wszystkich seriach maszyn wirtualnych, nie powinna przekraczać całkowitego zatwierdzonego przydziału regionalnych procesorów wirtualnych dla subskrypcji. Jeśli którykolwiek z tych przydziałów zostanie przekroczony, wdrożenie maszyny Wirtualnej nie jest dozwolone.

Można zażądać zwiększenia limitu przydziału procesora wirtualnego dla serii maszyn wirtualnych przy użyciu witryny Azure portal. Zwiększenie przydziału serii maszyn wirtualnych automatycznie zwiększa całkowity regionalny limit vCPU o tę samą kwotę.

Aby dowiedzieć się więcej o standardowych przydziałach procesora wirtualnego, zobacz [Przydziały vCPU maszyn wirtualnych](../../virtual-machines/windows/quotas.md) oraz [limity subskrypcji i usług platformy Azure](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests).

Aby dowiedzieć się więcej o zwiększaniu limitu vCPU według regionu dla przydziału standardowego, zobacz [Przydział standardowy: Zwiększanie limitów według regionu](regional-quota-requests.md).

Aby dowiedzieć się więcej o zwiększaniu limitów wirtualnych maszyn wirtualnych punktowych, zobacz [Przydział punktowy: Zwiększanie limitów dla wszystkich serii maszyn wirtualnych](low-priority-quota.md).

Można zażądać zwiększenia standardowych limitów przydziału vCPU na serię maszyn wirtualnych na jeden z dwóch sposobów, zgodnie z opisem w poniższych sekcjach.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Żądanie standardowego zwiększenia przydziału z pomocy + wsparcie

Aby zażądać standardowego zwiększenia przydziału procesora wirtualnego na serię maszyn wirtualnych z **pomocy + pomocy technicznej:**

> [!NOTE]
> Można również zażądać zwiększenia limitu przydziału dla wielu regionów za pośrednictwem jednego przypadku pomocy technicznej. Szczegółowe informacje można znaleźć w kroku 8.

1. W menu [portalu platformy Azure](https://portal.azure.com) wybierz pozycję Pomoc + pomoc **techniczna**.

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

1. W **szczegóły przydziału**wykonaj następujące czynności:

   ![TProvide dodatkowe szczegóły przydziału](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. W przypadku **modelu wdrażania**wybierz odpowiedni model.

   1. W obszarze **Lokalizacje**wybierz lokalizację. Dla wybranej lokalizacji w obszarze **Typy**w **obszarze Wybierz typ**wybierz pozycję **Standardowy**.

      ![Szczegóły przydziału — typy przydziałów](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      W obszarze **Typy**można zażądać zarówno standardowych, jak i punktowych typów przydziałów z pojedynczego przypadku pomocy technicznej za pośrednictwem obsługi wielu selekcji.

      Aby uzyskać więcej informacji na temat zwiększania limitów przydziałów [punktowych, zobacz Maszyny wirtualne spot platformy Azure dla zestawów skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/use-spot.md).

   1. W **standardzie**wybierz serię jednostek SKU dla zwiększenia przydziałów.

      ![Szczegóły przydziału — seria SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Wprowadź nowe limity przydziałów, które chcesz dla tej subskrypcji. Aby usunąć jednostkę SKU z listy, wyczyść pole wyboru obok jednostki SKU lub wybierz ikonę odrzuć "X".

      ![Wybieranie nowego limitu vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Aby zażądać zwiększenia przydziału dla więcej niż jednej lokalizacji, wybierz dodatkową lokalizację w **obszarze Lokalizacje**, a następnie wybierz odpowiedni typ maszyny Wirtualnej. Następnie można wprowadzić limit, który ma zastosowanie do dodatkowej lokalizacji.

   ![Określanie dodatkowych lokalizacji w szczegółach przydziału](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Wybierz **pozycję Zapisz i kontynuuj** tworzenie żądania pomocy technicznej.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Żądanie standardowego zwiększenia przydziału z subskrypcji

Aby zażądać standardowego zwiększenia przydziału procesora wirtualnego na serię maszyn wirtualnych z **subskrypcji:**

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

1. W **szczegóły przydziału**wykonaj następujące czynności:

   1. W przypadku **modelu wdrażania**wybierz odpowiedni model, a w przypadku **lokalizacji**wybierz lokalizację.

      ![Podaj szczegóły przydziału](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Dla wybranej lokalizacji w obszarze **Typy**wybierz pozycję **Wybierz typ**, a następnie wybierz pozycję **Standardowy**.

      ![Wybierz typ standardowy](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      W obszarze **Typy**można zażądać zarówno standardowych, jak i punktowych typów przydziałów z pojedynczego przypadku pomocy technicznej za pośrednictwem obsługi wielu selekcji.

      Aby uzyskać więcej informacji na temat zwiększania limitów przydziałów [punktowych, zobacz Maszyny wirtualne spot platformy Azure dla zestawów skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/use-spot.md).

   1. W przypadku **opcji Standardowa**wybierz serię jednostek SKU, których przydziały chcesz zwiększyć.

      ![Szczegóły przydziału — seria SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Wprowadź nowe limity przydziałów, które chcesz dla tej subskrypcji. Aby usunąć jednostkę SKU z listy, usuń zaznaczenie pola wyboru obok jednostki SKU lub wybierz ikonę odrzuć "X".

      ![Wybieranie nowego limitu vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Aby zażądać zwiększenia przydziału dla więcej niż jednej lokalizacji, wybierz dodatkową lokalizację w **obszarze Lokalizacje**, a następnie wybierz odpowiedni typ maszyny Wirtualnej.

   Ten krok wstępnie ładuje serię jednostek SKU, która została wybrana dla wcześniejszych lokalizacji. Wprowadź limity przydziału, które chcesz zastosować do dodatkowej serii.

   ![Wybieranie dodatkowych lokalizacji w szczegółach przydziału](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Wybierz **pozycję Zapisz i kontynuuj** tworzenie żądania pomocy technicznej.
