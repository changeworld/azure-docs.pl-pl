---
title: Lokalizowanie maszyn wirtualnych z systemem Windows Azure
description: Dowiedz się więcej na temat sposobu lokalizowania zasobów maszyn wirtualnych platformy Azure w celu zwiększenia opóźnienia.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: b61a84f2b284177b7443be00efeea65e7eb8aaef
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266795"
---
# <a name="co-locate-resource-for-improved-latency"></a>Wspólne lokalizowanie zasobu w celu uzyskania lepszych opóźnień

Podczas wdrażania aplikacji na platformie Azure rozproszenie wystąpień między regionami lub strefami dostępności tworzy opóźnienie sieci, co może mieć wpływ na ogólną wydajność aplikacji. 


## <a name="proximity-placement-groups"></a>Grupy umieszczania w pobliżu 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Następne kroki

Wdróż maszynę wirtualną w [grupie położenia zbliżeniowe](proximity-placement-groups.md) przy użyciu Azure PowerShell.

Dowiedz się, jak [testować opóźnienia sieci](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Dowiedz się, jak [zoptymalizować przepływność sieci](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Dowiedz się, jak [używać grup umieszczania zbliżeniowe z aplikacjami SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).