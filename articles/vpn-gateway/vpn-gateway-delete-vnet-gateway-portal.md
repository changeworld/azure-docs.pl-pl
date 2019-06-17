---
title: 'Usuwanie bramy sieci wirtualnej: Witryna Azure Portal: Resource Manager | Microsoft Docs'
description: Usuwanie bramy sieci wirtualnej przy użyciu portalu Azure w modelu wdrażania usługi Resource Manager.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.date: 10/23/2018
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: 387b4e982772f22453876e1ea8b9e7c4039601c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60845705"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Usuwanie bramy sieci wirtualnej przy użyciu portalu

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Program PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasyczny)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Ten artykuł zawiera instrukcje dotyczące usuwania bram Azure VPN Gateway wdrożonych za pomocą modelu wdrażania usługi Resource Manager. Istnieje kilka różnych metod, które można podjąć usunąć bramę sieci wirtualnej dla konfiguracji bramy sieci VPN.

- Jeśli chcesz usunąć wszystkie elementy i zacząć od początku, tak jak w przypadku środowiska testowego, możesz usunąć grupę zasobów. Podczas usuwania grupy zasobów powoduje usunięcie wszystkich zasobów w grupie. Ta metoda jest zalecana tylko, jeśli nie chcesz zachować zasobów w grupie zasobów. Nie można selektywnie usunąć tylko kilka zasobów, przy użyciu tej metody.

- Jeśli chcesz zachować niektóre zasoby w grupie zasobów, usuwanie bramy sieci wirtualnej staje się nieco bardziej skomplikowane. Aby można było usunąć bramę sieci wirtualnej, należy najpierw usunąć wszystkie zasoby, które są zależne od bramą. Czynności, które należy wykonać zależą od typu połączenia, które zostały utworzone i zasoby zależne, dla każdego połączenia.

> [!IMPORTANT]
> W poniższych instrukcjach opisano sposób usuwania bram Azure VPN Gateway wdrożonych za pomocą modelu wdrażania usługi Resource Manager. Aby usunąć bramę sieci VPN, wdrożone za pomocą klasycznego modelu wdrażania, należy użyć programu Azure PowerShell zgodnie z opisem [tutaj](vpn-gateway-delete-vnet-gateway-classic-powershell.md).


## <a name="delete-a-vpn-gateway"></a>Usuwanie bramy VPN Gateway

Aby usunąć bramę sieci wirtualnej, musisz najpierw usunąć wszystkie zasoby, które odnoszą się do bramy sieci wirtualnej. Zasoby muszą zostać usunięte w określonej kolejności z powodu zależności.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

W tym momencie usuwana jest brama sieci wirtualnej. Następne kroki pomóc usunąć wszystkie zasoby, które są już używane.

### <a name="to-delete-the-local-network-gateway"></a>Aby usunąć bramę sieci lokalnej

1. W **wszystkie zasoby**, zlokalizuj bramy sieci lokalnej, które skojarzono z każdego połączenia.
2. Na **Przegląd** kliknij blok bramy sieci lokalnej **Usuń**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Aby usunąć zasób publicznego adresu IP bramy

1. W **wszystkie zasoby**, Znajdź zasób publicznego adresu IP przypisanego do bramy. Jeśli brama sieci wirtualnej było aktywne aktywne, zobaczysz dwa publiczne adresy IP. 
2. Na **Przegląd** stronie publiczny adres IP, kliknij przycisk **Usuń**, następnie **tak** o potwierdzenie.

### <a name="to-delete-the-gateway-subnet"></a>Aby usunąć podsieć bramy

1. W **wszystkie zasoby**, zlokalizuj sieci wirtualnej. 
2. Na **podsieci** bloku kliknij **GatewaySubnet**, następnie kliknij przycisk **Usuń**. 
3. Kliknij przycisk **tak** aby upewnić się, że chcesz usunąć podsieć bramy.

## <a name="deleterg"></a>Usuwanie bramy sieci VPN, usuwając grupę zasobów

Jeśli nie jesteś zajmującym się przechowywanie wszystkich zasobów w grupie zasobów i po prostu chcesz zacząć od początku, możesz usunąć całą grupę zasobów. Jest to szybki sposób, aby usunąć wszystkie elementy. Poniższe kroki mają zastosowanie tylko do modelu wdrażania usługi Resource Manager.

1. W **wszystkie zasoby**, Znajdź grupę zasobów i kliknij, aby otworzyć blok.
2. Kliknij przycisk **Usuń**. W bloku usuwanie wyświetlanie uwzględnionych zasobów. Upewnij się, że chcesz usunąć wszystkich tych zasobów. Jeśli nie, wykonaj kroki w obszarze usuwanie bramy sieci VPN w górnej części tego artykułu.
3. Aby kontynuować, wpisz nazwę grupy zasobów, którą chcesz usunąć, a następnie kliknij przycisk **Usuń**.
