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
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: cac17403425f53593d4f48692b4216a92c8624e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61481755"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Jak otworzyć porty do maszyny wirtualnej w witrynie Azure portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

1. Wyszukaj i wybierz grupę zasobów dla maszyny Wirtualnej, wybierz **Dodaj**, a następnie wyszukaj i wybierz **sieciowej grupy zabezpieczeń**.

2. Wybierz pozycję **Utwórz**.

    **Tworzenie sieciowej grupy zabezpieczeń** zostanie otwarte okno.

    ![Tworzenie sieciowej grupy zabezpieczeń](./media/nsg-quickstart-portal/create-nsg.png)

2. Wprowadź nazwę dla sieciowej grupy zabezpieczeń. 

3. Wybierz lub Utwórz grupę zasobów, a następnie wybierz lokalizację.

4. Wybierz **Utwórz** utworzyć sieciową grupę zabezpieczeń.

## <a name="create-an-inbound-security-rule"></a>Tworzenie reguły zabezpieczeń dla ruchu przychodzącego

1. Wybierz nową grupę zabezpieczeń sieci. 

2. Wybierz **reguły zabezpieczeń dla ruchu przychodzącego**, a następnie wybierz **Dodaj**.

    ![Dodaj regułę ruchu przychodzącego](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Wybierz pozycję **Zaawansowane**. 

4. Wybierz często **usługi** z menu rozwijanego, takich jak **HTTP**. Możesz również wybrać **niestandardowe** Jeśli chcesz udostępnić określony port do użycia. 

5. Opcjonalnie można zmienić **priorytet** lub **nazwa**. Priorytet wpływa na kolejność stosowania reguł: niższe wartości liczbowej, wcześniej reguła jest stosowana.

6. Wybierz **Dodaj** Aby utworzyć regułę.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Kojarzenie sieciowej grupy zabezpieczeń z podsiecią

Ostatni krok to skojarzenie sieciowej grupy zabezpieczeń z podsiecią lub określonym interfejsem sieciowym. W tym przykładzie firma Microsoft skojarzyć sieciową grupę zabezpieczeń z podsiecią. 

1. Wybierz **podsieci**, a następnie wybierz **skojarzyć**.

    ![Skojarz sieciową grupę zabezpieczeń z podsiecią](./media/nsg-quickstart-portal/associate-subnet.png)

2. Wybierz sieć wirtualną, a następnie wybierz odpowiednią podsieć.

    ![Kojarzenie sieciowej grupy zabezpieczeń z sieciami wirtualnymi](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Wszystkie maszyny wirtualne, z którymi się łączysz się do tej podsieci są teraz dostępne na porcie 80.

## <a name="additional-information"></a>Dodatkowe informacje

Możesz również [przy użyciu programu Azure PowerShell, wykonaj kroki w tym artykule](nsg-quickstart-powershell.md).

Polecenie opisane w tym artykule umożliwiają szybkie rozpoczęcie ruch przepływający do maszyny Wirtualnej. Sieciowe grupy zabezpieczeń zapewniają wiele świetnych funkcji i stopień szczegółowości do kontrolowania dostępu do zasobów. Aby uzyskać więcej informacji, zobacz [filtrowanie ruchu sieciowego z sieciową grupą zabezpieczeń](../../virtual-network/tutorial-filter-network-traffic.md).

Dla aplikacji sieci web o wysokiej dostępności należy rozważyć umieszczenie maszyny wirtualne za modułem równoważenia obciążenia platformy Azure. Moduł równoważenia obciążenia dystrybuuje ruch do maszyn wirtualnych z sieciową grupą zabezpieczeń, która pozwala na filtrowanie ruchu sieciowego. Aby uzyskać więcej informacji, zobacz [Równoważenie obciążenia maszyn wirtualnych Windows na platformie Azure do utworzenia aplikacji o wysokiej dostępności](tutorial-load-balancer.md).

## <a name="next-steps"></a>Kolejne kroki
W tym artykule utworzono sieciową grupę zabezpieczeń, utworzyć regułę ruchu przychodzącego, która zezwala na ruch HTTP na porcie 80, a następnie skojarzone z tą regułą z podsiecią. 

Można znaleźć informacje dotyczące tworzenia środowisk bardziej szczegółowe w następujących artykułach:
- [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
- [Grupy zabezpieczeń](../../virtual-network/security-overview.md)