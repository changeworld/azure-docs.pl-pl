---
title: Limit przydziału usługi Azure Resource Manager procesorów vCPU zwiększyć żądań | Dokumentacja firmy Microsoft
description: Żądań zwiększenia limitu przydziału usługi Azure Resource Manager procesorów wirtualnych.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 05/09/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9d19d1a993d574777aa630b9c58f2472b94716cd
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479423"
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Żądań zwiększenia limitu przydziału procesorów wirtualnych usługi Resource Manager

Limity przydziału procesorów wirtualnych Menedżera zasobów, dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych są wymuszane w dwóch warstwach dla każdej subskrypcji, w każdym regionie. 

Pierwsza warstwa jest całkowita regionalny limit procesorów wirtualnych Vcpu (we wszystkich maszyn wirtualnych serii), a druga warstwa limit procesorów wirtualnych Vcpu serię maszyn wirtualnych (np. wirtualnych procesorów CPU serii D). Zawsze, gdy nowa maszyna wirtualna ma zostać wdrożony, Suma użytkowania nowych i istniejących wirtualnych procesorów CPU w tej serii maszyny Wirtualnej nie może przekraczać limit przydziału procesorów wirtualnych, zatwierdzone dla tej konkretnej maszyny Wirtualnej serii dalsze, liczba całkowita procesora wirtualnego vCPU wdrożone we wszystkich maszyn wirtualnych serii nie może przekraczać łączny regionalnego przydziału procesorów wirtualnych  zatwierdzone dla subskrypcji. Jeśli jedno z tych limitów przydziału zostaną przekroczone, wdrażanie maszyny Wirtualnej będzie niemożliwe.
Można zażądać zwiększenia limitu przydziału procesorów wirtualnych na maszyny Wirtualne serii z witryny Azure portal. Zwiększenie limitu przydziału maszyny Wirtualnej serii automatycznie zwiększa łączny regionalny limit procesorów wirtualnych Vcpu jednakową kwotę. 

Podczas tworzenia nowej subskrypcji, domyślne całkowita regionalnych procesorów wirtualnych Vcpu może nie być równa sumie limity przydziału procesorów wirtualnych domyślne dla wszystkich poszczególnych maszyn wirtualnych serii ten może powodować w ramach subskrypcji za pomocą wystarczający przydział dla każdej poszczególnych serii maszyny Wirtualnej, który chcesz wdrożyć , ale subskrypcja ma wystarczający przydział dla łączna liczba procesorów wirtualnych Vcpu dla wszystkich wdrożeń. W takim przypadku należy przesłać żądanie zwiększyć wzrost łącznego limitu przydziału dla limitu region jawnie. Łączna liczba procesorów wirtualnych Vcpu limit nie może przekraczać sumę zatwierdzonych przydziału we wszystkich serię maszyn wirtualnych dla regionu.

Więcej informacji na temat limitów przydziału na [stronie limity przydziału procesorów wirtualnych maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) i [limity usług i subskrypcji platformy Azure](https://aka.ms/quotalimits) strony. 

Teraz można zażądać zwiększenia za pośrednictwem **Pomoc i obsługa techniczna** bloku lub **użycia i przydziałów** bloku w portalu. 

## <a name="request-quota-increase-using-the-help--support-blade"></a>Zwiększ limit przydziału żądanie, używając **Pomoc i obsługa techniczna** bloku

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


## <a name="request-quota-increase-at-subscription-level-using-usages--quota"></a>Zażądać zwiększenia limitu przydziału na poziomie subskrypcji przy użyciu wartości użycia i przydziałów

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


