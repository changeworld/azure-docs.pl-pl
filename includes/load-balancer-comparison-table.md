---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/21/2020
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 33e6fecafa2b9748a27794800365974ebc975d0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78202482"
---
| | Standardowy moduł równoważenia obciążenia | Podstawowy moduł równoważenia obciążenia |
| --- | --- | --- |
| [Rozmiar puli zaplecza](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Obsługuje do 1000 wystąpień. | Obsługuje do 300 wystąpień. |
| Punkty końcowe puli zaplecza | Wszystkie maszyny wirtualne lub zestawy skalowania maszyny wirtualnej w jednej sieci wirtualnej. | Maszyny wirtualne w jednym zestawie dostępności lub zestaw skalowania maszyn wirtualnych. |
| [Sondy kondycji](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Zachowanie sondy kondycji podczas niepowodzenia](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | Połączenia TCP pozostają aktywne na sondy wystąpienia w dół __i__ na wszystkich sond w dół. | Połączenia TCP pozostają aktywne podczas sondy wystąpienia w dół. Wszystkie połączenia TCP kończą się, gdy wszystkie sondy są w dół. |
| Strefy dostępności | Strefowo nadmiarowe i strefowe frontony dla ruchu przychodzącego i wychodzącego. | Niedostępne |
| Diagnostyka | [Metryki wielowymiarowe usługi Azure Monitor](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Dzienniki usługi Azure Monitor](../articles/load-balancer/load-balancer-monitor-log.md) |
| Porty wysokiej dostępności | [Dostępne dla wewnętrznego modułu równoważenia obciążenia](../articles/load-balancer/load-balancer-ha-ports-overview.md) | Niedostępne |
| Zabezpieczenie domyślne | Zamknięte dla przepływów przychodzących, chyba że zezwala na to sieć grupy zabezpieczeń. Należy pamiętać, że ruch wewnętrzny z sieci wirtualnej do wewnętrznego modułu równoważenia obciążenia jest dozwolony. | Otwórz domyślnie. Opcjonalna grupa zabezpieczeń sieciowych. |
| Reguły ruchu wychodzącego | [Deklaratywna wychodząca konfiguracja translatora osi dostępu do sieci](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Niedostępne |
| Resetowanie protokołu TCP w wyniku bezczynności | [Dostępne na dowolnej zasadzie](../articles/load-balancer/load-balancer-tcp-reset.md) | Niedostępne |
| [Wiele przednich końcówek](../articles/load-balancer/load-balancer-multivip-overview.md) | Ruch przychodzący i [wychodzący](../articles/load-balancer/load-balancer-outbound-connections.md) | Tylko ruch przychodzący |
| Operacje zarządzania | Większość operacji < 30 sekund | 60-90+ sekund typowo |
| Umowa SLA | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Niedostępne | 
