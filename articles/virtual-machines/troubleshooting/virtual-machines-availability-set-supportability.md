---
title: Możliwość obsługi dodawania maszyn wirtualnych platformy Azure do istniejącego zestawu dostępności | Dokumenty firmy Microsoft
description: Ten artykuł zawiera macierz obsługi, o której serii maszyn wirtualnych można mieszać w tym samym zestawie dostępności
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: a9ca8f219bef787de04b51600209bfd3a24dd166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122924"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Możliwość obsługi dodawania maszyn wirtualnych platformy Azure do istniejącego zestawu dostępności

Od czasu do czasu mogą wystąpić ograniczenia podczas dodawania nowych maszyn wirtualnych (maszyn wirtualnych) do istniejącego zestawu dostępności. Poniższe informacje na wykresie, które serii maszyn wirtualnych można mieszać w tym samym zestawie dostępności.

Oto macierz obsługi do mieszania różnych typów maszyn wirtualnych:

Zestaw dostępności & serii|Druga maszyna wirtualna|A|Okręg wyborczy Av2|D|Dv2 ( dv2 )|Dv3 ( dv3 )|
|---|---|---|---|---|---|---|
|Pierwsza maszyna wirtualna|||||||
|A||OK|OK|OK|OK|OK|
|Okręg wyborczy Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2 ( dv2 )||OK|OK|OK|OK|OK|
|Dv3 ( dv3 )||OK|OK|OK|OK|OK|

Wszystkie inne serie nie mogą być w tym samym zestawie dostępności, ponieważ wymagają określonego sprzętu.

Nie można mieszać rozmiaru maszyny Wirtualnej A8/A9 ze względu na wymagania dotyczące dedykowanej sieci zaplecza RDMA.
