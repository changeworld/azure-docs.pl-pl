---
title: Żądanie zwiększenia regionalnych limitów przydziałów platformy Azure w procesorze vCPU
description: Jak zażądać zwiększenia limitu przydziału procesora wirtualnego dla regionu w witrynie Azure portal.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9236989f7bbb4695db24f86a2a049f5bb58f302a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843688"
---
# <a name="standard-quota-increase-limits-by-region"></a>Przydział standardowy: zwiększanie limitów według regionów

Usługa Azure Resource Manager obsługuje dwa typy przydziałów wirtualnych dla maszyn wirtualnych:

* *Maszyny wirtualne* zgodnie z rzeczywistym użyciem i *zarezerwowane wystąpienia maszyn wirtualnych* podlegają *standardowemu przydziałowi vCPU*.
* *Maszyny wirtualne punktowe* podlegają *inkcjom owym przydziałowi vCPU*.

Standardowy przydział vCPU dla wystąpień płatności zgodnie z rzeczywistym użyciem i zarezerwowanej maszyny wirtualnej jest wymuszany w dwóch warstwach dla każdej subskrypcji w każdym regionie:

* Pierwsza warstwa to *całkowity regionalny limit procesorów wirtualnych*we wszystkich seriach maszyn wirtualnych.
* Druga warstwa to *limit procesorów wirtualnych serii na maszynę wirtualną dla maszyn wirtualnych,* taki jak procesory wirtualne z serii D.

Za każdym razem, gdy wdrażasz nową maszynę wirtualną punktową, całkowite nowe i istniejące użycie procesora wirtualnego dla tej serii maszyn wirtualnych nie może przekraczać zatwierdzonego przydziału vCPU dla danej serii maszyn wirtualnych. Ponadto całkowita liczba nowych i istniejących procesorów wirtualnych, które są wdrażane we wszystkich seriach maszyn wirtualnych, nie powinna przekraczać całkowitego zatwierdzonego przydziału regionalnych procesorów wirtualnych dla subskrypcji. Jeśli którykolwiek z tych przydziałów zostanie przekroczony, wdrożenie maszyny Wirtualnej nie jest dozwolone.

Można zażądać zwiększenia limitu przydziału procesora wirtualnego dla serii maszyn wirtualnych przy użyciu witryny Azure portal. Zwiększenie przydziału serii maszyn wirtualnych automatycznie zwiększa całkowity regionalny limit vCPU o tę samą kwotę.

Podczas tworzenia nowej subskrypcji domyślna całkowita liczba regionalnych procesorów wirtualnych może nie być równa całkowitemu domyślnemu przydziałowi vCPU dla wszystkich poszczególnych serii maszyn wirtualnych. Ta rozbieżność może spowodować subskrypcji z wystarczającą ilością przydziału dla każdej serii maszyn wirtualnych, które chcesz wdrożyć. Ale może nie być wystarczającej ilości przydziału, aby pomieścić całkowitą regionalną procesory wirtualne dla wszystkich wdrożeń. W takim przypadku należy przesłać żądanie jawnego zwiększenia limitu całkowitej liczby regionalnych procesorów wirtualnych. Całkowity regionalny limit vCPU nie może przekroczyć całkowitego zatwierdzonego przydziału we wszystkich seriach maszyn wirtualnych dla regionu.

Aby dowiedzieć się więcej o standardowych przydziałach procesora wirtualnego, zobacz [Przydziały vCPU maszyn wirtualnych oraz limity](../../virtual-machines/windows/quotas.md) [subskrypcji i usług platformy Azure, przydziały i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Aby dowiedzieć się więcej o zwiększaniu limitów wirtualnych maszyn wirtualnych punktowych, zobacz [Przydział punktowy: Zwiększanie limitów dla wszystkich serii maszyn wirtualnych](low-priority-quota.md).

Można zażądać zwiększenia standardowego limitu przydziału vCPU według regionu na jeden z dwóch sposobów.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Żądanie zwiększenia przydziału według regionu z pomocy + wsparcie

Aby zażądać zwiększenia przydziału vCPU według regionu z **pomocy + pomocy technicznej:**

1. Z menu [portalu platformy Azure](https://portal.azure.com) wybierz pozycję Pomoc + obsługa **.**

   ![Łącze "Pomoc + wsparcie"](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. W **pomocy + pomocy ,** wybierz nowe żądanie pomocy **.**

    ![Nowe żądanie pomocy technicznej](./media/resource-manager-core-quotas-request/new-support-request.png)

1. W obszarze **Typ problemu**wybierz pozycję **Limity usługi i subskrypcji (przydziały).**

   ![Wybieranie typu problemu](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. W przypadku **opcji Subskrypcja**wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Wybieranie subskrypcji](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Dla **typu przydziału**wybierz pozycję **Inne żądania**.

   ![Wybieranie typu przydziału](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Wybierz **dalej: Rozwiązania,** aby otworzyć **SZCZEGÓŁY PROBLEMU**. W **opisie**podaj następujące informacje:

    1. W przypadku **modelu wdrażania**określ **Menedżera zasobów**.  
    1. W obszarze **Region**określ wymagany region, na przykład **wschodnie stany USA 2**.  
    1. W przypadku **nowego limitu**określ nowy limit procesora wirtualnego dla regionu. Ta wartość nie powinna przekraczać sumy zatwierdzonych przydziałów dla poszczególnych serii jednostek SKU dla tej subskrypcji.

    ![Wprowadź szczegóły żądania przydziału](./media/resource-manager-core-quotas-request/regional-details.png)

1. Wybierz **opcję Przejrzyj + utwórz,** aby kontynuować tworzenie żądania pomocy technicznej.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Żądanie zwiększenia przydziału według regionów z subskrypcji

Aby zażądać zwiększenia przydziału procesora wirtualnego w policzeniu regionów z **subskrypcji:**

1. W [witrynie Azure portal](https://portal.azure.com)wyszukaj i wybierz **pozycję Subskrypcje**.

   ![Przejdź do subskrypcji w witrynie Azure portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Wybierz subskrypcję do zmodyfikowania](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. W lewym okienku wybierz **pozycję Użycie + przydziały**.

   ![Łącze Użycie i przydziały](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. W prawym górnym rogu wybierz pozycję **Poproś o zwiększenie**.

   ![Wybierz, aby zwiększyć przydział](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Z **typu przydziału**wybierz pozycję **Inne żądania**.

   ![Wybierz typ przydziału](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Wybierz **dalej: Rozwiązania,** aby otworzyć **SZCZEGÓŁY PROBLEMU**. W polu **Opis** podaj następujące dodatkowe informacje:

    1. W przypadku **modelu wdrażania**określ **Menedżera zasobów**.  
    1. W obszarze **Region**określ wymagany region, na przykład **wschodnie stany USA 2**.  
    1. W przypadku **nowego limitu**określ nowy limit procesora wirtualnego dla regionu. Ta wartość nie powinna przekraczać sumy zatwierdzonych przydziałów dla poszczególnych serii jednostek SKU dla tej subskrypcji.

    ![Wprowadzanie informacji w szczegółach](./media/resource-manager-core-quotas-request/regional-details.png)

1. Wybierz **opcję Przejrzyj + utwórz,** aby kontynuować tworzenie żądania pomocy technicznej.
