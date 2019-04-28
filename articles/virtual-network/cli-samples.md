---
title: Przykłady interfejsu wiersza polecenia platformy Azure dla sieci wirtualnej | Microsoft Docs
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
ms.date: 04/17/2019
ms.author: kumud
ms.openlocfilehash: de96f101778b7011a5d3c67905b24908df7a7362
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098460"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla sieci wirtualnej

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą poleceń interfejsu wiersza polecenia platformy Azure:

| | |
|----|----|
| [Tworzenie sieci wirtualnej dla aplikacji wielowarstwowych](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch do podsieci frontonu jest ograniczony do protokołów HTTP i SSH, a ruch do podsieci zaplecza jest ograniczony do portu 3306 środowiska MySQL. |
| [Komunikacja równorzędna dwóch sieci wirtualnych](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Tworzy i łączy dwie sieci wirtualne w tym samym regionie. |
| [Kierowanie ruchu przez wirtualne urządzenie sieciowe](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza oraz maszyną wirtualną, która może kierować ruchem między dwiema podsieciami. |
| [Filtrowanie przychodzącego i wychodzącego ruchu sieciowego maszyny wirtualnej](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch sieciowy przychodzący do podsieci frontonu jest ograniczony do protokołów HTTP, HTTPS i SSH. Ruch wychodzący do Internetu z podsieci zaplecza jest niedozwolony. |
|[Konfigurowanie protokołu IPv4 + IPv6 podwójnego stosu sieci wirtualnej](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Wdraża obsługującej podwójny stos (IPv4 + IPv6) sieci wirtualnej z dwiema maszynami wirtualnymi i podstawowa usługi Azure Load Balancer z publicznych adresów IP protokołu IPv4 i IPv6. |