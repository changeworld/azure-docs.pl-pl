---
title: Metryki i alerty dotyczące usługi Azure Virtual Network translator adresów sieciowych
titleSuffix: Azure Virtual Network
description: Informacje dotyczące Azure Monitor metryk i alertów dostępnych dla Virtual Network translatora adresów sieciowych.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/03/2020
ms.author: allensu
ms.openlocfilehash: 6bffa114b325e8b98f439e7ff2ce21480407a643
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274914"
---
# <a name="azure-virtual-network-nat-metrics"></a>Metryki translatora adresów sieciowych na platformie Azure Virtual Network

Zasoby bramy translatora adresów sieciowych w usłudze Azure Virtual Network zapewniają wielowymiarowe metryki. Te metryki służą do obserwowania operacji i [rozwiązywania problemów](nat-metrics.md).  Alerty można skonfigurować pod kątem krytycznych problemów, takich jak wyczerpanie adresów.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtual Network translator adresów sieciowych dla ruchu wychodzącego do Internetu">
</p>

*Rysunek: Virtual Network translator adresów sieciowych dla ruchu wychodzącego do Internetu*

>[!NOTE] 
>Virtual Network translator adresów sieciowych jest w tej chwili dostępny jako publiczna wersja zapoznawcza. Jest ono obecnie dostępne tylko w ograniczonym zestawie [regionów](nat-overview.md#region-availability). Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="metrics"></a>Metryki

Zasoby bramy NAT zapewniają następujące wielowymiarowe metryki w Azure Monitor:

| Metryka | Opis | Zalecana agregacja | Wymiary |
|---|---|---|---|
| Bajty | Przetworzone bajty przychodzące i wychodzące | Suma | Kierunek (w; Out), protokół (6 TCP; 17 UDP) |
| SPI | Pakiety przetworzone do ruchu przychodzącego i wychodzącego | Suma | Kierunek (w; Out), protokół (6 TCP; 17 UDP) |
| Pakiety opuszczone | Pakiety odrzucone przez bramę translatora adresów sieciowych | Suma | / |
| Liczba połączeń z translatorem adresów sieciowych | Przejścia stanu na interwał | Suma | Stan połączenia, protokół (6 TCP; 17 połączeń UDP) |
| Łączna liczba połączeń z wiązaniami adresów sieciowych | Bieżące aktywne połączenia z przydziałami (~ porty w użyciu) | Suma | Protokół (6 TCP; 17 UDP) |


## <a name="alerts"></a>Alerty

Alerty dotyczące metryk można skonfigurować w Azure Monitor dla każdej z powyższych [metryk](#metrics).

## <a name="limitations"></a>Ograniczenia

Resource Health nie jest obsługiwana.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Virtual Network translatora adresów sieciowych](nat-overview.md)
- Informacje o [zasobie bramy translatora adresów sieciowych](nat-gateway-resource.md)
- Dowiedz się więcej o [Azure monitor](../azure-monitor/overview.md)
