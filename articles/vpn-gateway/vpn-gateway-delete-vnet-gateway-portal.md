---
title: 'Usuwanie bramy sieci wirtualnej: portalu Azure: Resource Manager | Dokumentacja firmy Microsoft'
description: Usuń bramę sieci wirtualnej przy użyciu portalu Azure w modelu wdrażania usługi Resource Manager.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: cherylmc
ms.openlocfilehash: b014d43ab25124d3e08e19ca190b320fc8456593
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2018
ms.locfileid: "27593460"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Usuwanie bramy sieci wirtualnej przy użyciu portalu

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Program PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasyczny)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Ten artykuł zawiera instrukcje dotyczące usuwania bram sieci VPN platformy Azure, wdrożyć przy użyciu modelu wdrażania usługi Resource Manager. Istnieje kilka różnych metod, które można wykonać, gdy chcesz usunąć bramę sieci wirtualnej dla konfiguracji bramy sieci VPN.

- Jeśli chcesz usunąć wszystkie elementy i rozpocząć od początku, tak jak w przypadku środowiska testowego, można usunąć grupy zasobów. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów w grupie. Ta metoda jest zalecana tylko, jeśli nie chcesz zachować zasobów w grupie zasobów. Nie można selektywnie usunąć tylko kilka zasobów, przy użyciu tej metody.

- Jeśli chcesz zachować niektóre zasoby w grupie zasobów, usuwanie bramy sieci wirtualnej staje się nieco bardziej skomplikowane. Aby można było usunąć bramę sieci wirtualnej, należy najpierw usunąć wszystkie zasoby, które są zależne od bramy. Kroki, które należy wykonać są zależne od typu połączenia, które zostały utworzone i zasoby zależne, dla każdego połączenia.

> [!IMPORTANT]
> Poniższe instrukcje opisują sposób Usuń wdrożenie za pomocą modelu wdrażania usługi Resource Manager bram sieci VPN platformy Azure. Aby usunąć bramę sieci VPN wdrożony przy użyciu klasycznego modelu wdrażania, użyj programu Azure PowerShell zgodnie z opisem [tutaj](vpn-gateway-delete-vnet-gateway-classic-powershell.md).


## <a name="delete-a-vpn-gateway"></a>Usuwanie bramy VPN Gateway

Aby usunąć bramę sieci wirtualnej, należy najpierw usunąć wszystkie zasoby, które odnoszą się do bramy sieci wirtualnej. Zasoby muszą zostać usunięte w określonej kolejności z powodu zależności.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

W tym momencie Brama sieci wirtualnej została usunięta. Następne kroki pomóc usunąć wszystkie zasoby, które są już używane.

### <a name="to-delete-the-local-network-gateway"></a>Aby usunąć bramę sieci lokalnej

1. W **wszystkie zasoby**, zlokalizuj bram sieci lokalnej, które zostały skojarzone z każdego połączenia.
2. Na **omówienie** bloku dla bramy sieci lokalnej, kliknij przycisk **usunąć**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Aby usunąć zasób adres publiczny adres IP dla bramy

1. W **wszystkie zasoby**, zlokalizuj zasób adres publiczny adres IP, który został skojarzony z bramą. Jeśli brama sieci wirtualnej był aktywny aktywny, zostanie wyświetlony dwa publiczne adresy IP. 
2. Na **omówienie** dla publicznego adresu IP kliknij pozycję **usunąć**, następnie **tak** o potwierdzenie.

### <a name="to-delete-the-gateway-subnet"></a>Aby usunąć podsieć bramy

1. W **wszystkie zasoby**, zlokalizuj sieci wirtualnej. 
2. Na **podsieci** bloku, kliknij przycisk **GatewaySubnet**, następnie kliknij przycisk **usunąć**. 
3. Kliknij przycisk **tak** aby upewnić się, że chcesz usunąć podsieci bramy.

## <a name="deleterg"></a>Usuwanie bramy sieci VPN przez usunięcie grupy zasobów

Jeśli nie są dane dotyczące poszczególnych zasobów w grupie zasobów i po prostu chcesz zacząć od nowa, należy usunąć całej grupy zasobów. Jest to szybko Usuń całą. Poniższe kroki dotyczą tylko modelu wdrażania usługi Resource Manager.

1. W **wszystkie zasoby**Znajdź grupę zasobów i kliknij, aby otworzyć blok.
2. Kliknij polecenie **Usuń**. W bloku Delete wyświetlania wykorzystywanych zasobów. Upewnij się, że chcesz usunąć wszystkich tych zasobów. Jeśli nie, wykonaj kroki w [usunąć bramę sieci VPN](#deletegw) w górnej części tego artykułu.
3. Aby kontynuować, wpisz nazwę grupy zasobów, które chcesz usunąć, a następnie kliknij przycisk **usunąć**.
