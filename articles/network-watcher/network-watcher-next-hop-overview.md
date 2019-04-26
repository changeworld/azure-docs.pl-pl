---
title: Wprowadzenie do następnego przeskoku w usłudze Azure Network Watcher | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie usługi Network Watcher następnego przeskoku możliwości.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 40f1790806aac6756979f28ad29442b7abf96019
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532298"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Użyj następny przeskok do diagnozowania problemów z routingiem maszyny wirtualnej

Ruch z maszyny wirtualnej (VM) jest wysyłany do miejsca docelowego na podstawie skutecznych tras skojarzone z interfejsu sieciowego (NIC). Następny przeskok pobiera typ następnego skoku i adres IP pakietu z konkretnej maszyny Wirtualnej i karty sieciowej. Znajomość następnego przeskoku zawarto informacje ułatwiające określenie, czy jest być kierowany ruch do miejsca docelowego czy ruch jest przesyłany w żadnym punkcie. Niepoprawna konfiguracja tras, w którym ruch będzie kierowany do lokalizacji lokalnej lub urządzenie wirtualne, może prowadzić do problemów z łącznością. Następny przeskok zwraca również wartość tabeli tras skojarzonej z następnego przeskoku. Jeśli trasa jest zdefiniowana jako trasy zdefiniowane przez użytkownika, zwracana jest tej trasy. W przeciwnym razie następnego przeskoku zwraca **trasa systemowa**.

![Omówienie dla następnego przeskoku](./media/network-watcher-next-hop-overview/figure1.png)

Kolejnymi przeskokami, które mogą być zwrócone przez następnego przeskoku możliwości są następujące:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* Brak

Aby dowiedzieć się więcej na temat każdego typu następnego przeskoku, zobacz [Omówienie routingu](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak używać miejsca docelowego następnego skoku do diagnozowania problemów z routingiem maszyny Wirtualnej sieci, zobacz diagnozowanie problemów z routingiem przy użyciu sieci maszyny Wirtualnej [witryny Azure portal](diagnose-vm-network-routing-problem.md), [PowerShell](diagnose-vm-network-routing-problem-powershell.md), lub [wiersza polecenia platformy Azure](diagnose-vm-network-routing-problem-cli.md).
