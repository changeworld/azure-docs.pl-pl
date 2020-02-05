---
title: Przewodnik Szybki Start dotyczący rozwiązań VMware platformy Azure (Automatyczna synchronizacja) — Tworzenie usługi
description: Dowiedz się, jak utworzyć usługę automatycznej synchronizacji, węzły zakupu i rezerwowe węzły
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e7eb414e51ca38f524ab83bfb51f80f771524287
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024436"
---
# <a name="quickstart---create-azure-vmware-solutions-avs-service"></a>Szybki Start — Tworzenie usługi Azure VMware Solutions (Automatyczna synchronizacja)

Aby rozpocząć, Utwórz rozwiązanie Azure VMware (Automatyczna synchronizacja) w Azure Portal.

## <a name="vmware-solutions-avs---service-overview"></a>VMware Solutions (Automatyczna synchronizacja) — Omówienie usługi

Usługa automatycznej synchronizacji umożliwia korzystanie z rozwiązania VMware na platformie Azure przez automatyczna synchronizacja. Tworzenie usługi pozwala na inicjowanie obsługi węzłów, rezerwowanie węzłów oraz tworzenie chmur prywatnych. Usługę automatycznej synchronizacji należy dodać w każdym regionie świadczenia usługi Azure, w którym jest dostępna usługa automatycznej synchronizacji. Usługa definiuje sieć brzegową rozwiązania VMware firmy Azure przez automatyczna synchronizacja. Ta sieć brzegowa jest używana w przypadku usług, które obejmują sieci VPN, ExpressRoute i łączność internetową z chmurami prywatnymi do automatycznej synchronizacji.

Aby dodać usługę automatycznej synchronizacji, należy utworzyć podsieć bramy. Podsieć bramy jest używana podczas tworzenia sieci brzegowej i wymaga bloku/28 CIDR. Przestrzeń adresowa podsieci bramy musi być unikatowa. Nie może pokrywać się z żadnym z lokalnych przestrzeni adresowych sieci ani przestrzenią adresową sieci wirtualnej platformy Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przydziel blok CIDR/28 dla podsieci bramy. Podsieć bramy jest wymagana dla każdej usługi automatycznej synchronizacji i jest unikatowa dla regionu, w którym została utworzona. Podsieć bramy jest używana w rozwiązaniu VMware platformy Azure przez automatyczna synchronizacja usług sieciowych i wymaga bloku CIDR/28. Przestrzeń adresowa podsieci bramy musi być unikatowa. Nie może pokrywać się z żadną siecią, która komunikuje się ze środowiskiem automatycznej synchronizacji. Sieci, które komunikują się z funkcją automatycznej synchronizacji, obejmują sieci lokalne i sieci wirtualne platformy Azure.

Przejrzyj [wymagania wstępne dotyczące sieci](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Tworzenie usługi

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługę automatyczna synchronizacja**.

    ![Wyszukaj usługę automatycznej synchronizacji](media/create-cloudsimple-service-search.png)

3. Wybierz pozycję **usługi automatycznej synchronizacji**.
4. Kliknij przycisk **Dodaj** , aby utworzyć nową usługę.

    ![Dodaj usługę automatycznej synchronizacji](media/create-cloudsimple-service-add.png)

5. Wybierz subskrypcję, w której chcesz utworzyć usługę automatycznej synchronizacji.
6. Wybierz grupę zasobów dla usługi. Aby dodać nową grupę zasobów, kliknij pozycję **Utwórz nową**.
7. Wprowadź nazwę identyfikującą usługę.
8. Wprowadź wartość CIDR dla bramy usługi. Określ podsieć/28, która nie nakłada się na żadną z podsieci lokalnych, podsieci platformy Azure ani w zaplanowanych podsieciach automatycznej synchronizacji. Po utworzeniu usługi nie można zmienić CIDR.

    ![Tworzenie usługi automatycznej synchronizacji](media/create-cloudsimple-service.png)

9. Kliknij przycisk **OK**.

Usługa zostanie utworzona i dodana do listy usług.

## <a name="provision-nodes"></a>Aprowizowanie węzłów

Aby skonfigurować funkcję płatność zgodnie z rzeczywistym użyciem dla środowiska chmury prywatnej automatycznej, należy najpierw udostępnić węzły w Azure Portal.

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

* [Tworzenie automatycznej synchronizacji chmury prywatnej i Konfigurowanie środowiska](quickstart-create-private-cloud.md)
* Dowiedz się więcej o [usłudze automatyczna synchronizacja](https://docs.azure.cloudsimple.com/cloudsimple-service)
