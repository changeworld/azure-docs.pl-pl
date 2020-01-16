---
title: Co to jest usługa Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Omówienie funkcji, architektury i implementacji usługi Azure Load Balancer. Dowiedz się, jak działa Load Balancer i jak korzystać z niego w chmurze.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/14/2019
ms.author: allensu
ms.openlocfilehash: dc986d40d50b93720c87ba36d265ed3044b0abc9
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045398"
---
# <a name="what-is-azure-load-balancer"></a>Co to jest usługa Azure Load Balancer?

*Równoważenie obciążenia* polega na równomiernym rozłożeniu obciążenia (ruch sieciowy przychodzący) między grupą zasobów lub serwerów zaplecza. Platforma Azure oferuje [różne opcje równoważenia obciążenia](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) , które można wybrać w zależności od potrzeb. Ten dokument zawiera Azure Load Balancer.

Azure Load Balancer działa w warstwie cztery z modelem połączenia typu Open Systems (OSI). Jest to pojedynczy punkt kontaktu dla klientów. Moduł równoważenia obciążenia dystrybuuje Nowe przepływy przychodzące, które docierają do frontonu modułu równoważenia obciążenia z wystąpieniami puli zaplecza. Te przepływy są zgodne ze skonfigurowanymi regułami równoważenia obciążenia i sondami kondycji. Wystąpienia puli zaplecza mogą być Virtual Machinesami platformy Azure lub wystąpieniami w zestawie skalowania maszyn wirtualnych.


**[Publiczny moduł równoważenia obciążenia](./concepts-limitations.md#publicloadbalancer)** może zapewnić połączenia wychodzące maszyn wirtualnych w sieci wirtualnej. Te połączenia są realizowane przez przetłumaczenie prywatnych adresów IP na publiczne adresy IP. Publiczne usługi równoważenia obciążenia są używane do równoważenia obciążenia ruchu internetowego na maszynach wirtualnych.

**[Wewnętrzny (lub prywatny) moduł równoważenia obciążenia](./concepts-limitations.md#internalloadbalancer)** jest używany w przypadku, gdy prywatne adresy IP są używane tylko na frontonie. Wewnętrzne moduły równoważenia obciążenia są używane do równoważenia obciążenia ruchu w sieci wirtualnej. Dostęp do frontonu modułu równoważenia obciążenia można uzyskać z sieci lokalnej w scenariuszu hybrydowym.

<div align="center">
  <img src='./media/load-balancer-overview/IC744147.png'>
</div>

*Ilustracja: Równoważenie aplikacji wielowarstwowych przy użyciu Load Balancer publicznego i wewnętrznego*

Aby uzyskać więcej informacji na temat poszczególnych składników usługi równoważenia obciążenia, zobacz [Azure Load Balancer składniki i ograniczenia](./concepts-limitations.md)

>[!NOTE]
> Firma Microsoft zaleca [Usługa Load Balancer w warstwie Standardowa](./load-balancer-standard-overview.md).
Autonomiczne maszyny wirtualne, zestawy dostępności i zestawy skalowania maszyn wirtualnych można połączyć tylko jednej jednostki SKU — nigdy do obu. Load Balancer oraz jednostka SKU publicznego adresu IP muszą być zgodne, gdy są używane z publicznymi adresami IP. Jednostki SKU Load Balancer i publicznych adresów IP nie są modyfikowalne.

## <a name="why-use-azure-load-balancer"></a>Dlaczego warto używać Azure Load Balancer?
Za pomocą Azure Load Balancer można skalować aplikacje i tworzyć usługi o wysokiej dostępności. Moduł równoważenia obciążenia obsługuje scenariusze przychodzące i wychodzące. Moduł równoważenia obciążenia zapewnia małe opóźnienia i wysoką przepływność oraz skaluje do milionów przepływów dla wszystkich aplikacji TCP i UDP.

Kluczowe scenariusze, które można wykonać za pomocą Azure Load Balancer obejmują:

- Równoważyć obciążenie ruchem **[wewnętrznym](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** i **[zewnętrznym](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** z maszynami wirtualnymi platformy Azure.

- Zwiększ dostępność przez dystrybucję zasobów **[w ramach](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** i **[między](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** strefami.

- Skonfiguruj **[łączność wychodzącą](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** dla maszyn wirtualnych platformy Azure.

- Do monitorowania zasobów o zrównoważonym obciążeniu służy **[sondy kondycji](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** .

- Zastosowanie **[przekazywania portów](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** w celu uzyskania dostępu do maszyn wirtualnych w sieci wirtualnej przez publiczny adres IP i port.

- Włącz obsługę **[równoważenia obciążenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** **[protokołu IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)** .

- Skorzystaj z **[metryk i diagnostyki](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)** Azure Load Balancer z **[Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview)** .

- Równoważyć obciążenie usług na **[wielu portach, wielu adresach IP lub obu](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)** .

- Przenoszenie **[wewnętrznych](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** i **[zewnętrznych](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** zasobów modułu równoważenia obciążenia w regionach platformy Azure.

- Równoważ obciążenie protokołów TCP i UDP na wszystkich portach jednocześnie przy użyciu **[portów ha](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)** .

## <a name="pricing"></a>Cennik

Usługa Load Balancer w warstwie Standardowa jest płatna.

* Liczba skonfigurowanych reguł równoważenia obciążenia i ruchu wychodzącego. Reguły NAT dla ruchu przychodzącego nie są liczone w łącznej liczbie reguł.
* Ilość danych przetworzonych w sposób przychodzący i wychodzący niezależnie od reguł.

Aby uzyskać informacje o cenach usługa Load Balancer w warstwie Standardowa, zobacz [Cennik usługi Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

Za użycie modułu równoważenia obciążenia w warstwie Podstawowa nie są naliczane opłaty.

## <a name="sla"></a>Umowa SLA

Aby uzyskać informacje na temat umowy SLA usługa Load Balancer w warstwie Standardowa, zobacz [Umowa SLA dla Load Balancer](https://aka.ms/lbsla).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z Load Balancer, zobacz [tworzenie usługa Load Balancer w warstwie Standardowa publicznego](quickstart-load-balancer-standard-public-portal.md) .

Aby uzyskać więcej informacji o Azure Load Balancer ograniczeniach i składnikach, zobacz [Azure Load Balancer pojęć i ograniczeń](./concepts-limitations.md)
