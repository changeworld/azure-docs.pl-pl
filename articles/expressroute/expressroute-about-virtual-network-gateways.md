---
title: Temat bram sieci wirtualnej usługi ExpressRoute — Azure | Dokumentacja firmy Microsoft
description: Informacje na temat bram sieci wirtualnej dla usługi ExpressRoute. Ten artykuł zawiera informacje o jednostkach SKU bramy i typów.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: d9c607114d6c6c56c25303a88dcc11f4ab804eb4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60367984"
---
# <a name="about-virtual-network-gateways-for-expressroute"></a>Informacje o bramach sieci wirtualnej dla usługi ExpressRoute
Brama sieci wirtualnej jest używany do wysyłania ruchu sieciowego między sieciami wirtualnymi platformy Azure i lokalizacjami lokalnymi. Za pomocą bramy sieci wirtualnej dla usługi ExpressRoute ruchu lub ruch sieci VPN. Ten artykuł koncentruje się na bramy sieci wirtualnej usługi ExpressRoute i zawiera informacje o jednostkach SKU, szacowana wydajność przez jednostki SKU i typy bram.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="gateway-types"></a>Typy bram

Podczas tworzenia bramy sieci wirtualnej, należy określić kilka ustawień. Jeden z wymaganych ustawień "-GatewayType", określa, czy brama jest używana w przypadku usługi ExpressRoute lub sieci VPN ruch. Dostępne są następujące typy dwóch bram:

* **Sieć VPN** — Aby wysyłać zaszyfrowany ruch sieciowy w publicznej sieci Internet, należy użyć typu bramy "Vpn". Jest to również nazywane bramy sieci VPN. Wszystkie połączenia typu lokacja-lokacja, punkt-lokacja i połączenia między sieciami wirtualnymi używają bramy sieci VPN.

* **Usługa ExpressRoute** — do wysyłania ruchu sieciowego na połączenia prywatnego, należy użyć typu bramy "ExpressRoute". To jest również określany jako brama usługi ExpressRoute i jest to typ bramy używanej podczas konfigurowania usługi ExpressRoute.

Każda sieć wirtualna może mieć tylko jedną bramę sieci wirtualnej na typ bramy. Na przykład można mieć jedną bramę sieci wirtualnej, która używa klasy -GatewayType Vpn, oraz jednej, która używa klasy -GatewayType ExpressRoute.

## <a name="gwsku"></a>Jednostki SKU bramy
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Jeśli chcesz uaktualnić brama jednostki SKU bramy bardziej wydajne, można użyć polecenia cmdlet programu PowerShell "Zmiany rozmiaru AzVirtualNetworkGateway" w większości przypadków. Będzie on działać w przypadku uaktualnień do Standard i Highperformance. Jednak aby przeprowadzić uaktualnienie do jednostki SKU UltraPerformance, należy ponownie utworzyć bramę. Ponowne tworzenie bramy wiąże się z przestojem.

### <a name="aggthroughput"></a>Szacowany wydajności według jednostek SKU bramy
W poniższej tabeli przedstawiono typy bram i szacowaną parametrów. Ta tabela ma zastosowanie w obu modelach wdrażania — przy użyciu usługi Resource Manager i klasycznym.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Wydajność aplikacji zależy od wielu czynników, takich jak opóźnienie end-to-end i liczba przepływów ruchu, który aplikacja zostanie otwarta. Liczby w tabeli reprezentują górną granicę, którą aplikacji może teoretycznie osiągnąć w idealne środowisko.
>
>

### <a name="zrgw"></a>Strefowo nadmiarowe jednostki SKU bramy.

Można także wdrożyć bramy usługi ExpressRoute w strefach dostępności platformy Azure. To fizycznie i logicznie oddzielone w różnych strefach dostępności w celu ochrony łączności sieci środowiska lokalnego do platformy Azure przed awariami poziomu strefy.

![Strefowo nadmiarowe bramę usługi ExpressRoute](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Strefowo nadmiarowe bramy używają określonych nowej jednostki SKU bramy dla bramy usługi ExpressRoute.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Nowe jednostki SKU bramy obsługują także inne opcje wdrażania, aby możliwie najlepiej odpowiadały potrzebom użytkownika. Podczas tworzenia bramy sieci wirtualnej przy użyciu nowej jednostki SKU bramy, masz również możliwość wdrożenia bramy z określonej strefy. Jest to określane jako bramy strefowych. Podczas wdrażania bramy strefowych, wszystkie wystąpienia bramy są wdrażane w tej samej strefie dostępności.

## <a name="resources"></a>Polecenia cmdlet programu PowerShell i interfejsów API REST
Aby uzyskać dodatkowe zasoby techniczne i wymagań określonej składni, korzystając z interfejsów API REST i poleceń cmdlet programu PowerShell dla konfiguracji bramy sieci wirtualnej zobacz następujące strony:

| **Wdrożenie klasyczne** | **Resource Manager** |
| --- | --- |
| [Program PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[Program PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [Interfejs API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[Interfejs API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Kolejne kroki
Zobacz [Omówienie usługi ExpressRoute](expressroute-introduction.md) Aby uzyskać więcej informacji o konfiguracjach dostępnego połączenia.

Zobacz [Tworzenie bramy sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) Aby uzyskać więcej informacji o tworzeniu bramy usługi ExpressRoute.

Zobacz [Tworzenie bramy sieci wirtualnej strefowo nadmiarowe](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md) Aby uzyskać więcej informacji o konfigurowaniu bram strefowo nadmiarowe.
