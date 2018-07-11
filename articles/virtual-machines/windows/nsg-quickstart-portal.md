---
title: Otwieranie portów dla maszyny Wirtualnej przy użyciu witryny Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak otworzyć port / utworzyć punkt końcowy do maszyny Wirtualnej Windows przy użyciu modelu wdrażania usługi resource manager w witrynie Azure Portal
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 2820dcabf042d7463f9776b42f277a0457caf3b6
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929019"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Jak otworzyć porty do maszyny wirtualnej w witrynie Azure portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Szybkie polecenia
Możesz również [wykonać te kroki przy użyciu programu Azure PowerShell](nsg-quickstart-powershell.md).

Najpierw utwórz sieciową grupę zabezpieczeń. Wybierz grupę zasobów w portalu, wybierz pozycję **Dodaj**, a następnie wyszukaj i wybierz **sieciowej grupy zabezpieczeń**:

![Dodaj sieciową grupę zabezpieczeń](./media/nsg-quickstart-portal/add-nsg.png)

Wprowadź nazwę dla sieciowej grupy zabezpieczeń, wybierz lub Utwórz grupę zasobów, a następnie wybierz lokalizację. Wybierz **Utwórz** po zakończeniu:

![Utwórz sieciową grupę zabezpieczeń](./media/nsg-quickstart-portal/create-nsg.png)

Wybierz nową grupę zabezpieczeń sieci. Wybierz pozycję "Reguły zabezpieczeń dla ruchu przychodzącego", a następnie wybierz **Dodaj** przycisk, aby utworzyć regułę:

![Dodaj regułę ruchu przychodzącego](./media/nsg-quickstart-portal/add-inbound-rule.png)

Aby utworzyć regułę zezwalającą na ruch:

- Wybierz **podstawowe** przycisku. Domyślnie **zaawansowane** okno zawiera kilka dodatkowych opcji konfiguracji, takich jak do definiowania zakresu bloku lub portu IP określonego źródła.
- Wybierz często **usługi** z menu rozwijanego, takich jak *HTTP*. Możesz również wybrać *niestandardowe* zapewnienie określonego portu do użycia. 
- Jeśli to konieczne, należy zmienić priorytet lub nazwy. Priorytet wpływa na kolejność, w której reguły są stosowane - niższa wartość liczbową, wcześniej reguła jest stosowana.
- Gdy wszystko będzie gotowe, wybierz pozycję **OK** do utworzenia reguły:

![Utwórz regułę ruchu przychodzącego](./media/nsg-quickstart-portal/create-inbound-rule.png)

Ostatnim krokiem jest, aby skojarzyć sieciową grupę zabezpieczeń z podsiecią lub określonym interfejsem sieciowym. Teraz można skojarzyć sieciową grupę zabezpieczeń z podsiecią. Wybierz **podsieci**, następnie wybierz **skojarzyć**:

![Skojarz sieciową grupę zabezpieczeń z podsiecią](./media/nsg-quickstart-portal/associate-subnet.png)

Wybierz sieć wirtualną, a następnie wybierz odpowiednią podsieć:

![Kojarzenie sieciowej grupy zabezpieczeń z sieciami wirtualnymi](./media/nsg-quickstart-portal/select-vnet-subnet.png)

Utworzono grupę zabezpieczeń sieci, utworzyć regułę ruchu przychodzącego, która zezwala na ruch na porcie 80 i skojarzono ją z podsiecią. Wszystkie maszyny wirtualne, z którymi się łączysz się do tej podsieci są dostępne na porcie 80.

## <a name="more-information-on-network-security-groups"></a>Więcej informacji na temat sieciowych grup zabezpieczeń
Szybkie polecenia w tym miejscu umożliwiają rozpoczęcie pracy z ruchem przepływać do maszyny Wirtualnej. Sieciowe grupy zabezpieczeń zapewniają wiele świetnych funkcji i stopień szczegółowości do kontrolowania dostępu do zasobów. Przeczytaj więcej o [tworzenie sieciowej grupy zabezpieczeń i listy kontroli dostępu reguły tutaj](../../virtual-network/tutorial-filter-network-traffic.md).

W przypadku aplikacji sieci web o wysokiej dostępności należy umieszczać maszyny wirtualne za modułem równoważenia obciążenia platformy Azure. Moduł równoważenia obciążenia dystrybuuje ruch do maszyn wirtualnych z sieciową grupą zabezpieczeń, która pozwala na filtrowanie ruchu sieciowego. Aby uzyskać więcej informacji, zobacz [sposób ładowania równoważenia maszyn wirtualnych systemu Linux na platformie Azure do utworzenia aplikacji o wysokiej dostępności](tutorial-load-balancer.md).

## <a name="next-steps"></a>Kolejne kroki
W tym przykładzie utworzono prosta Reguła zezwalająca na ruch HTTP. Można znaleźć informacje dotyczące tworzenia środowisk bardziej szczegółowe w następujących artykułach:

* [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Co to jest sieciowa grupa zabezpieczeń?](../../virtual-network/security-overview.md)