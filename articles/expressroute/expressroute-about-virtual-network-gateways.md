---
title: Temat bram sieci wirtualnej ExpressRoute | Dokumentacja firmy Microsoft
description: Informacje na temat bram sieci wirtualnej dla usługi ExpressRoute.
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-resource-manager, azure-service-management
ms.assetid: 7e0d9658-bc00-45b0-848f-f7a6da648635
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: cherylmc
ms.openlocfilehash: ae971e7743d6dd3269c0a4f976bd2a5316300f58
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="about-virtual-network-gateways-for-expressroute"></a>Informacje o bramach sieci wirtualnej dla usługi ExpressRoute
Brama sieci wirtualnej służy do wysyłania ruchu sieciowego między sieciami wirtualnymi platformy Azure i lokalnymi lokalizacji. Po skonfigurowaniu połączenia ExpressRoute, należy utworzyć i skonfigurować bramę sieci wirtualnej i połączenia bramy sieci wirtualnej.

Podczas tworzenia bramy sieci wirtualnej należy określić kilka ustawień. Jeden z wymaganych ustawień Określa, czy brama będzie używana dla ruchu ExpressRoute i sieci VPN typu lokacja-lokacja. W modelu wdrażania usługi Resource Manager, jest ustawienie "-elementu GatewayType".

Gdy ruch sieciowy będzie przesyłany w połączeniu sieci prywatnej, używając typu bramy "ExpressRoute". Ten typ bramy jest również określany jako brama usługi ExpressRoute. Gdy ruch sieciowy będzie przesyłany zaszyfrowane w publicznej sieci Internet, używając typu bramy "Vpn". Ten typ bramy jest określany jako brama sieci VPN. Wszystkie połączenia typu lokacja-lokacja, punkt-lokacja i połączenia między sieciami wirtualnymi używają bramy sieci VPN.

Każda sieć wirtualna może mieć tylko jedną bramę sieci wirtualnej na typ bramy. Na przykład można mieć jedną bramę sieci wirtualnej, która używa klasy -GatewayType Vpn, oraz jednej, która używa klasy -GatewayType ExpressRoute. Ten artykuł skupia się na bramę sieci wirtualnej usługi ExpressRoute.

## <a name="gwsku"></a>Jednostki SKU bramy
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Jeśli chcesz uaktualnić bramę do bardziej zaawansowanych jednostka SKU bramy, w większości przypadków służy polecenie cmdlet programu PowerShell "Zmiany rozmiaru AzureRmVirtualNetworkGateway". Będzie działać, dotyczy uaktualnień do standardowej i wysokowydajnej jednostki SKU. Jednak aby uaktualnić do wersji UltraPerformance, należy ponownie utworzyć bramę.

### <a name="aggthroughput"></a>Szacowany parametrów przez jednostka SKU bramy
W poniższej tabeli przedstawiono typy bramy i szacowany parametrów. Ta tabela ma zastosowanie w obu modelach wdrażania — przy użyciu usługi Resource Manager i klasycznym.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Wydajność aplikacji zależy od wielu czynników, takich jak czas oczekiwania na trasie, a liczba przepływów ruchu sieciowego, który otwiera aplikację. Numery w tabeli reprezentuje górny limit, który aplikacja teoretycznie można uzyskać w środowisku idealne. 
> 
>

## <a name="resources"></a>Polecenia cmdlet programu PowerShell i interfejsów API REST
Aby uzyskać dodatkowe zasoby techniczne i wymagania określonej składni, korzystając z polecenia cmdlet programu PowerShell i interfejsów API REST konfiguracji bramy sieci wirtualnej zobacz następujące strony:

| **Wdrożenie klasyczne** | **Resource Manager** |
| --- | --- |
| [Program PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[Program PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [Interfejs API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[Interfejs API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Kolejne kroki
Zobacz [omówienie ExpressRoute](expressroute-introduction.md) Aby uzyskać więcej informacji o konfiguracjach dostępnych połączeń.

Zobacz [Tworzenie bramy sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) Aby uzyskać więcej informacji o tworzeniu bram usługi ExpressRoute.
