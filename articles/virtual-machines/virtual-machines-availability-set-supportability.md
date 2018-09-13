---
title: Możliwość obsługi dodawania maszyny wirtualne platformy Azure do istniejącego dostępności zestawu | Dokumentacja firmy Microsoft
description: Możliwość obsługi dodawania maszyny wirtualne platformy Azure do istniejącego zestawu dostępności.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 13448f4b335d84264d4141cb4fb8c3eadcf0303e
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35941051"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Możliwość obsługi dodawania maszyny wirtualne platformy Azure do istniejącego zestawu dostępności

Można czasami napotkać ograniczenia podczas dodawania nowych maszyn wirtualnych (VM) do istniejącego zestawu dostępności. Poniższej tabeli przedstawiono serię maszyn wirtualnych, które można łączyć w tym samym zestawie dostępności.

Oto macierzy obsługi, aby łączyć różne rodzaje maszyn wirtualnych:

Serie i zestaw dostępności|Drugą maszynę Wirtualną|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Pierwsza maszyna wirtualna|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Innych serii nie można w tym samym dostępności, ponieważ wymagają one określonego sprzętu.

Nie można łączyć rozmiar maszyn wirtualnych a8/A9 z powodu wymogu odnoszącego się na dedykowana sieć zaplecza RDMA.
