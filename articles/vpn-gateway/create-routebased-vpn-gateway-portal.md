---
title: 'Tworzenie bramy sieci VPN opartej na trasach: Azure Portal | Microsoft Docs'
description: Tworzenie VPN Gateway opartej na trasach przy użyciu Azure Portal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: cherylmc
ms.openlocfilehash: 2a04c0fa2d92514103377c2aef420290d1bdd057
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781166"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Tworzenie bramy sieci VPN opartej na trasach za pomocą Azure Portal

Ten artykuł pomaga w szybkim tworzeniu bramy sieci VPN opartej na trasach przy użyciu Azure Portal.  Brama sieci VPN jest używana podczas tworzenia połączenia sieci VPN z siecią lokalną. Do łączenia się z usługą sieci wirtualnych można także użyć bramy sieci VPN. 

Kroki opisane w tym artykule spowodują utworzenie sieci wirtualnej, podsieci, podsieci bramy i bramy sieci VPN opartej na trasach (bramy sieci wirtualnej). Po zakończeniu tworzenia bramy można utworzyć połączenia. Te kroki wymagają subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="vnet"></a>Tworzenie sieci wirtualnej

[!INCLUDE [create-gateway](../../includes/vpn-gateway-create-virtual-network-portal-include.md)]

## <a name="gwsubnet"></a>Dodawanie podsieci bramy

[!INCLUDE [gateway subnet](../../includes/vpn-gateway-add-gateway-subnet-portal-include.md)]

## <a name="gwvalues"></a>Konfigurowanie i tworzenie bramy

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

>[!NOTE]
>Jednostka SKU bramy podstawowej nie obsługuje uwierzytelniania IKEv2 ani RADIUS. Jeśli planujesz, aby klienci z systemem Mac mogli łączyć się z siecią wirtualną, nie używaj podstawowej jednostki SKU.

## <a name="viewgw"></a>Wyświetlanie bramy sieci VPN

1. Po utworzeniu bramy przejdź do VNet1 w portalu. Brama sieci VPN jest wyświetlana na stronie Przegląd jako połączone urządzenie.

   ![Podłączone urządzenia](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Podłączone urządzenia")

2. Na liście urządzeń kliknij pozycję **VNet1GW** , aby wyświetlić więcej informacji.

   ![Wyświetlanie bramy sieci VPN](./media/create-routebased-vpn-gateway-portal/view-gateway.png "Wyświetlanie bramy sieci VPN")

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu tworzenia bramy można utworzyć połączenie między Twoją siecią wirtualną a inną. Można też utworzyć połączenie między siecią wirtualną i lokalizacją lokalną.

> [!div class="nextstepaction"]
> [Utwórz połączenie lokacja-lokacja](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Tworzenie połączenia typu punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Utwórz połączenie z inną siecią wirtualną](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
