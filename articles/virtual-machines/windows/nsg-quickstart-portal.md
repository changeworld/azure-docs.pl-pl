---
title: Otwórz porty na maszynie wirtualnej przy użyciu Azure Portal | Microsoft Docs
description: Dowiedz się, jak otworzyć port/utworzyć punkt końcowy na maszynie wirtualnej z systemem Windows przy użyciu modelu wdrażania usługi Resource Manager w witrynie Azure Portal
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: f9edee7a0ff19a536d0ea719ede6d0cd2e9d6ac7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102629"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Jak otworzyć porty na maszynie wirtualnej przy użyciu Azure Portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

1. Wyszukaj i wybierz grupę zasobów dla maszyny wirtualnej, wybierz pozycję **Dodaj**, a następnie wyszukaj i wybierz pozycję **sieciowa Grupa zabezpieczeń**.

2. Wybierz pozycję **Utwórz**.

    Zostanie otwarte okno **Tworzenie sieciowej grupy zabezpieczeń** .

    ![Tworzenie sieciowej grupy zabezpieczeń](./media/nsg-quickstart-portal/create-nsg.png)

2. Wprowadź nazwę sieciowej grupy zabezpieczeń. 

3. Wybierz lub Utwórz grupę zasobów, a następnie wybierz lokalizację.

4. Wybierz pozycję **Utwórz** , aby utworzyć grupę zabezpieczeń sieci.

## <a name="create-an-inbound-security-rule"></a>Tworzenie reguły zabezpieczeń dla ruchu przychodzącego

1. Wybierz nową sieciową grupę zabezpieczeń. 

2. Wybierz pozycję **reguły zabezpieczeń dla ruchu przychodzącego**, a następnie wybierz pozycję **Dodaj**.

    ![Dodaj regułę ruchu przychodzącego](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Wybierz pozycję **Zaawansowane**. 

4. Wybierz wspólną **usługę** z menu rozwijanego, na przykład **http**. Możesz również wybrać opcję **niestandardowe** , jeśli chcesz podać konkretny port. 

5. Opcjonalnie można zmienić **priorytet** lub **nazwę**. Priorytet ma wpływ na kolejność stosowania reguł: mniejsza wartość liczbowa, przed zastosowaniem reguły.

6. Wybierz pozycję **Dodaj** , aby utworzyć regułę.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Kojarzenie sieciowej grupy zabezpieczeń z podsiecią

Ostatni krok to skojarzenie sieciowej grupy zabezpieczeń z podsiecią lub określonym interfejsem sieciowym. Na potrzeby tego przykładu zostanie skojarzona sieciowa Grupa zabezpieczeń z podsiecią. 

1. Wybierz pozycję **podsieci**, a następnie wybierz pozycję **Skojarz**.

    ![Kojarzenie sieciowej grupy zabezpieczeń z podsiecią](./media/nsg-quickstart-portal/associate-subnet.png)

2. Wybierz sieć wirtualną, a następnie wybierz odpowiednią podsieć.

    ![Kojarzenie sieciowej grupy zabezpieczeń z sieciami wirtualnymi](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Wszystkie maszyny wirtualne, które są połączone z tą podsiecią, są teraz dostępne na porcie 80.

## <a name="additional-information"></a>Dodatkowe informacje

[Kroki opisane w tym artykule można także wykonać przy użyciu Azure PowerShell](nsg-quickstart-powershell.md).

Polecenia opisane w tym artykule pozwalają szybko uzyskać ruch sieciowy przepływający na maszynę wirtualną. Sieciowe grupy zabezpieczeń zapewniają wiele doskonałych funkcji i szczegółowości kontroli dostępu do zasobów. Aby uzyskać więcej informacji, zobacz [Filtrowanie ruchu sieciowego za pomocą sieciowej grupy zabezpieczeń](../../virtual-network/tutorial-filter-network-traffic.md).

W przypadku aplikacji sieci Web o wysokiej dostępności Rozważ umieszczenie maszyn wirtualnych za modułem równoważenia obciążenia platformy Azure. Moduł równoważenia obciążenia dystrybuuje ruch do maszyn wirtualnych z sieciową grupą zabezpieczeń, która zapewnia filtrowanie ruchu. Aby uzyskać więcej informacji, zobacz [równoważenie obciążenia maszyn wirtualnych systemu Windows na platformie Azure w celu utworzenia aplikacji o wysokiej](tutorial-load-balancer.md)dostępności.

## <a name="next-steps"></a>Następne kroki
W tym artykule utworzono sieciową grupę zabezpieczeń, utworzoną regułę ruchu przychodzącego, która zezwala na ruch HTTP na porcie 80, a następnie skojarzono tę regułę z podsiecią. 

Informacje na temat tworzenia bardziej szczegółowych środowisk można znaleźć w następujących artykułach:
- [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
- [Grupy zabezpieczeń](../../virtual-network/security-overview.md)