---
title: Zwiększenie limitu sieci | Microsoft Docs
description: Zwiększanie limitu sieci
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 278e9ff68fa20a0a99a6447bb4cf7ac7fddbfb7b
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249114"
---
# <a name="networking-limit-increase"></a>Zwiększanie limitu sieci

Aby wyświetlić bieżące użycie sieci i limit przydziału, można odwiedzić blok **użycie i limit przydziału** w Azure Portal. Do wyświetlania użycia i limitów sieci można także użyć interfejsu [wiersza polecenia](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages)użycia, [programu PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) lub [API użycia sieci](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) .

Możesz poprosić o zwiększenie za pośrednictwem bloku **Pomoc i obsługa techniczna** lub blok **użycie + przydział** w portalu.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Zażądaj zwiększenia przydziału sieci na poziomie subskrypcji przy użyciu bloku **Pomoc i obsługa techniczna**

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej za pomocą bloku "pomoc i obsługa techniczna platformy Azure" dostępnego w Azure Portal. 

1. W https://portal.azure.com wybierz pozycję **Pomoc i obsługa techniczna**.

    ![Pomoc i obsługa techniczna](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Wybierz pozycję **Nowe żądanie obsługi**. 

    ![Nowe żądanie pomocy technicznej](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Z listy rozwijanej Typ problemu wybierz pozycję **usługi i limity subskrypcji (przydziały)** .

    ![Lista rozwijana typu problemu](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

    ![Wybieranie Aktualności subskrypcji](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Wybierz pozycję **Sieć** na liście rozwijanej **Typ limitu przydziału** . 

    ![Wybierz typ limitu przydziału](./media/networking-quota-request/select-quota-type-network.png)

6. W obszarze **Szczegóły problemu**podaj dodatkowe informacje, aby pomóc w przetwarzaniu żądania przez kliknięcie pozycji **Podaj szczegóły**.

    ![Podaj szczegóły](./media/resource-manager-core-quotas-request/provide-details.png)

7. W panelu **Szczegóły przydziału** wybierz pozycję model wdrażania, lokalizację i zasoby, dla których chcesz zażądać zwiększenia.

    ![Szczegóły przydziału DM](./media/networking-quota-request/quota-details-network.png)

8.  Wprowadź nowe limity, które chcesz wykonać w ramach subskrypcji. Aby usunąć wiersz, usuń zaznaczenie zasobu z listy rozwijanej zasób lub kliknij ikonę Odrzuć "x". Po wprowadzeniu żądanego przydziału dla każdego zasobu kliknij przycisk **Zapisz i Kontynuuj** w panelu Szczegóły przydziału, aby kontynuować tworzenie żądania obsługi.

    ![Nowe limity](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Zażądaj zwiększenia przydziału sieci na poziomie subskrypcji przy użyciu bloku **użycia i limitu przydziału**

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej za pomocą bloku "użycie i limit przydziału" platformy Azure dostępnego w Azure Portal. 

1. W https://portal.azure.com wybierz pozycję **subskrypcje**.

    ![Subskrypcje](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

    ![Wybierz subskrypcję](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Wybierz **użycie i przydziały**

    ![Wybierz użycie i przydziały](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. W prawym górnym rogu wybierz pozycję **Zwiększ liczbę żądań**.

    ![Zwiększ liczbę żądań](./media/resource-manager-core-quotas-request/request-increase.png)

5. Wykonaj kroki zaczynające się od kroku 3 z sekcji *żądanie zwiększenia przydziału sieci w ramach subskrypcji* , korzystając z sekcji w bloku **Pomoc i obsługa techniczna** .

## <a name="about-networking-limits"></a>Informacje o limitach sieci

Aby dowiedzieć się więcej na temat limitów sieci, zobacz sekcję dotyczącą [sieci](../azure-subscription-service-limits.md#networking-limits) na stronie limity lub nasza liczba często zadawanych pytań dotyczących sieci