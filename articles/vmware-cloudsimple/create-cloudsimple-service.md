---
title: Tworzenie rozwiązania VMware platformy Azure według CloudSimple — usługa
description: Opisuje sposób tworzenia usługi CloudSimple w Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6986e0a7e6eee6dbbd43c72a415b01df7da7da51
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812445"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>Tworzenie rozwiązania VMware platformy Azure według CloudSimple — usługa

Aby rozpocząć pracę z rozwiązaniem VMware platformy Azure przez CloudSimple, Utwórz rozwiązanie Azure VMware według usługi CloudSimple w Azure Portal.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przydziel blok CIDR/28 dla podsieci bramy.  Podsieć bramy jest wymagana dla usługi CloudSimple i jest unikatowa dla regionu, w którym została utworzona. Podsieć bramy jest używana dla usług sieci brzegowej i wymaga bloku/28 CIDR. Przestrzeń adresowa podsieci bramy musi być unikatowa. Nie może pokrywać się z żadną siecią, która komunikuje się ze środowiskiem CloudSimple.  Sieci, które komunikują się z usługą CloudSimple, obejmują sieci lokalne i sieci wirtualne platformy Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Tworzenie usługi

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **usługi CloudSimple Services**.

    ![Wyszukaj usługę CloudSimple](media/create-cloudsimple-service-search.png)

3. Wybierz pozycję **CloudSimple Services**.

4. Kliknij przycisk **Dodaj** , aby utworzyć nową usługę.

    ![Dodaj usługę CloudSimple](media/create-cloudsimple-service-add.png)

5. Wybierz subskrypcję, w której chcesz utworzyć usługę CloudSimple.

6. Wybierz grupę zasobów dla usługi. Aby dodać nową grupę zasobów, kliknij pozycję **Utwórz nową**.

7. Wprowadź nazwę identyfikującą usługę.

8. Wprowadź wartość CIDR dla bramy usługi. Określ podsieć/28, która nie nakłada się na żadną z istniejących podsieci.  Obejmują one podsieci lokalne, podsieci platformy Azure lub wszystkie planowane podsieci CloudSimple. Po utworzeniu usługi nie można zmienić CIDR.

    ![Tworzenie usługi CloudSimple](media/create-cloudsimple-service.png)

9. Kliknij przycisk **OK**.

Usługa zostanie utworzona i dodana do listy usług.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [utworzyć chmurę prywatną](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Dowiedz się, jak [skonfigurować środowisko chmury prywatnej](quickstart-create-private-cloud.md)
