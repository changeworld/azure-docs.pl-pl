---
title: Przykłady programu Azure PowerShell dla sieci wirtualnej | Microsoft Docs
description: Przykłady programu Azure PowerShell dla sieci wirtualnej.
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
ms.openlocfilehash: 418a72eb9a8f98bca24c863b2f953bfe720979e6
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249043"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Przykłady programu Azure PowerShell dla sieci wirtualnej

Poniższa tabela zawiera linki do skryptów programu Azure PowerShell:

| | |
|----|----|
| [Tworzenie sieci wirtualnej dla aplikacji wielowarstwowych](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch do podsieci frontonu jest ograniczony do protokołu HTTP, a ruch do podsieci zaplecza jest ograniczony do portu 1433 środowiska SQL. |
| [Komunikacja równorzędna dwóch sieci wirtualnych](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Tworzy i łączy dwie sieci wirtualne w tym samym regionie. |
| [Kierowanie ruchu przez wirtualne urządzenie sieciowe](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza oraz maszyną wirtualną, która może kierować ruchem między dwiema podsieciami. |
| [Filtrowanie przychodzącego i wychodzącego ruchu sieciowego maszyny wirtualnej](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch sieciowy przychodzący do podsieci frontonu jest ograniczony do protokołów HTTP i HTTPS. Ruch wychodzący do Internetu z podsieci zaplecza jest niedozwolony. |
|[Konfigurowanie protokołu IPv4 + IPv6 podwójnego stosu sieci wirtualnej przy użyciu podstawowego modułu równoważenia obciążenia](./scripts/virtual-network-powershell-sample-ipv6-dual-stack.md)|Wdraża obsługującej podwójny stos (IPv4 + IPv6) sieci wirtualnej z dwiema maszynami wirtualnymi i podstawowa usługi Azure Load Balancer z publicznych adresów IP protokołu IPv4 i IPv6. |
|[Konfigurowanie protokołu IPv4 + IPv6 podwójnego stosu sieci wirtualnej przy użyciu standardowego modułu równoważenia obciążenia](./scripts/virtual-network-powershell-sample-ipv6-dual-stack-standard-load-balancer.md)|Wdraża obsługującej podwójny stos (IPv4 + IPv6) sieci wirtualnej z dwiema maszynami wirtualnymi i standardowa usługi Azure Load Balancer z publicznych adresów IP protokołu IPv4 i IPv6. |
