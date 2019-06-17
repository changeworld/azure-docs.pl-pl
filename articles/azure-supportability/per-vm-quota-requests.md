---
title: Zwiększ limit przydziału procesorów wirtualnych platformy Azure dla maszyny Wirtualnej, żądania | Dokumentacja firmy Microsoft
description: na limit przydziału procesorów wirtualnych maszyn wirtualnych należy zwiększyć żądań
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: f921b4a95c1b0cfb29d84c0bacc17d268af6e6c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082822"
---
# <a name="vm-series-vcpu-limit-increase"></a>Zwiększono limit procesorów wirtualnych serię maszyn wirtualnych

Teraz można zażądać zwiększenia za pośrednictwem **Pomoc i obsługa techniczna** bloku lub **użycia i przydziałów** bloku w portalu. 

## <a name="request-per-vm-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Żądania dla zwiększenia limitu przydziału procesorów wirtualnych maszyn wirtualnych na poziomie przy użyciu subskrypcji **Pomoc i obsługa techniczna** bloku

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej za pośrednictwem platformy Azure dostępne w witrynie Azure portal bloku "Pomoc i obsługa techniczna". 

1. Z https://portal.azure.com, wybierz opcję **Pomoc i obsługa techniczna**.

![Pomoc i obsługa techniczna](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Wybierz pozycję **Nowe żądanie obsługi**. 

![Nowe żądanie pomocy technicznej](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Z listy rozwijanej Typ problemu wybierz **limity usług i subskrypcji (przydziały)** .

![Menu rozwijanego Typ problemu](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

![Wybieranie subskrypcji newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Wybierz **obliczenia limit subskrypcji — maszyn wirtualnych (rdzeni Vcpu) zwiększa** w **typ limitu przydziału** listy rozwijanej. 

![Wybierz typ limitu przydziału](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. W **szczegóły problemu**, zapewniają dodatkowe informacje, aby ułatwić proces żądania, klikając **Podaj szczegóły**.

![Podaj szczegóły](./media/resource-manager-core-quotas-request/provide-details.png)

7. W **szczegóły limitu przydziału** panelu, wybierz model wdrażania i wybierz lokalizację.

![Szczegóły limitu przydziału DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. Wybierz **rodzin SKU** wymagających zwiększenia. 

![Rodzina SKU](./media/resource-manager-core-quotas-request/sku-family.png)

9. Wprowadź nowe wartości graniczne, które Twoim zdaniem w ramach subskrypcji. Aby usunąć wiersz, usuń zaznaczenie pola wyboru jednostki SKU z listy rozwijanej rodziny SKU, lub kliknij ikonę odrzucenia "x". Po wprowadzeniu żądany limit przydziału dla każdej rodziny SKU, kliknij przycisk **Zapisz i Kontynuuj** na panel szczegółów przydziału, aby kontynuować tworzenie żądania pomocy technicznej.

![Nowe limity](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Żądanie dla zwiększenia limitu przydziału procesorów wirtualnych maszyn wirtualnych na poziomie przy użyciu subskrypcji **użycia i przydziałów** bloku

Postępuj zgodnie z instrukcjami poniżej z użyciem, aby utworzyć żądanie pomocy technicznej za pośrednictwem platformy Azure "użycie i limit przydziału" bloku dostępne w witrynie Azure portal. 

1. Z https://portal.azure.com, wybierz opcję **subskrypcje**.

![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

![Wybieranie subskrypcji](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Wybierz **użycie i przydziały**

![Wybierz opcję użycie i przydziały](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. W prawym górnym rogu wybierz **zażądać zwiększenia**.

![Zażądać zwiększenia](./media/resource-manager-core-quotas-request/request-increase.png)

5. Wybierz **limit subskrypcji obliczeniowych maszyn wirtualnych (rdzeni Vcpu) zwiększa** jako typ oferty. 

![Wypełnij formularz](./media/resource-manager-core-quotas-request/forms.png)
   
6. W **szczegóły limitu przydziału** panelu, wybierz model wdrażania i wybierz lokalizację.

![Blok Problem limitu przydziału](./media/resource-manager-core-quotas-request/problemstep.png)

7. Wybierz **rodzin SKU** wymagających zwiększenia.

![Serie jednostek SKU wybrane](./media/resource-manager-core-quotas-request/sku-family.png)

8. Wprowadź nowe wartości graniczne, które Twoim zdaniem w ramach subskrypcji. Aby usunąć wiersz, usuń zaznaczenie pola wyboru jednostki SKU z listy rozwijanej rodziny SKU, lub kliknij ikonę odrzucenia "x". Po wprowadzeniu żądany limit przydziału dla każdej rodziny SKU, kliknij przycisk **Zapisz i Kontynuuj** na stronie krok Problem, aby kontynuować tworzenie żądania pomocy technicznej.

![Nowe żądanie limitu przydziału jednostki SKU](./media/resource-manager-core-quotas-request/new-limits.png)

