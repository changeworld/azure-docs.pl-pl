---
title: Zbieranie i analizowanie dzienników zdarzeń Windows w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Dzienniki zdarzeń Windows są jednymi z najbardziej typowe źródła danych używane przez usługę Log Analytics.  W tym artykule opisano sposób konfigurowania zbierania dzienników zdarzeń Windows i szczegółowe informacje o rekordy, które tworzą w obszarze roboczym usługi Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2017
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 4275b734a30310c896c397a5ef9cc1b218d89476
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842547"
---
# <a name="windows-event-log-data-sources-in-log-analytics"></a>Źródła danych dziennika zdarzeń Windows w usłudze Log Analytics
Dzienniki zdarzeń Windows są jedną z najbardziej typowych [źródeł danych](agent-data-sources.md) do zbierania danych przy użyciu agentów Windows, ponieważ wiele aplikacji zapisu w dzienniku zdarzeń Windows.  Oprócz określenia żadnych dzienników niestandardowych utworzone przez aplikacje, które są potrzebne do monitorowania może zbierać zdarzenia z dzienników standardowych, takich jak systemu i aplikacji.

![Zdarzenia Windows](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Dzienniki konfigurowanie zdarzeń Windows
Skonfiguruj dzienniki zdarzeń Windows z [danych menu Ustawienia usługi Analiza dzienników](agent-data-sources.md#configuring-data-sources).

Usługa log Analytics zbiera tylko zdarzenia z dzienników zdarzeń Windows, które są określone w ustawieniach.  Możesz dodać dziennik zdarzeń przez wpisanie nazwy dziennika, a następnie klikając polecenie **+**.  Dla każdego dziennika są zbierane tylko zdarzenia o ważności wybranych.  Zaznacz ważności dla określonego dziennika, które mają być zbierane.  Nie można podać wszelkie dodatkowe kryteria, aby filtrować zdarzenia.

Podczas wpisywania nazwy dziennika zdarzeń, usługa Log Analytics oferuje sugestie dotyczące nazw pospolitych dziennika zdarzeń. Jeśli dziennik, który chcesz dodać, nie ma na liście, możesz je dodać, wpisując pełną nazwę dziennika. Pełna nazwa dziennika można znaleźć za pomocą Podglądu zdarzeń. W Podglądzie zdarzeń, otwórz *właściwości* strony dziennika i skopiuj ciąg z *imię i nazwisko* pola.

![Konfiguruj zdarzenia Windows](media/data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Zbieranie danych
Usługa log Analytics zbiera każdego zdarzenia, które odpowiada wybranej ważności z monitorowanych dziennika zdarzeń, podczas tworzenia zdarzenia.  Agent rejestruje jej miejscu w każdym dzienniku zdarzeń, który zbiera z.  Jeśli agent przejdzie do trybu offline w okresie czasu, następnie usługi Log Analytics zbiera dane zdarzeń z tam, gdzie ją ostatnia przerwaliśmy, nawet jeśli te zdarzenia zostały utworzone, gdy agent był w trybie offline.  Istnieje możliwość dla tych zdarzeń nie można pobrać, jeśli w dzienniku zdarzeń opakowuje ze zdarzeniami niepobranych zostaną zastąpione, gdy agent jest w trybie offline.

>[!NOTE]
>Usługa log Analytics nie są zbierane zdarzenia inspekcji utworzone przez program SQL Server ze źródła *MSSQLSERVER* z Identyfikatorem zdarzenia 18453, który zawiera słowa kluczowe — *klasycznego* lub *Sukces inspekcji* i słowo kluczowe *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Właściwości rekordy zdarzeń Windows
Rekordy zdarzeń Windows mają typ **zdarzeń** i mają właściwości podane w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| Computer (Komputer) |Nazwa komputera, na którym zostały zebrane zdarzenia. |
| EventCategory |Kategoria zdarzenia. |
| EventData |Wszystkie dane zdarzeń w formacie nieprzetworzonym. |
| Identyfikator zdarzenia |Numer zdarzenia. |
| eventLevel |Ważność zdarzenia w forma liczbowa. |
| EventLevelName |Ważność zdarzenia w postaci tekstu. |
| Dziennik zdarzeń |Nazwa dziennika zdarzeń, które zostały zebrane zdarzenia. |
| ParameterXml |Wartości parametrów zdarzenia w formacie XML. |
| ManagementGroupName |Nazwa grupy zarządzania agentów programu System Center Operations Manager.  W innych agentów ta wartość to AOI-<workspace ID> |
| RenderedDescription |Opis zdarzenia przy użyciu wartości parametrów |
| Element źródłowy |Źródło zdarzenia. |
| SourceSystem |Typ agenta, które zostały zebrane zdarzenia. <br> Łączenie OpsManager — Windows agent, bezpośrednio lub zarządzania programu Operations Manager <br> Linux — Wszyscy agenci systemu Linux  <br> AzureStorage — Diagnostyka Azure |
| TimeGenerated |Data i godzina utworzenia zdarzenia w Windows. |
| UserName |Nazwa użytkownika konta, które są rejestrowane zdarzenia. |

## <a name="log-searches-with-windows-events"></a>Wyszukiwanie w dzienniku zdarzeń Windows
Poniższa tabela zawiera różne przykłady przeszukiwania dzienników, które pobierają rekordy zdarzeń Windows.

| Zapytanie | Opis |
|:---|:---|
| Wydarzenie |Wszystkie zdarzenia Windows. |
| Zdarzenie &#124; gdzie EventLevelName == "error" |Windows wszystkich zdarzeń o ważności błędu. |
| Zdarzenie &#124; Podsumuj count() według źródła |Liczba Windows zdarzenia według źródła. |
| Zdarzenie &#124; gdzie EventLevelName == "error" &#124; Podsumuj count() według źródła |Zdarzenia błędu liczba Windows według źródła. |


## <a name="next-steps"></a>Kolejne kroki
* Skonfiguruj usługę Log Analytics do gromadzenia innych [źródeł danych](agent-data-sources.md) do analizy.
* Dowiedz się więcej o [dziennikach](../../azure-monitor/log-query/log-query-overview.md) analizować dane zbierane z innych źródeł danych i rozwiązań.  
* Użyj [pól niestandardowych](../../log-analytics/log-analytics-custom-fields.md) do analizowania rekordów zdarzeń do poszczególnych pól.
* Konfigurowanie [zbieranie liczników wydajności](data-sources-performance-counters.md) z agentów użytkownika Windows.
