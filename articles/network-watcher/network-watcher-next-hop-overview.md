---
title: Wprowadzenie do następnego przeskoku w usłudze Azure Network Watcher | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie funkcji następnego przeskoku obserwatora sieci.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 33c22b5b68b6677e8cf271dc185007316ec44500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844062"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Użyj następnego przeskoku do diagnozowania problemów z routingiem maszyn wirtualnych

Ruch z maszyny wirtualnej jest wysyłany do miejsca docelowego na podstawie skutecznych tras skojarzonych z interfejsem sieciowym (NIC). Następny przeskok pobiera następny typ przeskoku i adres IP pakietu z określonej maszyny Wirtualnej i karty sieciowej. Znajomość następnego przeskoku pomaga określić, czy ruch jest kierowany do zamierzonego miejsca docelowego lub czy ruch jest wysyłany donikąd. Nieprawidłowa konfiguracja tras, w której ruch jest kierowany do lokalizacji lokalnej lub urządzenia wirtualnego, może prowadzić do problemów z łącznością. Następny przeskok zwraca również tabelę marszruty skojarzoną z następnym przeskokiem. Jeśli trasa jest zdefiniowana jako trasa zdefiniowana przez użytkownika, ta trasa jest zwracana. W przeciwnym razie następny przeskok zwraca **trasę systemową**.

![następny przegląd przeskoku](./media/network-watcher-next-hop-overview/figure1.png)

Następne przeskoki, które mogą być zwracane przez następnego przeskoku są następujące:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* Program MicrosoftEdge
* Brak

Aby dowiedzieć się więcej o każdym następnym typie przeskoku, zobacz [Omówienie routingu](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak używać następnego przeskoku do diagnozowania problemów z routingiem sieci maszyn wirtualnych, zobacz Diagnozowanie problemów z routingiem sieci maszyn wirtualnych przy użyciu [portalu Azure,](diagnose-vm-network-routing-problem.md) [programu PowerShell](diagnose-vm-network-routing-problem-powershell.md)lub [interfejsu wiersza polecenia platformy Azure.](diagnose-vm-network-routing-problem-cli.md)
