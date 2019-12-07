---
title: Statystyka opóźnienia w sieci platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat statystyk opóźnienia rundy w regionach platformy Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 12/04/2019
ms.author: mnayak
ms.openlocfilehash: 3947df81b67d5aefc1b628b6ddaf8275152a4cd3
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893093"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statystyka opóźnienia w sieci platformy Azure

Platforma Azure nieustannie monitoruje opóźnienia (szybkość) podstawowych obszarów swojej sieci przy użyciu wewnętrznych narzędzi do monitorowania oraz pomiarów zebranych przez [ThousandEyes](https://thousandeyes.com), a także do innych firmowych usług monitorowania syntetycznego.

## <a name="how-are-the-measurements-collected"></a>Jak zbierane są pomiary?

Pomiary opóźnienia są zbierane od agentów ThousandEyes, hostowanych w regionach chmury platformy Azure na całym świecie, które w sposób ciągły wysyłają sondy sieci między sobą w interwałach 1-minutowych. Statystyki miesięczne opóźnienia pochodzą od średniej zebranych próbek przez miesiąc.

## <a name="november-2019-latency-figures"></a>Dane dotyczące opóźnień z listopada 2019

**Aktualizacja z listopada:** Dodano 3 regiony.

* Norwegia Wschodnia
* Norwegia Zachodnia
* Australia

Poniżej przedstawiono średni miesięczny czas błądzenia między regionami platformy Azure w ciągu ostatnich 30 dni (kończący się na 30 listopada 2019). Następujące pomiary są obsługiwane przez [ThousandEyes](https://thousandeyes.com).

![Statystyka opóźnień między regionami platformy Azure](media/azure-network-latency/latency-nov-2019.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [regionach platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).
