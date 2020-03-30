---
title: 'Brama sieci VPN platformy Azure: usuwanie bramy: portal'
description: Usuń bramę sieci wirtualnej przy użyciu portalu Azure w modelu wdrażania usługi Resource Manager.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 10/23/2018
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: fba213be2de3b747d3ba962674a03c0bc7df3ed5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863669"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Usuwanie bramy sieci wirtualnej za pomocą portalu

> [!div class="op_single_selector"]
> * [Portal Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Powershell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasyczny)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Ten artykuł zawiera instrukcje dotyczące usuwania bram sieci VPN platformy Azure wdrożonych przy użyciu modelu wdrażania Usługi Resource Manager. Istnieje kilka różnych metod, które można podjąć, gdy chcesz usunąć bramę sieci wirtualnej dla konfiguracji bramy sieci VPN.

- Jeśli chcesz usunąć wszystko i zacząć od nowa, tak jak w przypadku środowiska testowego, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów w grupie. Ta metoda jest zalecana tylko wtedy, gdy nie chcesz zachować żadnych zasobów w grupie zasobów. Nie można selektywnie usunąć tylko kilka zasobów przy użyciu tej metody.

- Jeśli chcesz zachować niektóre zasoby w grupie zasobów, usunięcie bramy sieci wirtualnej staje się nieco bardziej skomplikowane. Przed usunięciem bramy sieci wirtualnej należy najpierw usunąć wszystkie zasoby zależne od bramy. Kroki, które należy wykonać, zależą od typu połączeń, które zostały utworzone i zasobów zależnych dla każdego połączenia.

> [!IMPORTANT]
> W poniższych instrukcjach opisano sposób usuwania bram sieci VPN platformy Azure wdrożonych przy użyciu modelu wdrażania Usługi Resource Manager. Aby usunąć bramę sieci VPN wdrożoną przy użyciu klasycznego modelu wdrażania, należy użyć programu Azure PowerShell w sposób opisany [poniżej](vpn-gateway-delete-vnet-gateway-classic-powershell.md).


## <a name="delete-a-vpn-gateway"></a>Usuwanie bramy VPN Gateway

Aby usunąć bramę sieci wirtualnej, należy najpierw usunąć każdy zasób odnoszący się do bramy sieci wirtualnej. Zasoby muszą zostać usunięte w określonej kolejności z powodu zależności.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

W tym momencie brama sieci wirtualnej jest usuwany. Następne kroki pomagają usunąć wszystkie zasoby, które nie są już używane.

### <a name="to-delete-the-local-network-gateway"></a>Aby usunąć bramę sieci lokalnej

1. W **aplikacji Wszystkie zasoby**znajdź bramy sieci lokalnej skojarzone z każdym połączeniem.
2. W bloku **Przegląd** bramy sieci lokalnej kliknij pozycję **Usuń**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Aby usunąć zasób Publiczny adres IP bramy

1. W **aplikacji Wszystkie zasoby**znajdź zasób publiczny adres IP skojarzony z bramą. Jeśli brama sieci wirtualnej była aktywna jako aktywna, zostaną wyświetlone dwa publiczne adresy IP. 
2. Na stronie **Przegląd** publicznego adresu IP kliknij pozycję **Usuń**, a następnie **tak,** aby potwierdzić.

### <a name="to-delete-the-gateway-subnet"></a>Aby usunąć podsieć bramy

1. W **wszystkich zasobach**zlokalizuj sieć wirtualną. 
2. W bloku **Podsieci** kliknij **przycisk GatewaySubnet**, a następnie kliknij przycisk **Usuń**. 
3. Kliknij **przycisk Tak,** aby potwierdzić, że chcesz usunąć podsieć bramy.

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="deleterg"></a>Usuwanie bramy sieci VPN przez usunięcie grupy zasobów

Jeśli nie obawiasz się, że nie masz żadnych zasobów w grupie zasobów i chcesz zacząć od nowa, możesz usunąć całą grupę zasobów. Jest to szybki sposób, aby usunąć wszystko. Poniższe kroki dotyczą tylko modelu wdrażania Menedżera zasobów.

1. W **dziale Wszystkie zasoby**zlokalizuj grupę zasobów i kliknij, aby otworzyć blok.
2. Kliknij **pozycję Usuń**. Na Delete bloku, zobacz zasoby, których dotyczy problem. Upewnij się, że chcesz usunąć wszystkie te zasoby. Jeśli nie, użyj kroków w usuń bramę sieci VPN u góry tego artykułu.
3. Aby kontynuować, wpisz nazwę grupy zasobów, którą chcesz usunąć, a następnie kliknij przycisk **Usuń**.
