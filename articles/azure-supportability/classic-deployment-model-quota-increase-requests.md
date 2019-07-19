---
title: Klasyczny model wdrażania platformy Azure | Microsoft Docs
description: Klasyczny model wdrażania platformy Azure
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 70ad44eade871d52591014ee24e645b95c52f1e5
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234872"
---
# <a name="classic-deployment-model"></a>Klasyczny model wdrażania

Klasyczny model wdrażania jest starszym trybem wdrażania platformy Azure, który wymusza globalne limity przydziału vCPU dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych. Klasyczny model wdrażania nie jest już zalecany i jest teraz zastępowany przez Menedżer zasobów model. Aby dowiedzieć się więcej na temat tych dwóch modeli wdrażania i zalet Menedżer zasobów można znaleźć na stronie Menedżer zasobów model wdrażania. Po utworzeniu nowej subskrypcji zostanie do niej przypisany domyślny przydział procesorów wirtualnych vCPU. Gdy nowa maszyna wirtualna ma zostać wdrożona przy użyciu klasycznego modelu wdrażania, suma nowych i istniejących procesorów wirtualnych vCPU użycie we wszystkich regionach nie może przekroczyć limitu przydziału vCPU zatwierdzonego dla klasycznego modelu wdrażania. Dowiedz się więcej o przydziałach na [stronie limitów subskrypcji i usług platformy Azure](https://aka.ms/quotalimits)

Można zażądać zwiększenia limitu procesorów wirtualnych vCPU dla klasycznego modelu wdrażania za pośrednictwem bloku pomoc i obsługa techniczna oraz bloku użycie i przydział w portalu.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Żądanie zwiększania limitu przydziału vCPU na poziomie subskrypcji przy użyciu bloku **Pomoc i obsługa techniczna**

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej za pomocą bloku "pomoc i obsługa techniczna platformy Azure" dostępnego w Azure Portal. 

1. W https://portal.azure.com programie wybierz pozycję **Pomoc i obsługa techniczna**.

   ![Pomoc i obsługa techniczna](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Wybierz pozycję **Nowe żądanie obsługi**. 

      ![Nowe żądanie pomocy technicznej](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Z listy rozwijanej Typ problemu wybierz pozycję **usługi i limity subskrypcji (przydziały)** .

   ![Lista rozwijana typu problemu](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

   ![Wybieranie Aktualności subskrypcji](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Wybierz pozycję **COMPUTE-VM (rdzenie-procesorów wirtualnych vCPU) limit subskrypcji** na liście rozwijanej **Typ limitu przydziału** . 

   ![Wybierz typ limitu przydziału](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. W obszarze **Szczegóły problemu**podaj dodatkowe informacje, aby pomóc w przetwarzaniu żądania przez kliknięcie pozycji **Podaj szczegóły**.

   ![Podaj szczegóły](./media/resource-manager-core-quotas-request/provide-details.png)

7. W panelu **Szczegóły przydziału** wybierz pozycję klasyczne i wybierz lokalizację.

   ![Szczegóły przydziału DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Wybierz **rodziny SKU** , które wymagają zwiększenia. 

   ![Rodzina SKU](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Wprowadź nowe limity, które chcesz wykonać w ramach subskrypcji. Aby usunąć wiersz, usuń zaznaczenie jednostki SKU z listy rozwijanej rodzina SKU lub kliknij ikonę Odrzuć "x". Po wprowadzeniu żądanego przydziału dla każdej rodziny SKU kliknij pozycję **Zapisz i Kontynuuj** w panelu Szczegóły przydziału, aby kontynuować tworzenie żądania obsługi.

   ![Nowe limity](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Żądanie zwiększania limitu przydziału vCPU na poziomie subskrypcji przy użyciu bloku **użycia + limitu przydziału**

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej za pomocą bloku "użycie i limit przydziału" platformy Azure dostępnego w Azure Portal. 

1. W https://portal.azure.com programie wybierz pozycję **subskrypcje**.

   ![Subskrypcje](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

   ![Wybieranie subskrypcji](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Wybierz **użycie i** przydziały

   ![Wybierz użycie i przydziały](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. W prawym górnym rogu wybierz pozycję **Zwiększ liczbę żądań**.

   ![Zwiększ liczbę żądań](./media/resource-manager-core-quotas-request/request-increase.png)

5. Wybierz pozycję **COMPUTE-VM (rdzenie-procesorów wirtualnych vCPU) limit subskrypcji rośnie** jako typ cudzysłowu. 

   ![Wypełnij formularz](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. W obszarze **Szczegóły problemu**podaj dodatkowe informacje, aby pomóc w przetwarzaniu żądania przez kliknięcie pozycji **Podaj szczegóły**.

   ![Podaj szczegóły](./media/resource-manager-core-quotas-request/provide-details.png)

7. W panelu **Szczegóły przydziału** wybierz pozycję klasyczne i wybierz lokalizację.

   ![Szczegóły przydziału DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Wybierz **rodziny SKU** , które wymagają zwiększenia. 

   ![Rodzina SKU](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Wprowadź nowe limity, które chcesz wykonać w ramach subskrypcji. Aby usunąć wiersz, usuń zaznaczenie jednostki SKU z listy rozwijanej rodzina SKU lub kliknij ikonę Odrzuć "x". Po wprowadzeniu żądanego przydziału dla każdej rodziny SKU kliknij pozycję **Zapisz i Kontynuuj** w panelu Szczegóły przydziału, aby kontynuować tworzenie żądania obsługi.

   ![Nowe limity](./media/resource-manager-core-quotas-request/new-limits-classic.png)

