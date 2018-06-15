---
title: Przykłady Azure CLI | Dokumentacja firmy Microsoft
description: Przykłady Azure CLI
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.openlocfilehash: 7977460f61bfdabd399e45e86d9bbf2e5083992b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864126"
---
# <a name="azure-cli-samples-for-networking"></a>Przykładów dla interfejsu wiersza polecenia platformy Azure do obsługi sieci

Poniższa tabela zawiera linki do bash skrypty utworzone przy użyciu wiersza polecenia platformy Azure.

| | |
|-|-|
|**Łączność między zasobami Azure**||
| [Tworzenie sieci wirtualnej dla aplikacji wielowarstwowych](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieci frontonu i zaplecza. Ruch do podsieci frontonu jest ograniczona do HTTP i SSH, gdy ruch do podsieci wewnętrznej jest ograniczony do MySQL, port 3306. |
| [Dwie wirtualne sieci równorzędne](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy i łączy dwie sieci wirtualne w tym samym regionie. |
| [Kierować ruchem przez urządzenie wirtualne sieci](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieci frontonu i zaplecza i maszynę Wirtualną, która może kierować ruchem między dwiema podsieciami. |
| [Filtrowanie ruchu przychodzącego i wychodzącego ruchu sieciowego maszyny Wirtualnej](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieci frontonu i zaplecza. Ruch sieciowy przychodzący do podsieci frontonu jest ograniczone do protokołu HTTP, HTTPS i SSH. Ruch wychodzący do Internetu z podsieci wewnętrznej jest niedozwolone. |
|**Kierunek ruchu i równoważenia obciążenia**||
| [Ruch równoważenia obciążenia do maszyn wirtualnych wysokiej dostępności](./scripts/load-balancer-linux-cli-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy kilka maszyny wirtualne o wysokiej dostępności i konfiguracji równoważenia obciążenia. |
| [Wiele witryn sieci Web na maszynach wirtualnych Równoważenie obciążenia](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy dwie maszyny wirtualne z wielu konfiguracji adresów IP, dołączony do platformy Azure zestawu dostępności, dostępny za pośrednictwem usługi równoważenia obciążenia Azure. |
| [Bezpośrednie ruchu w różnych regionach aplikacji wysokiej dostępności](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Tworzy dwa planów usługi aplikacji, dwie aplikacje sieci web profilu Menedżera ruchu i dwa punkty końcowe Menedżera ruchu. |
| | |
