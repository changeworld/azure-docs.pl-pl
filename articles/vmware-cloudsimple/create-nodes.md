---
title: Węzły inicjowania obsługi administracyjnej rozwiązania VMware według usługi CloudSimple — Azure
description: Dowiedz się, jak dodać węzły do urządzenia VMWare za pomocą wdrożenia CloudSimple
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eb033425d18b472c9da1a2d6a1bb6f166702905e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024810"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>Inicjowanie obsługi administracyjnej węzłów rozwiązania Azure VMware przez CloudSimple

Inicjuj węzeły w witrynie Azure portal. Następnie można skonfigurować pojemność płatności zgodnie z rzeczywistyma pozycją dla środowiska chmury prywatnej CloudSimple.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Dodawanie węzła do chmury prywatnej CloudSimple

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **węzły CloudSimple**.

   ![Szukaj węzłów CloudSimple](media/create-cloudsimple-node-search.png)

3. Wybierz **opcję Węzły CloudSimple**.
4. Kliknij **przycisk Dodaj,** aby utworzyć węzły.

    ![Dodawanie węzłów CloudSimple](media/create-cloudsimple-node-add.png)

5. Wybierz subskrypcję, w której chcesz aprowizować węzły CloudSimple.
6. Wybierz grupę zasobów dla węzłów. Aby dodać nową grupę zasobów, kliknij przycisk **Utwórz nowy**.
7. Wprowadź prefiks, aby zidentyfikować węzły.
8. Wybierz lokalizację dla zasobów węzła.
9. Wybierz dedykowaną lokalizację, aby obsługiwać zasoby węzła.
10. Wybierz [typ węzła](cloudsimple-node.md).
11. Wybierz liczbę węzłów do zainicjowania.
12. Wybierz **pozycję Recenzja + Utwórz**.
13. Przejrzyj ustawienia. Aby zmodyfikować dowolne ustawienia, kliknij przycisk **Poprzedni**.
14. Wybierz **pozycję Utwórz**.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie chmury prywatnej](create-private-cloud.md)
