---
title: Wprowadzenie do następnego przeskoku obserwatora sieciowego Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie obserwatora sieciowego następnego przeskoku możliwości.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: bbb782e700781dcfedbbd340c7d10db53767b035
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32180389"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Następny przeskok umożliwia diagnozowanie problemów z routingiem maszyny wirtualnej

Ruch z maszyny wirtualnej (VM) jest wysyłany do lokalizacji docelowej, na podstawie tras skuteczne skojarzone z interfejsu sieciowego (NIC). Następny przeskok pobiera typ następnego przeskoku i adres IP pakietu z określonej maszyny Wirtualnej i karty sieciowej. Znajomość następnego przeskoku ułatwia określenie, czy ruch jest być kierowany do przeznaczenia czy ruch jest wysyłany w żadnym punkcie. Niepoprawna konfiguracja tras, których ruch jest kierowany do lokalizacji lokalnej lub urządzenie wirtualne, może prowadzić do problemów z łącznością. Następny przeskok zwraca również wartość tabeli tras skojarzone z następnego przeskoku. Jeśli trasa jest zdefiniowana jako trasy zdefiniowane przez użytkownika, zwracany jest tej trasy. W przeciwnym razie wartość następnego przeskoku zwraca **trasa systemowa**.

![następnego przeskoku — omówienie](./media/network-watcher-next-hop-overview/figure1.png)

Dalej przeskoków, które mogą być zwrócone przez możliwości następnego przeskoku są następujące:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* VnetPeering
* Brak

Aby dowiedzieć się więcej na temat każdego Typ następnego przeskoku, zobacz [Omówienie routingu](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak używać następnego przeskoku do diagnozowania problemów routingu sieci maszyny Wirtualnej, zobacz [diagnozowania sieci maszyny Wirtualnej problemy z routingiem](diagnose-vm-network-routing-problem.md).
