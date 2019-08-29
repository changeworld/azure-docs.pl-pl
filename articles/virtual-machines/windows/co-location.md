---
title: Lokalizowanie maszyn wirtualnych z systemem Windows Azure | Microsoft Docs
description: Dowiedz się więcej na temat sposobu lokalizowania zasobów maszyn wirtualnych platformy Azure w celu zwiększenia opóźnienia.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: cynthn
ms.openlocfilehash: ddffcd1230048a0b1e47076270ac24a607d53a7e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103253"
---
# <a name="co-locate-resource-for-improved-latency"></a>Wspólne lokalizowanie zasobu w celu uzyskania lepszych opóźnień

Podczas wdrażania aplikacji na platformie Azure rozproszenie wystąpień między regionami lub strefami dostępności tworzy opóźnienie sieci, co może mieć wpływ na ogólną wydajność aplikacji. 


## <a name="preview-proximity-placement-groups"></a>Wersja zapoznawcza: Grupy umieszczania zbliżeniowe 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Następne kroki

Wdróż maszynę wirtualną w [grupie położenia zbliżeniowe](proximity-placement-groups.md) przy użyciu Azure PowerShell.

