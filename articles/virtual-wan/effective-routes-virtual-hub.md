---
title: 'Wyświetlanie skutecznych tras wirtualnego centrum: wirtualna sieć WAN platformy Azure | Dokumenty firmy Microsoft'
description: Vie skuteczne trasy dla wirtualnego centrum w wirtualnej sieci WAN platformy Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515852"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Wyświetlanie skutecznych tras wirtualnego koncentratora

Możesz wyświetlić wszystkie trasy centrum wirtualnej sieci WAN w witrynie Azure portal. Aby wyświetlić trasy, przejdź do koncentratora wirtualnego, a następnie wybierz pozycję **Routing -> Wyświetl trasy efektywne**.

## <a name="understanding-routes"></a><a name="understand"></a>Opis tras

Poniższy przykład może pomóc lepiej zrozumieć, jak wirtualne routingu sieci WAN.

W tym przykładzie mamy wirtualną sieć WAN z trzema koncentratorami. Pierwszy hub znajduje się w regionie Wschodnich Stanów Zjednoczonych, drugi hub znajduje się w regionie Europy Zachodniej, a trzeci znajduje się w regionie Zachodnie Stany Zjednoczone. W wirtualnej sieci WAN wszystkie koncentratory są ze sobą połączone. W tym przykładzie założono, że wschodnie centra stany USA i Europa Zachodnia mają połączenia z lokalnych oddziałów (szprych) i sieci wirtualnych platformy Azure (szprych).

Adres wirtualny platformy Azure (10.4.0.0/16) z wirtualnym urządzeniem sieciowym (10.4.0.6) jest dalej równorzędny z siecią wirtualną (10.5.0.0/16). Zobacz [dodatkowe informacje](#abouthubroute) w dalszej części tego artykułu, aby uzyskać więcej informacji na temat tabeli tras koncentratora.

W tym przykładzie zakładamy również, że oddział 1 w Europie Zachodniej jest połączony z węzłem Wschodnie stany USA, a także z centrum Europy Zachodniej. Obwód usługi ExpressRoute w wschodnich stanach USA łączy oddział 2 z centrum wschodnie stany USA.

![diagram](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>Wyświetlanie efektywnych tras

Po wybraniu opcji "Wyświetl trasy efektywne" w portalu, tworzy dane wyjściowe wyświetlane w [tabeli trasy centrum](#routetable) dla centrum wschodnich stanów USA.

Aby umieścić to w perspektywie, pierwszy wiersz oznacza, że centrum wschodnie stany USA nauczyło się trasy 10.20.1.0/24 (Gałąź 1) z powodu połączenia *typu NASTĘPNEGO przeskoku* SIECI VPN (wystąpienie bramy sieci VPN następnego przeskoku00 IP 10.1.0.6, wystąpienie 1 IP 10.1.0.7). *Punkt początkowy trasy* wskazuje identyfikator zasobu. *As Path* wskazuje ścieżkę AS dla gałęzi 1.

### <a name="hub-route-table"></a><a name="routetable"></a>Tabela trasy koncentratora

Użyj paska przewijania u dołu tabeli, aby wyświetlić "Ścieżkę AS".

| **Prefiks** |  **Typ następnego skoku** | **Następny przeskok** |  **Początek trasy** |**Ścieżka AS** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|Sieć VPN |10.1.0.6, 10.1.0.7| /subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d855555-eastus-gw|21000|
|10.23.1.0/24| Sieć VPN |10.1.0.6, 10.1.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw|23000|
|10.4.0.0/16|Połączenie z siecią wirtualną| On-link |  |  |
|10.5.0.0/16| Adres IP| 10.4.0.6|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|0.0.0.0/0| Adres IP| `<Azure Firewall IP>` |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|10.22.1.0/16| Koncentrator zdalny|10.8.0.6, 10.8.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_| 4848-22000 |
|10.9.0.0/16| Koncentrator zdalny|  On-link |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_1| |

>[!NOTE]
> Gdyby centra wschodnich stanów USA i Europy Zachodniej nie komunikowały się ze sobą w przykładowej topologii, nauczona trasa (10.9.0.0/16) nie istniałaby. Koncentratory anonsują tylko sieci, które są bezpośrednio z nimi połączone.
>

## <a name="additional-information"></a><a name="additional"></a>Dodatkowe informacje

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>Tabela trasy koncentratora – informacje

Można utworzyć wirtualną trasę koncentratora i zastosować trasę do tabeli trasy koncentratora wirtualnego. Możesz zastosować wiele tras do tabeli tras koncentratora wirtualnego. Dzięki temu można ustawić trasę dla docelowej sieci wirtualnej za pośrednictwem adresu IP (zazwyczaj sieciowego urządzenia wirtualnego (NVA) w sieci wirtualnej szprychy). Aby uzyskać więcej informacji na temat sieciowe, zobacz [Kierowanie ruchu z koncentratora wirtualnego do urządzenia WUS](virtual-wan-route-table-portal.md).

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>Informacje o trasie domyślnej (0.0.0.0/0)

Koncentrator wirtualny ma możliwość propagowania wyuczonej domyślnej trasy do sieci wirtualnej, sieci VPN typu lokacja-lokacja i połączenia usługi ExpressRoute, jeśli flaga jest "Włączona" w połączeniu. Ta flaga jest widoczna podczas edytowania połączenia sieci wirtualnej, połączenia sieci VPN lub połączenia usługi ExpressRoute. "EnableInternetSecurity" jest zawsze false domyślnie w hub vnet, expressroute i połączeń sieci VPN.

Trasa domyślna nie pochodzi z wirtualnego koncentratora sieci WAN. Trasa domyślna jest propagowana, jeśli jest już prowadzona przez koncentrator wirtualnej sieci WAN w wyniku wdrożenia zapory w centrum lub jeśli inna połączona lokacja wymusiła tunelowanie włączone.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz [Omówienie usługi Virtual WAN](virtual-wan-about.md).