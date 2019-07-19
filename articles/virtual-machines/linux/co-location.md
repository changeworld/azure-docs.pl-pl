---
title: Wspólne lokalizowanie maszyn wirtualnych platformy Azure dla systemu Linux | Microsoft Docs
description: Dowiedz się więcej na temat sposobu lokalizowania zasobów maszyn wirtualnych platformy Azure w celu zwiększenia opóźnienia.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: cynthn
ms.openlocfilehash: d6d8986117bd2899ea0de0aa6490954aef6c3a56
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850342"
---
# <a name="co-locate-resources-for-improved-latency"></a>Wspólne Lokalizowanie zasobów w celu uzyskania lepszych opóźnień

Podczas wdrażania aplikacji na platformie Azure rozproszenie wystąpień między regionami lub strefami dostępności tworzy opóźnienie sieci, co może mieć wpływ na ogólną wydajność aplikacji. 

## <a name="preview-proximity-placement-groups"></a>Wersja zapoznawcza: Grupy umieszczania zbliżeniowe

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Kolejne kroki

Wdróż maszynę wirtualną w [grupie umieszczania sąsiedztwa](proximity-placement-groups.md) przy użyciu interfejsu wiersza polecenia platformy Azure.

