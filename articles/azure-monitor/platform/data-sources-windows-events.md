---
title: Zbieranie i analizowanie dzienników zdarzeń systemu Windows w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób konfigurowania kolekcji dzienników zdarzeń systemu Windows przez usługę Azure Monitor oraz szczegółów rekordów, które tworzą.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aa34196233ce4037ef6fa49b782b9aa958f7632d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274686"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Źródła danych dziennika zdarzeń systemu Windows w monitorze platformy Azure
Dzienniki zdarzeń systemu Windows są jednym z najczęstszych [źródeł danych](agent-data-sources.md) do zbierania danych przy użyciu agentów systemu Windows, ponieważ wiele aplikacji zapisuje w dzienniku zdarzeń systemu Windows.  Zdarzenia można zbierać ze standardowych dzienników, takich jak System i Aplikacja, oprócz określania wszelkich dzienników niestandardowych utworzonych przez aplikacje, które należy monitorować.

![Zdarzenia systemu Windows](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Konfigurowanie dzienników zdarzeń systemu Windows
Konfigurowanie dzienników zdarzeń systemu Windows z [menu Dane w obszarze Ustawienia zaawansowane](agent-data-sources.md#configuring-data-sources).

Usługa Azure Monitor zbiera tylko zdarzenia z dzienników zdarzeń systemu Windows, które są określone w ustawieniach.  Dziennik zdarzeń można dodać, wpisując nazwę dziennika i **+** klikając przycisk .  Dla każdego dziennika zbierane są tylko zdarzenia z wybranymi ważnościami.  Sprawdź ważności dla określonego dziennika, który chcesz zebrać.  Nie można podać żadnych dodatkowych kryteriów filtrowania zdarzeń.

Podczas wpisywanie nazwy dziennika zdarzeń usługa Azure Monitor udostępnia sugestie dotyczące typowych nazw dzienników zdarzeń. Jeśli dziennik, który chcesz dodać, nie jest wyświetlany na liście, nadal można go dodać, wpisując pełną nazwę dziennika. Pełną nazwę dziennika można znaleźć za pomocą podglądu zdarzeń. W przeglądarce zdarzeń otwórz stronę *Właściwości* dziennika i skopiuj ciąg z pola *Pełna nazwa.*

![Konfigurowanie zdarzeń systemu Windows](media/data-sources-windows-events/configure.png)

> [!NOTE]
> Zdarzenia krytyczne z dziennika zdarzeń systemu Windows będą miały ważność "Błąd" w dziennikach monitora platformy Azure.

## <a name="data-collection"></a>Zbieranie danych
Usługa Azure Monitor zbiera każde zdarzenie, które pasuje do wybranej ważności z monitorowanego dziennika zdarzeń podczas tworzenia zdarzenia.  Agent rejestruje swoje miejsce w każdym dzienniku zdarzeń, z których zbiera.  Jeśli agent przejdzie w tryb offline przez pewien czas, następnie zbiera zdarzenia, z których ostatnio zostało przerwane, nawet jeśli te zdarzenia zostały utworzone, gdy agent był w trybie offline.  Istnieje możliwość, że te zdarzenia nie mogą być zbierane, jeśli dziennik zdarzeń zawija niezebrane zdarzenia są zastępowane, gdy agent jest w trybie offline.

>[!NOTE]
>Usługa Azure Monitor nie zbiera zdarzeń inspekcji utworzonych przez program SQL Server ze źródła *MSSQLSERVER* o identyfikatorze zdarzenia 18453 zawierającego słowa kluczowe — *Klasyczny* lub *Audyt sukcesu* i słowo kluczowe *0xa000000000000000*.
>

## <a name="windows-event-records-properties"></a>Właściwości rekordów zdarzeń systemu Windows
Rekordy zdarzeń systemu Windows mają typ **zdarzenia** i mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| Computer (Komputer) |Nazwa komputera, z których pobrano zdarzenie. |
| Kategoria zdarzeń |Kategoria wydarzenia. |
| Eventdata |Wszystkie dane zdarzeń w formacie raw. |
| Identyfikator zdarzenia |Numer zdarzenia. |
| Eventlevel |Ważność zdarzenia w postaci liczbowej. |
| Nazwa eventlevelname |Ważność zdarzenia w formie tekstowej. |
| Eventlog |Nazwa dziennika zdarzeń, z których zostało zebrane zdarzenie. |
| ParametrXml |Wartości parametrów zdarzenia w formacie XML. |
| ManagementGroupName |Nazwa grupy zarządzania agentami programu System Center Operations Manager.  W przypadku innych agentów wartość ta jest`AOI-<workspace ID>` |
| RenderedDescription (RenderedDescription) |Opis zdarzenia z wartościami parametrów |
| Element źródłowy |Źródło zdarzenia. |
| SourceSystem |Typ agenta, z jakiego zdarzenia pobrano zdarzenie. <br> OpsManager — agent systemu Windows, bezpośrednie połączenie lub zarządzanie programem Operations Manager <br> Linux – Wszyscy agenci Linuksa  <br> Usługa AzureStorage — diagnostyka platformy Azure |
| TimeGenerated |Data i godzina utworzenia zdarzenia w systemie Windows. |
| UserName |Nazwa użytkownika konta, które zarejestrowało zdarzenie. |

## <a name="log-queries-with-windows-events"></a>Rejestrowanie zapytań ze zdarzeniami systemu Windows
Poniższa tabela zawiera różne przykłady zapytań dziennika, które pobierają rekordy zdarzeń systemu Windows.

| Zapytanie | Opis |
|:---|:---|
| Wydarzenie |Wszystkie zdarzenia systemu Windows. |
| Zdarzenie &#124; gdzie EventLevelName == "error" |Wszystkie zdarzenia systemu Windows z ważnością błędu. |
| Zdarzenie &#124; podsumuj liczbę() według źródła |Liczba zdarzeń systemu Windows według źródła. |
| Zdarzenie &#124;, w którym EventLevelName == "error" &#124; summarize count() by Source |Liczba zdarzeń błędu systemu Windows według źródła. |


## <a name="next-steps"></a>Następne kroki
* Skonfiguruj usługa Log Analytics do zbierania innych [źródeł danych](agent-data-sources.md) do analizy.
* Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md) do analizowania danych zebranych ze źródeł danych i rozwiązań.  
* [Skonfiguruj kolekcję liczników wydajności](data-sources-performance-counters.md) od agentów systemu Windows.
