---
title: Przykłady programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Przykłady programu Azure PowerShell
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
ms.openlocfilehash: 0bca4fb6874bd265f0ae9faeb4219abeb4ffb6d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23926354"
---
# <a name="azure-powershell-samples-for-networking"></a>Przykładów dla platformy Azure PowerShell do obsługi sieci

Poniższa tabela zawiera linki do skryptów utworzony przy użyciu programu Azure PowerShell.

| | |
|-|-|
|**Łączność między zasobami Azure**||
| [Tworzenie sieci wirtualnej dla aplikacji wielowarstwowych](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieci frontonu i zaplecza. Ruch do podsieci frontonu jest ograniczone do protokołu HTTP, gdy ruch do podsieci wewnętrznej jest ograniczony do bazy danych SQL, port 1433. |
| [Dwie wirtualne sieci równorzędne](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy i łączy dwie sieci wirtualne w tym samym regionie. |
| [Kierować ruchem przez urządzenie wirtualne sieci](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieci frontonu i zaplecza i maszynę Wirtualną, która może kierować ruchem między dwiema podsieciami. |
| [Filtrowanie ruchu przychodzącego i wychodzącego ruchu sieciowego maszyny Wirtualnej](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieci frontonu i zaplecza. Ruch sieciowy przychodzący do podsieci frontonu jest ograniczone do protokołu HTTP i HTTPS. Ruch wychodzący do Internetu z podsieci wewnętrznej jest niedozwolone. |
|**Kierunek ruchu i równoważenia obciążenia**||
| [Ruch równoważenia obciążenia do maszyn wirtualnych wysokiej dostępności](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy kilka maszyny wirtualne o wysokiej dostępności i konfiguracji równoważenia obciążenia. |
| [Wiele witryn sieci Web na maszynach wirtualnych Równoważenie obciążenia](./scripts/load-balancer-windows-powershell-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy dwie maszyny wirtualne z wielu konfiguracji adresów IP, dołączony do platformy Azure zestawu dostępności, dostępny za pośrednictwem usługi równoważenia obciążenia Azure. |
| [Bezpośrednie ruchu w różnych regionach aplikacji wysokiej dostępności](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Tworzy dwa planów usługi aplikacji, dwie aplikacje sieci web profilu Menedżera ruchu i dwa punkty końcowe Menedżera ruchu. |
| | |
