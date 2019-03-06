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
ms.openlocfilehash: 7a5e97b66fec040b4ec32caa8d58cf9b50169a33
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433148"
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

Rozmiar maszyny Wirtualnej a8/A9 nie mogą być mieszane ze względu na wymagania na dedykowana sieć zaplecza RDMA.
