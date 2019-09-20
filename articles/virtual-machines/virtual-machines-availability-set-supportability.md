---
title: Obsługa dodawania maszyn wirtualnych platformy Azure do istniejącego zestawu dostępności | Microsoft Docs
description: Obsługa dodawania maszyn wirtualnych platformy Azure do istniejącego zestawu dostępności.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 3954df389516aa7199022d713dc63d62dda961ae
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155459"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Obsługa dodawania maszyn wirtualnych platformy Azure do istniejącego zestawu dostępności

Czasami mogą wystąpić ograniczenia podczas dodawania nowych maszyn wirtualnych do istniejącego zestawu dostępności. Poniższy wykres zawiera szczegółowe informacje o serii maszyn wirtualnych, które można mieszać w tym samym zestawie dostępności.

Oto macierz obsługi do mieszania różnych typów maszyn wirtualnych:

Zestaw dostępności & serii|Druga maszyna wirtualna|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Pierwsza maszyna wirtualna|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Wszystkie inne serie nie mogą znajdować się w tym samym zestawie dostępności, ponieważ wymagają określonego sprzętu.

Nie można mieszać A8/A9 maszyny wirtualnej ze względu na wymaganie dedykowanej sieci zaplecza RDMA.
