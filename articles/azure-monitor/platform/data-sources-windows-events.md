---
title: Zbieranie i analizowanie dzienników zdarzeń Windows w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania zbierania dzienników zdarzeń Windows usługi Azure monitor i szczegółowe informacje o rekordy, które tworzą.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: 8fcab1ead4ab6135e715dc173829178e43f8af2a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60236906"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Źródła danych dziennika zdarzeń Windows w usłudze Azure Monitor
Dzienniki zdarzeń Windows są jedną z najbardziej typowych [źródeł danych](agent-data-sources.md) do zbierania danych przy użyciu agentów Windows, ponieważ wiele aplikacji zapisu w dzienniku zdarzeń Windows.  Oprócz określenia żadnych dzienników niestandardowych utworzone przez aplikacje, które są potrzebne do monitorowania może zbierać zdarzenia z dzienników standardowych, takich jak systemu i aplikacji.

![Zdarzenia systemu Windows](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Dzienniki konfigurowanie zdarzeń Windows
Skonfiguruj dzienniki zdarzeń Windows z [menu danych w zaawansowanych ustawieniach](agent-data-sources.md#configuring-data-sources).

Usługa Azure Monitor zbiera tylko zdarzenia z dzienników zdarzeń Windows, które są określone w ustawieniach.  Możesz dodać dziennik zdarzeń przez wpisanie nazwy dziennika, a następnie klikając polecenie **+**.  Dla każdego dziennika są zbierane tylko zdarzenia o ważności wybranych.  Zaznacz ważności dla określonego dziennika, które mają być zbierane.  Nie można podać wszelkie dodatkowe kryteria, aby filtrować zdarzenia.

Podczas wpisywania nazwy dziennika zdarzeń, usługi Azure Monitor zapewnia sugestie dotyczące nazw pospolitych dziennika zdarzeń. Jeśli dziennik, który chcesz dodać, nie ma na liście, możesz je dodać, wpisując pełną nazwę dziennika. Pełna nazwa dziennika można znaleźć za pomocą Podglądu zdarzeń. W Podglądzie zdarzeń, otwórz *właściwości* strony dziennika i skopiuj ciąg z *imię i nazwisko* pola.

![Konfiguruj zdarzenia Windows](media/data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Zbieranie danych
Usługa Azure Monitor umożliwia zbieranie informacji o każdego zdarzenia, które odpowiada wybranej ważności z monitorowanych dziennika zdarzeń, podczas tworzenia zdarzenia.  Agent rejestruje jej miejscu w każdym dzienniku zdarzeń, który zbiera z.  Jeśli agent przejdzie do trybu offline w okresie czasu, następnie zbiera zdarzenia z tam, gdzie ją ostatnia przerwaliśmy, nawet jeśli te zdarzenia zostały utworzone, gdy agent był w trybie offline.  Istnieje możliwość dla tych zdarzeń nie można pobrać, jeśli w dzienniku zdarzeń opakowuje ze zdarzeniami niepobranych zostaną zastąpione, gdy agent jest w trybie offline.

>[!NOTE]
>Usługa Azure Monitor nie są zbierane zdarzenia inspekcji utworzone przez program SQL Server ze źródła *MSSQLSERVER* z Identyfikatorem zdarzenia 18453, który zawiera słowa kluczowe — *klasycznego* lub *Sukces inspekcji* i słowo kluczowe *0xa0000000000000*.
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
| ManagementGroupName |Nazwa grupy zarządzania agentów programu System Center Operations Manager.  W innych agentów ta wartość to `AOI-<workspace ID>` |
| RenderedDescription |Opis zdarzenia przy użyciu wartości parametrów |
| Element źródłowy |Źródło zdarzenia. |
| SourceSystem |Typ agenta, które zostały zebrane zdarzenia. <br> Łączenie OpsManager — Windows agent, bezpośrednio lub zarządzania programu Operations Manager <br> Linux — Wszyscy agenci systemu Linux  <br> AzureStorage — Diagnostyka Azure |
| TimeGenerated |Data i godzina utworzenia zdarzenia w Windows. |
| UserName |Nazwa użytkownika konta, które są rejestrowane zdarzenia. |

## <a name="log-queries-with-windows-events"></a>Dziennik zapytań ze zdarzeniami Windows
Poniższa tabela zawiera przykłady różnych zapytań dziennika, które pobierają rekordy zdarzeń Windows.

| Zapytanie | Opis |
|:---|:---|
| Wydarzenie |Wszystkie zdarzenia Windows. |
| Zdarzenie &#124; gdzie EventLevelName == "error" |Windows wszystkich zdarzeń o ważności błędu. |
| Event &#124; summarize count() by Source |Liczba Windows zdarzenia według źródła. |
| Event &#124; where EventLevelName == "error" &#124; summarize count() by Source |Zdarzenia błędu liczba Windows według źródła. |


## <a name="next-steps"></a>Kolejne kroki
* Skonfiguruj usługę Log Analytics do gromadzenia innych [źródeł danych](agent-data-sources.md) do analizy.
* Dowiedz się więcej o [rejestrowania zapytań](../log-query/log-query-overview.md) analizować dane zbierane z innych źródeł danych i rozwiązań.  
* Konfigurowanie [zbieranie liczników wydajności](data-sources-performance-counters.md) z agentów użytkownika Windows.