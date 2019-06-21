---
title: Sieć zwiększono limit | Dokumentacja firmy Microsoft
description: Zwiększono limit sieci
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 48d7e9cc4a3034e149901931f2addbc7df78e2bc
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297220"
---
# <a name="networking-limit-increase"></a>Zwiększono limit sieci

Aby wyświetlić bieżące użycia sieci i limit przydziału, możesz odwiedzić stronę **użycia i przydziałów** bloku w witrynie Azure portal. Umożliwia także użycie [interfejsu wiersza polecenia](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) lub [użycie interfejsu API sieci](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) do wyświetlania użycia sieci i limitów.

Możesz poprosić o zwiększenie za pośrednictwem **Pomoc i obsługa techniczna** bloku lub **użycia i przydziałów** bloku w portalu.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Zażądać zwiększenia limitu przydziału sieci na poziomie przy użyciu subskrypcji **Pomoc i obsługa techniczna** bloku

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej za pośrednictwem platformy Azure dostępne w witrynie Azure portal bloku "Pomoc i obsługa techniczna". 

1. Z https://portal.azure.com, wybierz opcję **Pomoc i obsługa techniczna**.

    ![Pomoc i obsługa techniczna](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Wybierz pozycję **Nowe żądanie obsługi**. 

    ![Nowe żądanie pomocy technicznej](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Z listy rozwijanej Typ problemu wybierz **limity usług i subskrypcji (przydziały)** .

    ![Menu rozwijanego Typ problemu](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

    ![Wybieranie subskrypcji newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Wybierz **sieć** w **typ limitu przydziału** listy rozwijanej. 

    ![Wybierz typ limitu przydziału](./media/networking-quota-request/select-quota-type-network.png)

6. W **szczegóły problemu**, zapewniają dodatkowe informacje, aby ułatwić proces żądania, klikając **Podaj szczegóły**.

    ![Podaj szczegóły](./media/resource-manager-core-quotas-request/provide-details.png)

7. W **szczegóły limitu przydziału** panelu, wybierz model wdrażania, lokalizacji i zasobów, które chcesz poprosić o zwiększenie.

    ![Szczegóły limitu przydziału DM](./media/networking-quota-request/quota-details-network.png)

8.  Wprowadź nowe wartości graniczne, które Twoim zdaniem w ramach subskrypcji. Aby usunąć wiersz, usuń zaznaczenie pola wyboru zasobów z listy rozwijanej zasobów, lub kliknij ikonę odrzucenia "x". Po wprowadzeniu żądany limit przydziału dla poszczególnych zasobów, kliknij przycisk **Zapisz i Kontynuuj** na panel szczegółów przydziału, aby kontynuować tworzenie żądania pomocy technicznej.

    ![Nowe limity](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Zażądać zwiększenia limitu przydziału sieci na poziomie przy użyciu subskrypcji **użycia i przydziałów** bloku

Postępuj zgodnie z instrukcjami poniżej z użyciem, aby utworzyć żądanie pomocy technicznej za pośrednictwem platformy Azure "użycie i limit przydziału" bloku dostępne w witrynie Azure portal. 

1. Z https://portal.azure.com, wybierz opcję **subskrypcje**.

    ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

    ![Wybieranie subskrypcji](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Wybierz **użycie i przydziały**

    ![Wybierz opcję użycie i przydziały](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. W prawym górnym rogu wybierz **zażądać zwiększenia**.

    ![Zażądać zwiększenia](./media/resource-manager-core-quotas-request/request-increase.png)

5. Postępuj zgodnie z instrukcjami, rozpoczynając od kroku 3 z *sieci żądań zwiększenia limitu przydziału na poziomie subskrypcji* sekcji przy użyciu **Pomoc i obsługa techniczna** sekcji bloku

## <a name="about-networking-limits"></a>Limity dotyczące sieci — informacje

Aby dowiedzieć się więcej na temat limity dotyczące sieci, zobacz [sekcji sieci](../azure-subscription-service-limits.md#networking-limits) strony ograniczenia lub naszej sieci limity — często zadawane pytania