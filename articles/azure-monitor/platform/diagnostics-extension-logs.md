---
title: Używanie magazynu obiektów blob dla usług IIS i magazynu tabel dla zdarzeń w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Usługa Azure Monitor może odczytywać dzienniki usług platformy Azure, które zapisują diagnostykę do magazynu tabel lub dzienników usług IIS zapisanych w magazynie obiektów blob.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 44368ab90abd189c6a8a0792494828c87142eb20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672399"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Zbieranie danych z rozszerzenia diagnostyki platformy Azure do dzienników usługi Azure Monitor
Rozszerzenie diagnostyki platformy Azure jest [agentem w usłudze Azure Monitor,](agents-overview.md) który zbiera dane monitorowania z systemu operacyjnego gościa zasobów obliczeniowych platformy Azure, w tym maszyn wirtualnych. W tym artykule opisano sposób zbierania danych zebranych przez rozszerzenie diagnostyki z usługi Azure Storage do dzienników usługi Azure Monitor.

> [!NOTE]
> Agent usługi Log Analytics w usłudze Azure Monitor jest zazwyczaj preferowaną metodą zbierania danych z systemu operacyjnego gościa do dzienników monitora platformy Azure. Zobacz [Omówienie agentów usługi Azure Monitor, aby](agents-overview.md) uzyskać szczegółowe porównanie agentów.

## <a name="supported-data-types"></a>Obsługiwane typy danych
Rozszerzenie diagnostyki platformy Azure przechowuje dane na koncie usługi Azure Storage. Aby dzienniki usługi Azure Monitor zbierać te dane, muszą znajdować się w następujących lokalizacjach:

| Typ dziennika | Typ zasobu | Lokalizacja |
| --- | --- | --- |
| Dzienniki usług IIS |Maszyny wirtualne <br> Role sieci Web <br> Role pracowników |plikami dziennika wad-iis (magazyn obiektów Blob) |
| Dziennik systemu |Maszyny wirtualne |LinuxsyslogVer2v0 (magazyn tabel) |
| Zdarzenia operacyjne sieci usług |Węzły sieci szkieletowej usług |WADServiceFabricSystemEventTable |
| Zdarzenia wiarygodnego aktora w sieci usług |Węzły sieci szkieletowej usług |WADServiceFabricReliableActorEventTable |
| Zdarzenia niezawodnej usługi w sieci usług |Węzły sieci szkieletowej usług |WADServiceFabricReliableServiceEventTable |
| Dzienniki zdarzeń systemu Windows |Węzły sieci szkieletowej usług <br> Maszyny wirtualne <br> Role sieci Web <br> Role pracowników |WADWindowsEventLogsTable (Magazyn tabel) |
| Dzienniki ETW systemu Windows |Węzły sieci szkieletowej usług <br> Maszyny wirtualne <br> Role sieci Web <br> Role pracowników |WADETWEventTable (magazyn tabel) |

## <a name="data-types-not-supported"></a>Typy danych nie są obsługiwane

- Dane dotyczące wydajności z systemu operacyjnego gościa
- Dzienniki usług IIS z witryn sieci Web platformy Azure


## <a name="enable-azure-diagnostics-extension"></a>Włącz rozszerzenie diagnostyki platformy Azure
Zobacz [Instalowanie i konfigurowanie rozszerzenia diagnostyki systemu Windows Azure (WAD)](diagnostics-extension-windows-install.md) lub [Użyj rozszerzenia diagnostycznego systemu Linux, aby monitorować metryki i dzienniki, aby](../../virtual-machines/extensions/diagnostics-linux.md) uzyskać szczegółowe informacje na temat instalowania i konfigurowania rozszerzenia diagnostyki. Pozwoli to określić konto magazynu i skonfigurować zbieranie danych, które chcesz przesłać dalej do dzienników usługi Azure Monitor.


## <a name="collect-logs-from-azure-storage"></a>Zbieranie dzienników z usługi Azure Storage
Poniższa procedura umożliwia zbieranie danych rozszerzenia diagnostyki z konta usługi Azure Storage:

1. W witrynie Azure portal przejdź do **obszarów roboczych usługi Log Analytics** i wybierz obszar roboczy.
1. Kliknij **pozycję Konta magazynu loguje** się w sekcji Źródła danych obszaru **roboczego** w menu.
2. Kliknij **pozycję Dodaj**.
3. Wybierz **konto magazyn,** które zawiera dane do zebrania.
4. Wybierz **typ danych,** który chcesz zebrać.
5. Wartość source jest automatycznie wypełniana na podstawie typu danych.
6. Kliknij **przycisk OK,** aby zapisać konfigurację.
7. Powtórz tę czynność dla dodatkowych typów danych.

W ciągu około 30 minut można wyświetlić dane z konta magazynu w obszarze roboczym usługi Log Analytics. Zobaczysz tylko dane, które są zapisywane w magazynie po zastosowaniu konfiguracji. Obszar roboczy nie odczytuje wcześniej istniejących danych z konta magazynu.

> [!NOTE]
> Portal nie sprawdza, czy źródło istnieje na koncie magazynu lub jeśli są zapisywane nowe dane.



## <a name="next-steps"></a>Następne kroki

* [Zbieranie dzienników i metryk dla usług platformy Azure](collect-azure-metrics-logs.md) dla obsługiwanych usług platformy Azure.
* [Włącz rozwiązania,](../../azure-monitor/insights/solutions.md) aby zapewnić wgląd w dane.
* [Użyj zapytań wyszukiwania](../../azure-monitor/log-query/log-query-overview.md) do analizowania danych.
