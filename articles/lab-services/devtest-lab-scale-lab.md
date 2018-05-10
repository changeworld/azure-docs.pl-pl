---
title: Skalowanie przydziały i limity w laboratorium w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skalować laboratorium w usłudze Azure DevTest Labs
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
ms.openlocfilehash: 5a179c0c6b777ee6b2afdd0f2e9267d247665d8d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Skalowanie przydziały i limity w usłudze DevTest Labs
Podczas pracy w usłudze DevTest Labs, można zauważyć, że istnieją pewne domyślne limity do niektórych zasobów platformy Azure, co może wpłynąć na usługę DevTest Labs. Ograniczenia te są określane jako **przydziały**.

> [!NOTE]
> Usługa DevTest Labs nie nakładają żadnych przydziałów. Wszelkie przydziałów, które mogą wystąpić są domyślne ograniczenia ogólne Azure subskrypcji.

Można użyć każdego zasobów platformy Azure, aż dojdziesz limit przydziału. Każda subskrypcja ma oddzielne przydziały i użycia są śledzone na subskrypcję.

Na przykład każda subskrypcja ma domyślnego przydziału rdzeni 20. Dlatego w przypadku tworzenia maszyn wirtualnych w laboratorium z czterech rdzeni, następnie można tworzyć tylko pięć maszyn wirtualnych. 

[Azure subskrypcji i ograniczenia usługi](https://docs.microsoft.com/azure/azure-subscription-service-limits) przedstawiono niektóre z najczęściej przydziały dla zasobów platformy Azure. Zasoby najczęściej używane w laboratorium, a dla mogą wystąpić przydziały, zawierają rdzeni maszyny Wirtualnej, publiczne adresy IP interfejsu sieciowego, dysków zarządzanych, przypisanie roli RBAC i obwody usługi ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Wyświetlanie Twoich użycia i przydziały
Te kroki pokazują, jak wyświetlić bieżący przydziały w subskrypcji dla określonych zasobów platformy Azure i zobacz, jaki procent każdego przydziału zostały użyte.

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **więcej usług**, a następnie wybierz **rozliczeń** z listy.
1. W bloku rozliczenia Wybierz subskrypcję.
4. Wybierz **użycia + przydziały**.

   ![Przycisk użycia i przydziały](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Użycie + przydziały zostanie wyświetlony blok, listę różnych dostępnych zasobów w subskrypcji i procent przydziału, który jest używany dla zasobów.

   ![Przydziały i użycia](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Żąda więcej zasobów w Twojej subskrypcji
Przekroczenie limitu przydziału domyślny limit zasobów w subskrypcji można zwiększyć maksymalnego limitu, zgodnie z opisem w [subskrypcji platformy Azure i ograniczenia usługi](https://docs.microsoft.com/azure/azure-subscription-service-limits).

Tych krokach przedstawiono sposób zażądać zwiększenia limitu przydziału przez [portalu Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **więcej usług**, wybierz pozycję **rozliczeń**, a następnie wybierz **użycia + przydziały**.
1. Użycie + przydziały bloku, wybierz opcję **żądanie zwiększenia** przycisku.

   ![Przycisk Zwiększ żądania](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Aby zakończyć i przesłać żądanie, wypełnij wymagane informacje na wszystkich trzech kartach **nowy obsługuje żądania** formularza.

   ![Zwiększ formularz żądania](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Opis ograniczeń Azure i zwiększa](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) zawiera więcej informacji o skontaktowaniu się z pomocą techniczną platformy Azure, aby zażądać zwiększenia limitu przydziału.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Kolejne kroki
* Eksploruj [galerię szablonów DevTest Labs Azure Resource Manager — Szybki Start](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
