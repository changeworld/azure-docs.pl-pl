---
title: Klasyczny model wdrażania platformy Azure
description: Model wdrażania klasycznego, teraz zastępowany przez model Menedżera zasobów, wymusza globalny limit przydziału vCPU dla maszyn wirtualnych i zestawów skalowania maszyny wirtualnej.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: a3d5106cafc1d3bfe77f3e42e85cedb668fc4fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76835635"
---
# <a name="classic-deployment-model"></a>Klasyczny model wdrażania

Klasyczny model wdrażania jest starszej generacji modelu wdrażania platformy Azure. Wymusza globalny limit przydziału vCPU dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych. Model wdrażania klasycznego nie jest już zalecany i jest teraz zastępowany przez model Menedżera zasobów.

Aby dowiedzieć się więcej o tych dwóch modelach wdrażania i zaletach korzystania z Menedżera zasobów, zobacz [Menedżer zasobów i wdrażanie klasyczne](../../azure-resource-manager/management/deployment-models.md).

Podczas tworzenia nowej subskrypcji jest do niej przypisywany domyślny przydział procesorów wirtualnych. Za każdym razem, gdy nowa maszyna wirtualna ma zostać wdrożona przy użyciu klasycznego modelu wdrażania, suma nowego i istniejącego użycia procesora wirtualnego we wszystkich regionach nie może przekraczać przydziału vCPU zatwierdzonego dla klasycznego modelu wdrażania.

Aby dowiedzieć się więcej o przydziałach, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Można zażądać zwiększenia limitu przydziału procesora wirtualnego dla modelu wdrażania klasycznego. Użyj **pomocy + pomocy lub** użycia + **przydziałów** w witrynie Azure portal.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Żądanie zwiększenia przydziału vCPU serii maszyn wirtualnych na poziomie subskrypcji przy użyciu pomocy + pomocy technicznej

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej przy użyciu **pomocy + pomocy technicznej** w witrynie Azure portal.

1. Z menu [portalu platformy Azure](https://portal.azure.com) wybierz pozycję Pomoc + obsługa **.**

   ![Wybierz Pomoc + pomoc w witrynie Azure portal](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Wybierz **pozycję Nowe żądanie pomocy technicznej**.

   ![Tworzenie nowego żądania pomocy technicznej w witrynie Azure portal](./media/resource-manager-core-quotas-request/new-support-request.png)

1. W **sekcji Typ wydania**wybierz pozycję **Limity usługi i subskrypcji (przydziały)**.

   ![Wybierz przydziały jako typ problemu](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Wybierz subskrypcję, dla której chcesz zwiększyć przydział](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. W przypadku **typu przydziału**wybierz limit **subskrypcji Compute -VM (cores-vCPU).**

   ![Wybierz typ przydziału, aby zwiększyć](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Wybierz **dalej: Rozwiązania,** aby otworzyć **SZCZEGÓŁY PROBLEMU**. Wybierz **pozycję Podaj szczegóły,** aby podać dodatkowe informacje.

   ![Podaj szczegóły, aby pomóc w zgłoszeniu](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. W **obszarze Szczegóły przydziału**wybierz pozycję **Klasyczny** i wybierz **pozycję Lokalizacja**.

   ![Dodawanie szczegółów, w tym modelu wdrażania i lokalizacji](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. W przypadku **rodziny jednostek SKU**wybierz jedną lub więcej rodzin jednostek SKU, które mają wzrosnąć.

   ![Określ rodzinę jednostek SKU, aby zwiększyć](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Wprowadź nowe limity, które chcesz w subskrypcji. Aby usunąć wiersz, usuń zaznaczenie jednostki SKU z **rodziny jednostek SKU** lub wybierz ikonę odrzuć "X". Po wprowadzeniu przydziału dla każdej rodziny jednostek SKU wybierz pozycję **Zapisz i kontynuuj** w **szczegółach przydziału,** aby kontynuować żądanie pomocy technicznej.

   ![Poproś o nowe limity](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Żądanie na zwiększenie przydziału vCPU serii maszyn wirtualnych na poziomie subskrypcji przy użyciu + przydziały

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej przy użyciu **użycia + przydziałów** w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com)wyszukaj i wybierz **pozycję Subskrypcje**.

   ![Przejdź do subskrypcji w witrynie Azure portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Wybierz subskrypcję do zmodyfikowania](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Wybierz **opcję Użycie + przydziały**.

   ![Wybieranie użycia i przydziałów dla subskrypcji](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. W prawym górnym rogu wybierz pozycję **Poproś o zwiększenie**.

   ![Wybierz, aby zwiększyć przydział](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Wybierz **limit subskrypcji Compute-VM (cores-vCPU) zwiększa się** wraz z **typem przydziału**.

   ![Wybierz typ przydziału](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Wybierz **dalej: Rozwiązania,** aby otworzyć **SZCZEGÓŁY PROBLEMU**. Wybierz **pozycję Podaj szczegóły,** aby podać dodatkowe informacje.

   ![Podaj szczegóły dotyczące swojego żądania](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. W **obszarze Szczegóły przydziału**wybierz pozycję **Klasyczny** i **Lokalizacja**.

   ![Wybieranie szczegółów przydziału, w tym modelu wdrożenia i lokalizacji](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Wybierz jedną lub więcej rodzin jednostek SKU dla zwiększenia.

   ![Wybierz rodzinę jednostek SKU, aby zwiększyć](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Wprowadź nowe limity, które chcesz w subskrypcji. Aby usunąć wiersz, usuń zaznaczenie jednostki SKU z **rodziny jednostek SKU** lub wybierz ikonę odrzuć "X". Po wprowadzeniu przydziału dla każdej rodziny jednostek SKU wybierz pozycję **Zapisz i kontynuuj** w **szczegółach przydziału,** aby kontynuować żądanie pomocy technicznej.

   ![Wprowadź nowy przydział](./media/resource-manager-core-quotas-request/new-limits-classic.png)

