---
title: Skalowanie przydziałów i limitów w laboratorium w laboratoriach devtest platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak można skalować laboratorium w laboratorium usługi Azure DevTest Labs. Wyświetl przydziały użycia i limity oraz poproś o zwiększenie.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: f3299c24bc751263ccd284a4fddb492fba5dd1e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761189"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Skalowanie przydziałów i limitów w laboratoriach DevTest
Podczas pracy w DevTest Labs można zauważyć, że istnieją pewne domyślne limity niektórych zasobów platformy Azure, które mogą mieć wpływ na usługę DevTest Labs. Limity te są określane jako **przydziały**.

> [!NOTE]
> Usługa DevTest Labs nie nakłada żadnych przydziałów. Wszystkie przydziały, które mogą wystąpić są domyślne ograniczenia ogólnej subskrypcji platformy Azure.

Zasób platformy Azure można używać do momentu osiągnięcia jego przydziału. Każda subskrypcja ma oddzielne przydziały, a użycie jest śledzone dla poszczególnych subskrypcji.

Na przykład każda subskrypcja ma domyślny przydział 20 rdzeni. Tak jeśli tworzysz maszyny wirtualne w laboratorium z czterech rdzeni każdy, a następnie można utworzyć tylko pięć maszyn wirtualnych.

[Limity subskrypcji i usług platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) zawierają listę niektórych najpopularniejszych przydziałów zasobów platformy Azure. Zasoby najczęściej używane w laboratorium, dla których mogą wystąpić przydziały, obejmują rdzenie maszyn wirtualnych, publiczne adresy IP, interfejs sieciowy, dyski zarządzane, przypisanie roli RBAC i obwody usługi ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Wyświetlanie użycia i przydziałów
Te kroki pokazują, jak wyświetlić bieżące przydziały w subskrypcji dla określonych zasobów platformy Azure i zobaczyć, jaki procent każdego przydziału, który został użyty.

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **pozycję Więcej usług**, a następnie wybierz pozycję **Rozliczenia** z listy.
1. W bloku Rozliczenia wybierz subskrypcję.
4. Wybierz **opcję Użycie + przydziały**.

   ![Przycisk Użycie i przydziały](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Zostanie wyświetlony blok Usage + quotas, zawierający listę różnych zasobów dostępnych w tej subskrypcji oraz procent przydziału, który jest używany dla każdego zasobu.

   ![Przydziały i użycie](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Żądanie większej ilości zasobów w ramach subskrypcji
Jeśli osiągniesz limit przydziału, domyślny limit zasobu w subskrypcji można zwiększyć do maksymalnego limitu, zgodnie z opisem w [obszarze Subskrypcja i limity usług platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

Te kroki pokazują, jak zażądać zwiększenia przydziału za pośrednictwem [witryny Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **pozycję Więcej usług**, wybierz pozycję **Rozliczenia**, a następnie wybierz pozycję Użycie **+ przydziały**.
1. W bloku Użycie + przydziały wybierz przycisk **Zwiększ żądanie.**

   ![Przycisk Zwiększ żądanie](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Aby wypełnić i przesłać żądanie, wypełnij wymagane informacje na wszystkich trzech kartach formularza **Nowe żądanie pomocy technicznej.**

   ![Formularz zwiększenia żądania](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Opis limitów i podwyżek platformy Azure](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) zawiera więcej informacji na temat kontaktowania się z pomocą techniczną platformy Azure w celu żądania zwiększenia przydziału.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Następne kroki
* Zapoznaj się z [galerią szablonów Szybki start programu Quick Start programu DevTest Labs w usłudze Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
