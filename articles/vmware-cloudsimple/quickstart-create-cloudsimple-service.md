---
title: Rozwiązanie programu VMware na platformie Azure przez CloudSimple Przewodnik Szybki Start — Tworzenie usługi
description: Dowiedz się, jak utworzyć usługę CloudSimple aprowizacji węzłów i rezerwa węzły
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5732ea726bdecc10d0757224870ee5d8be83a2b2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164221"
---
# <a name="quickstart---create-service"></a>Przewodnik Szybki Start — Tworzenie usługi

Aby rozpocząć pracę, należy utworzyć rozwiązanie VMware Azure przez CloudSimple w witrynie Azure portal.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware Solution by CloudSimple — Omówienie usługi

Usługa CloudSimple umożliwia korzystanie z platformy Azure VMware Solution by CloudSimple.  Tworzenie usługi umożliwia węzłów należy zarezerwować węzłów i tworzenie chmur prywatnych.  Możesz dodać usługę CloudSimple w każdym regionie platformy Azure, w których usługa CloudSimple jest dostępna.  Usługa zawiera definicję sieci krawędź Azure VMware Solution by CloudSimple.  Ta sieć usługi edge jest używana dla usług, które obejmują sieć VPN, usługi ExpressRoute i łączność z Internetem do chmur prywatnych.

Aby dodać usługę CloudSimple, należy utworzyć podsieć bramy. Podsieć bramy jest używany podczas tworzenia sieci krawędź i wymaga o rozmiarze/28 blok CIDR. Przestrzeń adresową podsieci bramy musi być unikatowa. Nie może nakładać się ze wszystkimi przestrzeniami adresów sieci lokalnej lub przestrzeni adresowej sieci wirtualnej platformy Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Włącz Microsoft.VMwareCloudSimple dostawcy zasobów

Wykonaj poniższe kroki, aby umożliwić dostawcy zasobów usługi CloudSimple.

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj i wybierz pozycję **subskrypcje**.

    ![Wybierz subskrypcje](media/cloudsimple-service-select-subscriptions.png)

3. Wybierz subskrypcję, dla którego chcesz włączyć usługę CloudSimple
4. Kliknij pozycję **dostawców zasobów** dla subskrypcji
5. Użyj **Microsoft.VMwareCloudSimple** do filtrowania dostawcy zasobów
6. Wybierz **Microsoft.VMwareCloudSimple** dostawcy zasobów i kliknięcie **zarejestrować**

    ![Rejestrowanie dostawcy zasobów](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>Tworzenie usługi

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługi CloudSimple**.

    ![CloudSimple usługi wyszukiwania](media/create-cloudsimple-service-search.png)

3. Wybierz **usług CloudSimple**.
4. Kliknij przycisk **Dodaj** do utworzenia nowej usługi.

    ![Dodaj usługę CloudSimple](media/create-cloudsimple-service-add.png)

5. Wybierz subskrypcję, której chcesz utworzyć usługę CloudSimple.
6. Wybierz grupę zasobów dla usługi. Aby dodać nową grupę zasobów, kliknij **Utwórz nowy**.
7. Wprowadź nazwę do identyfikowania usługi.
8. Wprowadź CIDR usługi bramy. Określ o rozmiarze/28 podsieci, która nie pokrywa się z dowolnej podsieci lokalnej, podsieci platformy Azure lub planowanych CloudSimple podsieci. Nie można zmienić CIDR, po utworzeniu usługi.

    ![Tworzenie usługi CloudSimple](media/create-cloudsimple-service.png)

9. Kliknij przycisk **OK**.

Usługa zostanie utworzony i dodany do listy usług.

## <a name="provision-nodes"></a>Aprowizacja węzłów

Aby skonfigurować płatność — jako — możesz Przejdź pojemności w środowisku chmury prywatnej CloudSimple, należy najpierw aprowizować węzły w witrynie Azure portal.

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **węzłów CloudSimple**.

    ![Węzły CloudSimple wyszukiwania](media/create-cloudsimple-node-search.png)

3. Wybierz **węzłów CloudSimple**.
4. Kliknij przycisk **Dodaj** utworzyć węzłów.

    ![Dodaj węzły CloudSimple](media/create-cloudsimple-node-add.png)

5. Wybierz subskrypcję, której chcesz aprowizować węzły CloudSimple.
6. Wybierz grupę zasobów dla węzłów. Aby dodać nową grupę zasobów, kliknij **Utwórz nowy**.
7. Wprowadź prefiks do identyfikowania węzłów.
8. Wybierz lokalizację dla zasobów węzła.
9. Wybierz lokalizację dedykowanych do hostowania zasobów węzła.
10. Wybierz typ węzła. Możesz wybrać [opcji CS28 lub CS36](cloudsimple-node.md). Tę druga opcję zawiera maksymalną moc obliczeniową i pamięć.
11. Wybierz liczbę węzłów do aprowizacji.
12. Wybierz pozycję **Przegląd + utwórz**.
13. Przejrzyj ustawienia. Aby zmodyfikować dowolne ustawienia, kliknij **Wstecz**.
14. Wybierz pozycję **Utwórz**.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie chmury prywatnej i konfigurowanie środowiska](quickstart-create-private-cloud.md)
* Dowiedz się więcej o [CloudSimple usługi](https://docs.azure.cloudsimple.com/cloudsimple-service)
