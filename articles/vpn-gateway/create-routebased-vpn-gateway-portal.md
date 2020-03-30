---
title: 'Tworzenie bramy sieci VPN opartej na trasie: portal'
titleSuffix: Azure VPN Gateway
description: Tworzenie bramy sieci VPN opartej na trasach przy użyciu portalu Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/03/2020
ms.author: cherylmc
ms.openlocfilehash: 6156d689a29ee348f9b1974d1520eb7d186a8d8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331353"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Tworzenie bramy sieci VPN opartej na trasach przy użyciu portalu Azure

Ten artykuł ułatwia szybkie tworzenie bramy sieci VPN opartej na marszrutach przy użyciu witryny Azure Portal.  Brama sieci VPN jest używana podczas tworzenia połączenia sieci VPN z siecią lokalną. Można również użyć bramy sieci VPN do łączenia sieci wirtualnych. 

Kroki opisane w tym artykule utworzy sieć wirtualną, podsieć, podsieć bramy i bramę sieci VPN opartą na trasie (brama sieci wirtualnej). Po zakończeniu tworzenia bramy można następnie utworzyć połączenia. Te kroki wymagają subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Tworzenie sieci wirtualnej

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="configure-and-create-the-gateway"></a><a name="gwvalues"></a>Konfigurowanie i tworzenie bramy

W tym kroku zostaje utworzona brama dla sieci wirtualnej użytkownika. Tworzenie bramy często może trwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

>[!NOTE]
>Jednostka SKU bramy podstawowej nie obsługuje uwierzytelniania IKEv2 lub RADIUS. Jeśli planujesz połączenie klientów mac z siecią wirtualną, nie należy używać podstawowej jednostki SKU.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Wyświetlanie bramy sieci VPN

1. Po utworzeniu bramy przejdź do sieci wirtualnej1 w portalu. Brama sieci VPN pojawi się na stronie Przegląd jako podłączone urządzenie.

   ![Połączone urządzenia](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Połączone urządzenia")

2. Na liście urządzeń kliknij pozycję **VNet1GW,** aby wyświetlić więcej informacji.

   ![Wyświetlanie bramy sieci VPN](./media/create-routebased-vpn-gateway-portal/view-gateway.png "Wyświetlanie bramy sieci VPN")

## <a name="next-steps"></a>Następne kroki

Po zakończeniu tworzenia bramy można utworzyć połączenie między siecią wirtualną a inną siecią wirtualną. Możesz też utworzyć połączenie między siecią wirtualną a lokalizacją lokalną.

> [!div class="nextstepaction"]
> [Tworzenie połączenia typu lokacja-lokacja](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Tworzenie połączenia typu punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Tworzenie połączenia z inną siecią wirtualną](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
