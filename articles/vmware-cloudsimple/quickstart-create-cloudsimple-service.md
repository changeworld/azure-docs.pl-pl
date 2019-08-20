---
title: Rozwiązanie VMware firmy Azure według CloudSimple — szybki start — Tworzenie usługi
description: Dowiedz się, jak utworzyć usługę CloudSimple, węzły zakupu i rezerwowe węzły
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b20ff261939dd97a74d27f5ec7f21eae2665f474
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574552"
---
# <a name="quickstart---create-cloudsimple-service"></a>Szybki Start — Tworzenie usługi CloudSimple

Aby rozpocząć, Utwórz rozwiązanie Azure VMware przez CloudSimple w Azure Portal.

Usługa CloudSimple umożliwia korzystanie z rozwiązań VMware platformy Azure przez CloudSimple.  Tworzenie usługi umożliwia kupowanie węzłów, rezerwowanie węzłów i tworzenie chmur prywatnych.  Należy dodać usługę CloudSimple w każdym regionie świadczenia usługi Azure, w którym jest dostępna usługa CloudSimple.  Usługa definiuje sieć brzegową rozwiązania Azure VMware przez CloudSimple.  Ta sieć brzegowa jest używana w przypadku usług, które obejmują sieci VPN, ExpressRoute i łączność z Internetem z chmurami prywatnymi.

Aby dodać usługę CloudSimple, należy utworzyć podsieć bramy. Podsieć bramy jest używana podczas tworzenia sieci brzegowej i wymaga bloku/28 CIDR. Przestrzeń adresowa podsieci bramy musi być unikatowa. Nie może pokrywać się z żadnym z lokalnych przestrzeni adresowych sieci ani przestrzenią adresową sieci wirtualnej platformy Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Włącz dostawcę zasobów Microsoft. VMwareCloudSimple

Wykonaj poniższe kroki, aby włączyć dostawcę zasobów dla usługi CloudSimple.

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj i wybierz pozycję **subskrypcje**.

    ![Wybierz subskrypcje](media/cloudsimple-service-select-subscriptions.png)

3. Wybierz subskrypcję, w której chcesz włączyć usługę CloudSimple.
4. Kliknij pozycję **dostawcy zasobów** dla subskrypcji.
5. Użyj elementu **Microsoft. VMwareCloudSimple** do filtrowania dostawcy zasobów.
6. Wybierz dostawcę zasobów **Microsoft. VMwareCloudSimple** , a następnie kliknij pozycję **zarejestruj**.

    ![Zarejestruj dostawcę zasobów](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>Tworzenie usługi

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługę CloudSimple**.

    ![Wyszukaj usługę CloudSimple](media/create-cloudsimple-service-search.png)

3. Wybierz pozycję **CloudSimple Services**.
4. Kliknij przycisk **Dodaj** , aby utworzyć nową usługę.

    ![Dodaj usługę CloudSimple](media/create-cloudsimple-service-add.png)

5. Wybierz subskrypcję, w której chcesz utworzyć usługę CloudSimple.
6. Wybierz grupę zasobów dla usługi. Aby dodać nową grupę zasobów, kliknij pozycję **Utwórz nową**.
7. Wprowadź nazwę identyfikującą usługę.
8. Wprowadź wartość CIDR dla bramy usługi. Określ podsieć/28, która nie nakłada się na żadną z podsieci lokalnych, podsieci platformy Azure lub podsieci planowanych CloudSimple. Po utworzeniu usługi nie można zmienić CIDR.

    ![Tworzenie usługi CloudSimple](media/create-cloudsimple-service.png)

9. Kliknij przycisk **OK**.

Usługa zostanie utworzona i dodana do listy usług.

## <a name="purchase-nodes"></a>Kupowanie węzłów

Aby skonfigurować funkcję płatność zgodnie z rzeczywistym użyciem dla środowiska chmury prywatnej CloudSimple, należy najpierw udostępnić węzły w Azure Portal.

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **węzły CloudSimple**.

    ![Wyszukaj węzły CloudSimple](media/create-cloudsimple-node-search.png)

3. Wybierz **węzły CloudSimple**.
4. Kliknij przycisk **Dodaj** , aby utworzyć węzły.

    ![Dodaj węzły CloudSimple](media/create-cloudsimple-node-add.png)

5. Wybierz subskrypcję, w której chcesz kupić węzły CloudSimple.
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

* [Utwórz chmurę prywatną i skonfiguruj środowisko](quickstart-create-private-cloud.md)
* Dowiedz się więcej o [usłudze CloudSimple](cloudsimple-service.md)
