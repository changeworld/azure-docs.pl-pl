---
title: Inicjowanie obsługi węzłów dla rozwiązań VMware przez CloudSimple — Azure
description: Dowiedz się, jak dodać węzły do programu VMWare przy użyciu wdrożenia CloudSimple
author: dikamath
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9eb02f04b5873e5906782a27ce833a724ceecfe3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812366"
---
# <a name="provision-nodes-for-vmware-solution-by-cloudsimple---azure"></a>Inicjowanie obsługi węzłów dla rozwiązań VMware przez CloudSimple — Azure

Inicjowanie obsługi węzłów w Azure Portal. Następnie możesz skonfigurować przepustowość zgodnie z rzeczywistym użyciem dla środowiska chmury prywatnej CloudSimple.

## <a name="add-a-provisioned-node-to-your-cloudsimple-private-cloud"></a>Dodaj węzeł zainicjowany do prywatnej chmury CloudSimple

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **węzły CloudSimple**.

   ![Wyszukaj węzły CloudSimple](media/create-cloudsimple-node-search.png)

3. Wybierz **węzły CloudSimple**.
4. Kliknij przycisk **Dodaj** , aby utworzyć węzły.

    ![Dodaj węzły CloudSimple](media/create-cloudsimple-node-add.png)

5. Wybierz subskrypcję, w której chcesz udostępnić węzły CloudSimple.
6. Wybierz grupę zasobów dla węzłów. Aby dodać nową grupę zasobów, kliknij pozycję **Utwórz nową**.
7. Wprowadź prefiks, aby zidentyfikować węzły.
8. Wybierz lokalizację dla zasobów węzła.
9. Wybierz dedykowaną lokalizację do hostowania zasobów węzła.
10. Wybierz typ węzła. Można wybrać [opcję CS28 lub CS36](cloudsimple-node.md). Ta ostatnia opcja obejmuje maksymalną moc obliczeniową i pojemność pamięci.
11. Wybierz liczbę węzłów do aprowizacji.
12. Wybierz pozycję **Przegląd + utwórz**.
13. Przejrzyj ustawienia. Aby zmodyfikować wszystkie ustawienia, kliknij przycisk **Wstecz**.
14. Wybierz pozycję **Utwórz**.

## <a name="next-steps"></a>Następne kroki

* [Utwórz chmurę prywatną](https://docs.azure.cloudsimple.com/create-private-cloud/)
