---
title: Współlokuj maszyny wirtualne z systemem Linux
description: Dowiedz się, jak współlokowanie zasobów maszyny Wirtualnej platformy Azure może zwiększyć opóźnienia.
ms.service: virtual-machines
ms.topic: article
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: d2fd8a2cd7dac7b1d3c78691c84a861d924005ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250285"
---
# <a name="co-locate-resources-for-improved-latency"></a>Kololokuj zasoby w celu zwiększenia opóźnień

Podczas wdrażania aplikacji na platformie Azure rozmieszczanie wystąpień w różnych regionach lub strefach dostępności tworzy opóźnienie sieci, co może mieć wpływ na ogólną wydajność aplikacji. 

## <a name="proximity-placement-groups"></a>Grupy umieszczania w pobliżu

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Następne kroki

Wdrażanie maszyny Wirtualnej do [grupy miejsc docelowych zbliżeniowych](proximity-placement-groups.md) przy użyciu interfejsu wiersza polecenia platformy Azure.

Dowiedz się, jak [przetestować opóźnienie sieci](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Dowiedz się, jak [zoptymalizować przepustowość sieci.](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

Dowiedz się, jak [korzystać z grup miejsc docelowych zbliżeniowych w aplikacjach SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
