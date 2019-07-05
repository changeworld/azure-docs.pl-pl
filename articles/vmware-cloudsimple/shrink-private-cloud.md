---
title: Zmniejszanie rozwiązanie VMware na platformie Azure, Chmura prywatna CloudSimple
description: W tym artykule opisano, jak zmniejszyć chmury prywatnej CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e639feb603f1654b4dcd40f16d8e3094307839a
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544519"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Zmniejszanie CloudSimple chmury prywatnej

CloudSimple zapewnia swobodę dynamicznie zmniejszać chmury prywatnej.  Chmura prywatna składa się z co najmniej jeden klaster vSphere. Każdy klaster może mieć węzłów 3-16. Podczas zmniejszania chmury prywatnej, możesz usunąć węzeł z istniejącego klastra, lub usunąć cały klaster. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do zmniejszania chmury prywatnej muszą być spełnione następujące warunki.  Zarządzanie klastra (pierwszy klaster) utworzony podczas tworzenia chmury prywatnej nie można jej usunąć.

* Klastrze vSphere musi mieć trzy węzły.  Nie można zmniejszyć klastra z tylko trzy węzły.
* Całkowitą ilość używanego miejsca nie powinna przekraczać łączna pojemność po zmniejszeniu klastra. 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Zmniejszanie chmury prywatnej 

1. [Dostęp do portalu CloudSimple](access-cloudsimple-portal.md).

2. Otwórz **zasobów** strony.

3. Kliknij chmurę prywatną, aby zmniejszyć

4. Na stronie podsumowania kliknij **zmniejszania**.

    ![Zmniejszanie chmury prywatnej](media/shrink-private-cloud.png)

5. Wybierz klaster, który chcesz zmniejszyć lub usunąć. 

    ![Zmniejszanie chmury prywatnej — wybierz klastra](media/shrink-private-cloud-select-cluster.png)

6. Wybierz **usunąć jeden węzeł** lub **Usuń cały klaster**. 

7. Sprawdź pojemność klastra

8. Kliknij przycisk **przesyłania** zmniejszyć chmury prywatnej.

Rozpoczyna się zmniejszania chmury prywatnej.  Można monitorować postęp zadania.  Proces zmniejszania może potrwać kilka godzin w zależności od danych, która musi być resynced w sieci vSAN.

## <a name="next-steps"></a>Kolejne kroki

* [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się więcej o [Chmurami prywatnymi](cloudsimple-private-cloud.md)