---
title: Statystyki opóźnienia podróży w sieci platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o statystykach opóźnienia podróży w obie strony między regionami platformy Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: d9cae04499f046749e504bcab89b893fcc31a81c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886957"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statystyki opóźnienia podróży w sieci platformy Azure

Platforma Azure stale monitoruje opóźnienia (szybkość) podstawowych obszarów sieci przy użyciu wewnętrznych narzędzi monitorowania, a także pomiarów zebranych przez [ThousandEyes](https://thousandeyes.com), usługę monitorowania syntetycznego innej firmy.

## <a name="how-are-the-measurements-collected"></a>W jaki sposób są zbierane pomiary?

Pomiary opóźnienia są zbierane od agentów ThousandEyes, hostowanych w regionach chmury platformy Azure na całym świecie, które stale wysyłają sondy sieciowe między sobą w odstępach 1-minutowych. Miesięczne statystyki opóźnienia są uzyskiwane z uśredniania pobranych próbek dla miesiąca.

## <a name="march-2020-round-trip-latency-figures"></a>Dane dotyczące opóźnień w obie strony w marcu 2020 r.

Poniżej przedstawiono średni miesięczny czas podróży w obie strony między regionami platformy Azure w ciągu ostatnich 31 dni (kończący się 31 marca 2020 r.). Poniższe pomiary są obsługiwane przez [ThousandEyes](https://thousandeyes.com).

[![Statystyki opóźnienia między regionami platformy Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [regionach platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).
