---
title: Zmniejszanie rozwiązania VMware platformy Azure przez chmurę prywatną CloudSimple
description: W tym artykule opisano, jak zmniejszyć CloudSimple private cloud.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 602dca105e91c55c591388a833a36e71f951da8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014270"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Zmniejszanie chmury chmura prywatna

CloudSimple zapewnia elastyczność dynamicznego zmniejszania chmury prywatnej.  Chmura prywatna składa się z jednego lub więcej klastrów vSphere. Każdy klaster może mieć od 3 do 16 węzłów. Podczas zmniejszania chmury prywatnej należy usunąć węzeł z istniejącego klastra lub usunąć cały klaster. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby zmniejszyć ilość chmury prywatnej, muszą być spełnione następujące warunki.  Klastra zarządzania (pierwszy klaster) utworzonego podczas tworzenia chmury prywatnej nie można usunąć.

* Klaster vSphere musi mieć trzy węzły.  Klastra z tylko trzy węzły nie można skurczyć.
* Całkowita ilość zużytego magazynu nie powinna przekraczać całkowitej pojemności po zmniejszonej liczbie klastrów.
* Sprawdź, czy wszystkie reguły harmonogramu zasobów rozproszonych (DRS) zapobiega vMotion maszyny wirtualnej.  Jeśli reguły są obecne, wyłącz lub usuń reguły.  Reguły DRS obejmują maszynę wirtualną do obsługi reguł koligacji.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Zmniejszanie chmury prywatnej

1. [Uzyskaj dostęp do portalu CloudSimple](access-cloudsimple-portal.md).

2. Otwórz stronę **Zasoby.**

3. Kliknij chmurę prywatną, którą chcesz zmniejszyć

4. Na stronie podsumowania kliknij pozycję **Zmniejsz**.

    ![Zmniejszanie chmury prywatnej](media/shrink-private-cloud.png)

5. Zaznacz klaster, który chcesz zmniejszyć lub usunąć. 

    ![Zmniejszanie chmury prywatnej — wybierz klaster](media/shrink-private-cloud-select-cluster.png)

6. Wybierz **pozycję Usuń jeden węzeł** lub Usuń cały **klaster**. 

7. Sprawdzanie pojemności klastra

8. Kliknij **przycisk Prześlij,** aby zmniejszyć chmurę prywatną.

Zmniejszanie chmury prywatnej rozpoczyna.  Można monitorować postęp w zadaniach.  Proces zmniejszania może potrwać kilka godzin w zależności od danych, które muszą zostać ponownie zsynchronowane w u vSAN.

> [!NOTE]
> 1. Jeśli zmniejszasz chmurę prywatną, usuwając ostatni lub jedyny klaster w centrum danych, centrum danych nie zostanie usunięte.
> 2. Jeśli wystąpi naruszenie reguły DRS, węzeł nie zostanie usunięty z klastra, a opis zadania pokazuje, że usunięcie węzła spowoduje naruszenie reguł DRS w klastrze.    


## <a name="next-steps"></a>Następne kroki

* [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się więcej o [chmurach prywatnych](cloudsimple-private-cloud.md)
