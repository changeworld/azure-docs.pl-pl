---
title: Przykłady dla programu Azure PowerShell | Microsoft Docs
description: Przykłady dla programu Azure PowerShell
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: georgewallace
ms.openlocfilehash: 5bd0b4e33b5bef4d293e0475880692c72bf1504c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60564675"
---
# <a name="azure-powershell-samples-for-networking"></a>Przykłady programu Azure PowerShell dla sieci

Poniższa tabela zawiera linki do skryptów utworzonych przy użyciu programu Azure PowerShell.

| | |
|-|-|
|**Łączność między zasobami platformy Azure**||
| [Tworzenie sieci wirtualnej dla aplikacji wielowarstwowych](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch do podsieci frontonu jest ograniczony do protokołu HTTP, a ruch do podsieci zaplecza jest ograniczony do portu 1433 środowiska SQL. |
| [Komunikacja równorzędna dwóch sieci wirtualnych](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy i łączy dwie sieci wirtualne w tym samym regionie. |
| [Kierowanie ruchu przez wirtualne urządzenie sieciowe](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza oraz maszyną wirtualną, która może kierować ruchem między dwiema podsieciami. |
| [Filtrowanie przychodzącego i wychodzącego ruchu sieciowego maszyny wirtualnej](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch sieciowy przychodzący do podsieci frontonu jest ograniczony do protokołów HTTP i HTTPS... Ruch wychodzący do Internetu z podsieci zaplecza jest niedozwolone. |
|**Kierunek ruchu i równoważenia obciążenia**||
| [Równoważenie obciążenia ruchem do maszyn wirtualnych wysokiej dostępności](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy kilka maszyn wirtualnych na platformie o wysokiej dostępności i konfiguracji ze zrównoważonym obciążeniem. |
| [Równoważ obciążenie powodowane przez wielu witryn sieci Web na maszynach wirtualnych](./scripts/load-balancer-windows-powershell-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy dwie maszyny wirtualne z wieloma konfiguracjami IP, dołączone do zestawu dostępności platformy Azure, za pośrednictwem usługi Azure Load Balancer. |
| [Bezpośrednie kierowanie ruchu w wielu regionach dla aplikacji o wysokiej dostępności](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Tworzy dwa plany usługi app service, dwie aplikacje internetowe, profilu usługi traffic manager i dwa punkty końcowe Menedżer ruchu. |
| | |
