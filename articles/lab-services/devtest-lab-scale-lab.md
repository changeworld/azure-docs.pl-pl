---
title: Skaluj przydziały i limity w laboratorium w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak skalować laboratorium w Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ae624155-9181-45fa-bd2b-1983339b7e0e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 231a227cbccaa4cbf74f3b13839c0ba42895cf24
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644855"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Skalowanie przydziałów i limitów w laboratoriach DevTest Labs
Podczas pracy w DevTest Labs można zauważyć, że istnieją pewne domyślne limity niektórych zasobów platformy Azure, które mogą mieć wpływ na usługę DevTest Labs. Limity te są określane jako limity **przydziału**.

> [!NOTE]
> Usługa DevTest Labs nie nakłada żadnych przydziałów. Wszystkie przydziały, które mogą wystąpić, są domyślnymi ograniczeniami ogólnej subskrypcji platformy Azure.

Każdy zasób platformy Azure można użyć do momentu osiągnięcia limitu przydziału. Każda subskrypcja ma oddzielne przydziały, a użycie jest śledzone dla poszczególnych subskrypcji.

Na przykład Każda subskrypcja ma domyślny przydział 20 rdzeni. Dlatego, jeśli tworzysz maszyny wirtualne w laboratorium z czterema rdzeniami, możesz utworzyć tylko pięć maszyn wirtualnych.

[Limity subskrypcji i usług platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) zawierają listę najbardziej typowych limitów zasobów platformy Azure. Najczęściej używane zasoby w laboratorium, dla których mogą wystąpić limity przydziału, obejmują rdzenie maszyn wirtualnych, publiczne adresy IP, interfejs sieciowy, dyski zarządzane, przypisywanie ról RBAC i obwody usługi ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Wyświetlanie użycia i przydziałów
W tych krokach pokazano, jak wyświetlić bieżące przydziały w ramach subskrypcji dla określonych zasobów platformy Azure, a także zobaczyć procent użycia każdego używanego przydziału.

1. Zaloguj się do [portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz pozycję **więcej usług**, a następnie z listy wybierz pozycję **rozliczenia** .
1. W bloku rozliczenia wybierz subskrypcję.
4. Wybierz pozycję **użycie i przydziały**.

   ![Przycisk użycie i przydziały](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Zostanie wyświetlony blok użycie i przydziały z listą różnych zasobów dostępnych w ramach tej subskrypcji oraz procent przydziału, który jest używany dla każdego zasobu.

   ![Limity przydziału i użycie](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Żądanie większej ilości zasobów w ramach subskrypcji
W przypadku osiągnięcia limitu przydziału domyślny limit zasobu w subskrypcji można zwiększyć do maksymalnego limitu, zgodnie z opisem w temacie [subskrypcja i limity usług platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

W tych krokach pokazano, jak poprosić o zwiększenie limitu przydziału za pomocą [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz pozycję **więcej usług**, wybierz pozycję **rozliczenia**, a następnie wybierz pozycję **użycie + przydziały**.
1. W bloku użycie i limity przydziału wybierz przycisk **Zwiększ żądanie** .

   ![Przycisk zwiększenia żądania](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Aby ukończyć i przesłać żądanie, podaj wymagane informacje na wszystkich trzech kartach w **nowym formularzu żądania pomocy technicznej** .

   ![Formularz zwiększenia żądania](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Zrozumienie ograniczeń i limitów platformy Azure](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) zapewnia więcej informacji na temat kontaktowania się z pomocą techniczną platformy Azure w celu zażądania zwiększenia limitu przydziału.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Następne kroki
* Zapoznaj się z [galerią szablonów DevTest Labs Azure Resource Manager — szybki start](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
