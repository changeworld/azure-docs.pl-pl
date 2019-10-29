---
title: Omówienie dzienników zasobów platformy Azure | Microsoft Docs
description: Poznaj obsługiwane usługi i schemat zdarzeń dla dzienników zasobów platformy Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/22/2019
ms.openlocfilehash: b953f9b5e5fd8c853746caad3047986786bd1317
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989621"
---
# <a name="azure-resource-logs-overview"></a>Omówienie dzienników zasobów platformy Azure
Dzienniki zasobów platformy Azure [są](platform-logs-overview.md) wyemitowane przez zasoby platformy Azure, które opisują ich operacje wewnętrzne. Wszystkie dzienniki zasobów udostępniają wspólny schemat najwyższego poziomu z elastycznością dla każdej usługi do emisji unikatowych właściwości dla własnych zdarzeń.

> [!NOTE]
> Dzienniki zasobów były wcześniej znane jako dzienniki diagnostyczne.

## <a name="collecting-resource-logs"></a>Zbieranie dzienników zasobów
Dzienniki zasobów są generowane automatycznie przez obsługiwane zasoby platformy Azure, ale nie są zbierane, chyba że zostaną skonfigurowane przy użyciu [Ustawienia diagnostycznego](diagnostic-settings.md). Utwórz ustawienia diagnostyczne dla każdego zasobu platformy Azure, aby przekazywać dzienniki do następujących miejsc docelowych:

| Cel | Scenariusz |
|:---|:---|:---|
| [Obszar roboczy usługi Log Analytics](resource-logs-collect-workspace.md) | Analizuj dzienniki z innymi danymi monitorowania i korzystaj z funkcji Azure Monitor, takich jak zapytania dzienników i alerty dzienników. |
| [Magazyn platformy Azure](resource-logs-collect-storage.md) | Archiwizuj dzienniki na potrzeby inspekcji lub tworzenia kopii zapasowych. |
| [Centrum zdarzeń](resource-logs-stream-event-hubs.md) | Przesyłanie strumieniowe dzienników do systemów rejestrowania i telemetrii innych firm.  |

## <a name="compute-resources"></a>Zasoby obliczeniowe
Dzienniki zasobów różnią się od dzienników na poziomie systemu operacyjnego gościa w zasobach obliczeniowych platformy Azure. Zasoby obliczeniowe wymagają od agenta zebrania dzienników i metryk z systemu operacyjnego gościa, w tym takich danych jak dzienniki zdarzeń, dziennik systemowy i liczniki wydajności. Użyj [rozszerzenia diagnostyki](agents-overview.md#azure-diagnostic-extension) , aby skierować dane dziennika z maszyn wirtualnych platformy Azure i [agenta log Analytics](agents-overview.md#log-analytics-agent) do zbierania dzienników i metryk z maszyn wirtualnych na platformie Azure, w innych chmurach i lokalnych w obszarze roboczym log Analytics. Aby uzyskać szczegółowe informacje, zobacz [źródła danych monitorowania dla Azure monitor](data-sources.md) .

## <a name="resource-logs-schema"></a>Schemat dzienników zasobów
Aby uzyskać więcej informacji na temat schematu i kategorii dzienników zasobów, zobacz [schemat dziennika zasobów](diagnostic-logs-schema.md). 

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat innych dzienników platformy Azure](platform-logs-overview.md) , których możesz użyć do monitorowania platformy Azure.
