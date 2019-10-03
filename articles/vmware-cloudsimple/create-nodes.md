---
title: Inicjowanie obsługi węzłów dla rozwiązań VMware przez CloudSimple — Azure
description: Dowiedz się, jak dodać węzły do programu VMWare przy użyciu wdrożenia CloudSimple
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eb033425d18b472c9da1a2d6a1bb6f166702905e
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828179"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>Inicjowanie obsługi administracyjnej węzłów dla rozwiązań VMware platformy Azure według CloudSimple

Inicjowanie obsługi węzłów w Azure Portal. Następnie możesz skonfigurować przepustowość zgodnie z rzeczywistym użyciem dla środowiska chmury prywatnej CloudSimple.

## <a name="sign-in-to-azure"></a>Zaloguj się do platformy Azure

Zaloguj się do Azure Portal w [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Dodawanie węzła do prywatnej chmury CloudSimple

1. Wybierz pozycję **wszystkie usługi**.
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
10. Wybierz [Typ węzła](cloudsimple-node.md).
11. Wybierz liczbę węzłów do aprowizacji.
12. Wybierz pozycję **Recenzja + Utwórz**.
13. Przejrzyj ustawienia. Aby zmodyfikować wszystkie ustawienia, kliknij przycisk **Wstecz**.
14. Wybierz pozycję **Utwórz**.

## <a name="next-steps"></a>Następne kroki

* [Utwórz chmurę prywatną](create-private-cloud.md)
