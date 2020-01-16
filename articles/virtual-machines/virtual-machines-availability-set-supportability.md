---
title: Obsługa dodawania maszyn wirtualnych platformy Azure do istniejącego zestawu dostępności | Microsoft Docs
description: Ten artykuł zawiera matrycę z możliwością obsługi, którą serie maszyn wirtualnych można mieszać w tym samym zestawie dostępności
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
ms.openlocfilehash: cb0034f2b353284e94d6f1508541b31040a5b076
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028406"
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
