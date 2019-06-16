---
title: Temat bram sieci wirtualnej strefowo nadmiarowy w strefach dostępności platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat bram VPN Gateway i ExpressRoute w strefach dostępności.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 0ba818ef3c24d0e88e662adf87b22cc938fe5fab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60391076"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Temat bram sieci wirtualnej strefowo nadmiarowy w strefach dostępności platformy Azure

Można wdrożyć bramy sieci VPN i ExpressRoute w [strefy dostępności platformy Azure](../availability-zones/az-overview.md). To zapewniają elastyczność, skalowalność i większą dostępność bramy sieci wirtualnej. Wdrażanie bramy w strefach dostępności Azure fizycznie i logicznie dzieli bramy w danym regionie, przy jednoczesnej ochronie łączności sieci środowiska lokalnego do platformy Azure przed awariami poziomu strefy.

### <a name="zrgw"></a>Strefowo nadmiarowe bram

Aby automatycznie wdrażać bram sieci wirtualnej w różnych strefach dostępności, można użyć bramy sieci wirtualnej strefowo nadmiarowe. Strefowo nadmiarowe bram możesz korzystać z odporności stref na dostęp do usługi o kluczowym znaczeniu, skalowalnych usług na platformie Azure.

<br>
<br>

![Grafika bram strefowo nadmiarowy](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Strefowy bram

Do wdrożenia bramy z określonej strefy, można użyć bramy strefowych. Podczas wdrażania bramy strefowych, wszystkie wystąpienia bramy są wdrażane w tej samej strefie dostępności.

<br>
<br>

![Grafika strefowych bram](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>Jednostki SKU bramy

Strefowo nadmiarowy i strefowych bram są dostępne jako nowe jednostki SKU bramy. Dodaliśmy nowe jednostki SKU bramy sieci wirtualnej w regionach AZ platformy Azure. Te jednostki SKU są podobne do odpowiedniej istniejącej jednostki SKU dla usługi ExpressRoute i VPN Gateway, z tą różnicą, że są one zależne strefowo nadmiarowy i strefowych bram.

Nowa brama jednostki SKU to:

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>Jednostki SKU publicznego adresu IP

Strefowo nadmiarowe bramy i bramy strefowych zależą od platformy Azure publiczny zasób adresu IP *standardowa* jednostki SKU. Konfigurację zasobów publicznych adresów IP platformy Azure Określa, czy bramy, który można wdrożyć strefowo nadmiarowe, lub strefowych. Jeśli utworzysz publiczny zasób adresu IP mający *podstawowe* jednostki SKU, brama nie będzie żadnych nadmiarowości strefy i zasoby bramy będzie regionalne.

### <a name="pipzrg"></a>Strefowo nadmiarowe bram

Po utworzeniu publicznego adresu IP, za pomocą **standardowa** publicznego adresu IP jednostki SKU bez określania strefy i zachowanie różni się w zależności od tego, czy brama jest brama sieci VPN lub brama usługi ExpressRoute. 

* Dla bramy sieci VPN wystąpienia dwóch bramy zostanie wdrożony w dowolnym 2 poza te trzy strefy, w celu zapewnienia nadmiarowości strefy. 
* W przypadku bramy usługi ExpressRoute ponieważ może istnieć więcej niż dwóch wystąpień, bramy mogą rozciągać się na trzy strefy.

### <a name="pipzg"></a>Strefowy bram

Po utworzeniu publicznego adresu IP, za pomocą **standardowa** publicznego adresu IP jednostki SKU i określenie strefy (1, 2 lub 3), wszystkie wystąpienia bramy zostanie wdrożony w tej samej strefie.

### <a name="piprg"></a>Regionalne bram

Po utworzeniu publicznego adresu IP, za pomocą **podstawowe** publicznego adresu IP jednostki SKU i bramy jest wdrażany jako regionalne bramy i nie ma żadnych strefy nadmiarowości wbudowanej w bramie.

## <a name="faq"></a>Często zadawane pytania

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Co się zmieni podczas wdrażania tych nowych jednostek SKU?

Z perspektywy można wdrożyć bram dzięki nadmiarowości strefy. Oznacza to, że wszystkie wystąpienia bramy zostanie wdrożone w różnych strefach dostępności platformy Azure, a każda strefa dostępności w innej domenie błędów i aktualizacji. Dzięki temu bram niezawodne, dostępna i odporna na awarie strefy.

### <a name="can-i-use-the-azure-portal"></a>Czy można użyć witryny Azure portal?

Tak, można użyć witryny Azure portal do wdrażania nowych jednostek SKU. Jednak zobaczą te nowe jednostki SKU tylko w tych regionach platformy Azure, które mają strefy dostępności platformy Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Jakie regiony są dostępne w użyciu nowych jednostek SKU?

Nowe jednostki SKU są dostępne w regionach platformy Azure, które mają strefy dostępności platformy Azure — środkowe stany USA, Francja środkowa, Europa Północna, Europa Zachodnia i zachodnie stany USA 2 regionach. Idąc dalej, firma Microsoft będzie udostępniać strefowo nadmiarowe bramy do Ciebie w innych regionach publicznych platformy Azure.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Czy mogę zmiany/migracja/uaktualnić Moje istniejącej bramy sieci wirtualnej do bram strefowo nadmiarowego lub strefowych?

Migrowanie istniejących bram sieci wirtualnej do bram strefowo nadmiarowego lub strefowych nie jest obecnie obsługiwane. Można jednak usunąć istniejącą bramę i ponownie utworzyć bramę strefowo nadmiarowego lub strefowych.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Czy można wdrożyć bramy sieci VPN i Expressroute w tej samej sieci wirtualnej?

Współistnienie bram sieci VPN i Expressroute w tej samej sieci wirtualnej jest obsługiwana. Jednak należy zarezerwować/27 zakres adresów IP podsieci bramy.

## <a name="next-steps"></a>Następne kroki

[Tworzenie strefowo nadmiarowej bramy sieci wirtualnej](create-zone-redundant-vnet-gateway.md)
