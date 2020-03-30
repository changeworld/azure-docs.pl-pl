---
title: Rozwiązanie Azure VMware by CloudSimple — tworzenie usługi CloudSimple
description: W tym artykule opisano sposób tworzenia usługi CloudSimple w witrynie Azure portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8648f2c9cc0175050d4b7642f5235d47159ecfaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024827"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Tworzenie usługi Azure VMware Solution by CloudSimple

Aby rozpocząć korzystanie z rozwiązania Azure VMware przez CloudSimple, utwórz usługę Azure VMware Solution by CloudSimple w witrynie Azure portal.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przydziel blok CIDR /28 dla podsieci bramy. Podsieć bramy jest wymagana dla usługi CloudSimple i jest unikatowa dla regionu, w którym została utworzona. Podsieć bramy jest używana dla usług sieci brzegowych i wymaga bloku CIDR /28. Przestrzeń adresowa podsieci bramy musi być unikatowa. Nie może pokrywać się z żadną siecią, która komunikuje się ze środowiskiem CloudSimple. Sieci, które komunikują się z CloudSimple obejmują sieci lokalne i sieci wirtualne platformy Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [Portalu Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Tworzenie usługi

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługi CloudSimple**.
    ![Usługa Search CloudSimple](media/create-cloudsimple-service-search.png)
3. Wybierz **pozycję CloudSimple Services**.
4. Kliknij **przycisk Dodaj,** aby utworzyć nową usługę.
    ![Dodaj usługę CloudSimple](media/create-cloudsimple-service-add.png)
5. Wybierz subskrypcję, w której chcesz utworzyć usługę CloudSimple.
6. Wybierz grupę zasobów dla usługi. Aby dodać nową grupę zasobów, kliknij przycisk **Utwórz nowy**.
7. Wprowadź nazwę, aby zidentyfikować usługę.
8. Wprowadź cidr bramy usługi. Określ podsieć /28, która nie pokrywa się z żadnymi podsieciami lokalnymi, podsieciami platformy Azure lub planowanymi podsieciami CloudSimple. Nie można zmienić CIDR po utworzeniu usługi.

    ![Tworzenie usługi CloudSimple](media/create-cloudsimple-service.png)
9. Kliknij przycisk **OK**.

Usługa jest tworzona i dodawana do listy usług.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [aprowizować węzły](create-nodes.md)
* Dowiedz się, jak [utworzyć chmurę prywatną](create-private-cloud.md)
* Dowiedz się, jak [skonfigurować środowisko chmury prywatnej](quickstart-create-private-cloud.md)
