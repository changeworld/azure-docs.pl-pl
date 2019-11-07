---
title: Statystyka opóźnienia w sieci platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat statystyk opóźnienia rundy w regionach platformy Azure.
services: networking
documentationcenter: na
author: nayak-mahesh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/04/2019
ms.author: mnayak
ms.openlocfilehash: 500676983233f943fdc9638d75758645dee65564
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587591"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statystyka opóźnienia w sieci platformy Azure

Platforma Azure nieustannie monitoruje opóźnienia (szybkość) podstawowych obszarów swojej sieci przy użyciu wewnętrznych narzędzi do monitorowania oraz pomiarów zebranych przez [ThousandEyes](https://thousandeyes.com), a także do innych firmowych usług monitorowania syntetycznego.

## <a name="how-are-the-measurements-collected"></a>Jak zbierane są pomiary?

Pomiary opóźnienia są zbierane od agentów ThousandEyes hostowanych na całym świecie w regionach chmury platformy Azure, które w sposób ciągły wysyłają sondy sieci między sobą, w odstępach 1-minutowych. Statystyki miesięczne opóźnienia pochodzą od średniej zebranych próbek przez miesiąc.

## <a name="october-2019-latency-figures"></a>Cyfry opóźnienia 2019 października

Dla 31 dni od 31 października 2019, miesięczny minimalny i maksymalny czas opóźnienia błądzenia w zagregowanych regionach to:

- od **5 ms** do **72 MS** dla rundy trips w regionach **Ameryka Północna** .
- **3 ms** do **28 MS** dla rundy w regionach **Europy** .
- **4 MS** do **134 MS** dla rundy TRIPS w regionach **Azji** .

Poniższe pomiary opóźnienia między regionami są obsługiwane przez [ThousandEyes](https://thousandeyes.com). Jednostka miary w poniższej tabeli jest w milisekundach (MS).

![Statystyka opóźnień między regionami platformy Azure](media/azure-network-latency/azure-inter-region-latency.png)


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [regionach platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).