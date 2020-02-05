---
title: Inicjowanie obsługi węzłów dla rozwiązań VMware (Automatyczna synchronizacja) — Azure
description: Dowiedz się, jak dodać węzły do programu VMWare z wdrożeniem automatycznej synchronizacji
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 94ca681153f1e3ccd9ff628b41a9d0e547be5802
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024810"
---
# <a name="provision-nodes-for-azure-vmware-solutions-avs"></a>Inicjowanie obsługi węzłów dla rozwiązań VMware platformy Azure (Automatyczna synchronizacja)

Inicjowanie obsługi węzłów w Azure Portal. Następnie możesz skonfigurować przepustowość zgodnie z rzeczywistym użyciem w środowisku chmury prywatnej.

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-avs-private-cloud"></a>Dodawanie węzła do chmury prywatnej do automatycznej synchronizacji

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **węzły automatycznej synchronizacji**.

   ![Wyszukaj węzły automatycznej synchronizacji](media/create-cloudsimple-node-search.png)

3. Wybierz **węzły automatyczna synchronizacja**.
4. Kliknij przycisk **Dodaj** , aby utworzyć węzły.

    ![Dodaj węzły automatycznej synchronizacji](media/create-cloudsimple-node-add.png)

5. Wybierz subskrypcję, w której chcesz udostępnić węzły automatycznej synchronizacji.
6. Wybierz grupę zasobów dla węzłów. Aby dodać nową grupę zasobów, kliknij pozycję **Utwórz nową**.
7. Wprowadź prefiks, aby zidentyfikować węzły.
8. Wybierz lokalizację dla zasobów węzła.
9. Wybierz dedykowaną lokalizację do hostowania zasobów węzła.
10. Wybierz [Typ węzła](cloudsimple-node.md).
11. Wybierz liczbę węzłów do aprowizacji.
12. Wybierz pozycję **Przegląd + utwórz**.
13. Przejrzyj ustawienia. Aby zmodyfikować wszystkie ustawienia, kliknij przycisk **Wstecz**.
14. Wybierz pozycję **Utwórz**.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie automatycznej synchronizacji w chmurze prywatnej](create-private-cloud.md)
