---
title: Dzienniki i metryki na platformie Azure | Microsoft Docs
description: Omówienie dzienników diagnostycznych na platformie Azure, które zapewniają rozbudowane, częste dane dotyczące operacji zasobu platformy Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 3fe220506e074f881a16c1805d25fb4b39927488
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262364"
---
# <a name="logs-and-metrics-in-azure"></a>Dzienniki i metryki na platformie Azure
Istnieją różne [dzienniki](data-platform-logs.md) i [metryki](data-platform-metrics.md)

Monitorowanie aplikacji i usług na platformie Azure może być rozdzielone na [platformy danych platformy Azure](data-platform.md). 

Dzienniki i metryki można podzielić na dwie kategorie

- Platforma — dzienniki i metryki, które są generowane automatycznie bez żadnej konfiguracji wymaganej od samego 



| Warstwa | Dzienniki platformy | Metryki platformy | Dzienniki niestandardowe | Metryki niestandardowe |
|:---|:---|:---|:---|:---|
| Aplikacja  | | | | |
| System operacyjny gościa     | Puls |  | Rozszerzenie diagnostyki<br>Agent Log Analytics | Rozszerzenie diagnostyki |
| Resource     | [Dzienniki zasobów](resource-logs-overview.md)<br>(specyficzne dla każdej usługi) | [Metryki zasobów](metrics-supported.md)<br>(specyficzne dla każdej usługi) | | [Metryki niestandardowe](metrics-custom-overview.md) |
| Subscription | [Dziennik aktywności](activity-logs-overview.md) | | | |
| Dzierżawa       | 

## <a name="next-steps"></a>Następne kroki

* [Stream dzienników diagnostycznych zasobów do **usługi Event Hubs**](resource-logs-stream-event-hubs.md)
* [Zmień ustawienia diagnostyczne zasobów przy użyciu interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/)
* [Analizowanie dzienników z usługi Azure Storage za pomocą Azure Monitor](collect-azure-metrics-logs.md)
