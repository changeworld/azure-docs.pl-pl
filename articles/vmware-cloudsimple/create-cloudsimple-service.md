---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Tworzenie usługi automatycznej synchronizacji
description: Zawiera opis sposobu tworzenia usługi automatycznej synchronizacji w Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 468ef8751438812422d7eee83d98acc9e3aedb82
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024827"
---
# <a name="create-the-azure-vmware-solutions-avs-service"></a>Tworzenie usługi Azure VMware Solutions (Automatyczna synchronizacja)

Aby rozpocząć pracę z rozwiązaniami VMware platformy Azure (Automatyczna synchronizacja), Utwórz usługę Azure VMware Solutions (Automatyczna synchronizacja) w Azure Portal.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przydziel blok CIDR/28 dla podsieci bramy. Podsieć bramy jest wymagana dla każdej usługi automatycznej synchronizacji i jest unikatowa dla regionu, w którym została utworzona. Podsieć bramy jest używana dla usług sieci brzegowej i wymaga bloku/28 CIDR. Przestrzeń adresowa podsieci bramy musi być unikatowa. Nie może pokrywać się z żadną siecią, która komunikuje się ze środowiskiem automatycznej synchronizacji. Sieci, które komunikują się z funkcją automatycznej synchronizacji, obejmują sieci lokalne i sieci wirtualne platformy Azure.

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do [Portalu Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Tworzenie usługi

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługi automatycznej synchronizacji**.
    Usługa ![przeszukiwania automatyczna synchronizacja](media/create-cloudsimple-service-search.png)
3. Wybierz pozycję **usługi automatycznej synchronizacji**.
4. Kliknij przycisk **Dodaj** , aby utworzyć nową usługę.
    ![dodać usługi automatycznej synchronizacji](media/create-cloudsimple-service-add.png)
5. Wybierz subskrypcję, w której chcesz utworzyć usługę automatycznej synchronizacji.
6. Wybierz grupę zasobów dla usługi. Aby dodać nową grupę zasobów, kliknij pozycję **Utwórz nową**.
7. Wprowadź nazwę identyfikującą usługę.
8. Wprowadź wartość CIDR dla bramy usługi. Określ podsieć/28, która nie nakłada się na żadną z podsieci lokalnych, podsieci platformy Azure ani w zaplanowanych podsieciach automatycznej synchronizacji. Po utworzeniu usługi nie można zmienić CIDR.

    ![Tworzenie usługi automatycznej synchronizacji](media/create-cloudsimple-service.png)
9. Kliknij przycisk **OK**.

Usługa zostanie utworzona i dodana do listy usług.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [inicjować obsługę węzłów](create-nodes.md)
* Dowiedz się [, jak utworzyć chmurę prywatną do automatycznej synchronizacji](create-private-cloud.md)
* Dowiedz się, jak [skonfigurować środowisko chmury prywatnej do automatycznej synchronizacji](quickstart-create-private-cloud.md)
