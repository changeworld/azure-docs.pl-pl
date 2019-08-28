---
title: Wspólne lokalizowanie maszyn wirtualnych platformy Azure dla systemu Linux | Microsoft Docs
description: Dowiedz się więcej na temat sposobu lokalizowania zasobów maszyn wirtualnych platformy Azure w celu zwiększenia opóźnienia.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: cynthn
ms.openlocfilehash: 81b9dc5059a6ab3e8245acd9c7e7ef8be5abdafd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083865"
---
# <a name="co-locate-resources-for-improved-latency"></a>Wspólne Lokalizowanie zasobów w celu uzyskania lepszych opóźnień

Podczas wdrażania aplikacji na platformie Azure rozproszenie wystąpień między regionami lub strefami dostępności tworzy opóźnienie sieci, co może mieć wpływ na ogólną wydajność aplikacji. 

## <a name="preview-proximity-placement-groups"></a>Wersja zapoznawcza: Grupy umieszczania zbliżeniowe

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Następne kroki

Wdróż maszynę wirtualną w [grupie umieszczania sąsiedztwa](proximity-placement-groups.md) przy użyciu interfejsu wiersza polecenia platformy Azure.

