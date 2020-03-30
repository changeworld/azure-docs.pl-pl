---
title: Współlokowanie maszyn wirtualnych systemu Windows Azure
description: Dowiedz się, jak współlokowanie zasobów maszyny Wirtualnej platformy Azure może zwiększyć opóźnienia.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266795"
---
# <a name="co-locate-resource-for-improved-latency"></a>Kololokuj zasób w celu zwiększenia opóźnienia

Podczas wdrażania aplikacji na platformie Azure rozmieszczanie wystąpień w różnych regionach lub strefach dostępności tworzy opóźnienie sieci, co może mieć wpływ na ogólną wydajność aplikacji. 


## <a name="proximity-placement-groups"></a>Grupy umieszczania w pobliżu 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Następne kroki

Wdrażanie maszyny Wirtualnej do [grupy miejsc docelowych zbliżeniowych](proximity-placement-groups.md) przy użyciu programu Azure PowerShell.

Dowiedz się, jak [przetestować opóźnienie sieci](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Dowiedz się, jak [zoptymalizować przepustowość sieci.](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)  

Dowiedz się, jak [korzystać z grup miejsc docelowych zbliżeniowych w aplikacjach SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)