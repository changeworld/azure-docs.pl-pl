---
title: Przykłady interfejsu wiersza polecenia platformy Azure dla sieci wirtualnej
description: Przykłady interfejsu wiersza polecenia platformy Azure dla sieci wirtualnej.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 5ce10cf37b61b0269e6c8f2279b8814d9dc4a4f9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78271208"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla sieci wirtualnej

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą poleceń interfejsu wiersza polecenia platformy Azure:

| | |
|----|----|
| [Tworzenie sieci wirtualnej dla aplikacji wielowarstwowych](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch do podsieci frontonu jest ograniczony do protokołów HTTP i SSH, a ruch do podsieci zaplecza jest ograniczony do portu 3306 środowiska MySQL. |
| [Komunikacja równorzędna dwóch sieci wirtualnych](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Tworzy i łączy dwie sieci wirtualne w tym samym regionie. |
| [Kierowanie ruchu przez wirtualne urządzenie sieciowe](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza oraz maszyną wirtualną, która może kierować ruchem między dwiema podsieciami. |
| [Filtrowanie przychodzącego i wychodzącego ruchu sieciowego maszyny wirtualnej](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch sieciowy przychodzący do podsieci frontonu jest ograniczony do protokołów HTTP, HTTPS i SSH. Ruch wychodzący do Internetu z podsieci zaplecza jest niedozwolony. |
|[Konfigurowanie sieci wirtualnej IPv4 + IPv6 z podstawowym modułem równoważenia obciążenia](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Wdraża sieć wirtualną z dwoma stosami (IPv4+IPv6) z dwiema maszynami wirtualnymi i podstawowym modułem równoważenia obciążenia platformy Azure z publicznymi adresami IP IPv4 i IPv6. |
|[Konfigurowanie sieci wirtualnej IPv4 + IPv6 z podwójnym stosem za pomocą standardowego modułu równoważenia obciążenia](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Wdraża sieć wirtualną z dwoma stosami (IPv4+IPv6) z dwiema maszynami wirtualnymi i modułem równoważenia obciążenia standardowego platformy Azure z publicznymi adresami IP IPv4 i IPv6. |
|[Samouczek: Tworzenie i testowanie bramy NAT — azure cli](../virtual-network/tutorial-create-validate-nat-gateway-cli.md)|Tworzenie i sprawdzanie poprawności bramy TRANSLATORA przy użyciu źródłowej i docelowej maszyny wirtualnej. |