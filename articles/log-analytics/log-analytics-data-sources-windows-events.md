---
title: Zbieranie i analizowanie dzienników zdarzeń systemu Windows w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Dzienniki zdarzeń systemu Windows są jednym z najbardziej typowych źródeł danych używanych przez analizy dzienników.  W tym artykule opisano sposób konfigurowania zbierania dzienników zdarzeń systemu Windows i szczegóły rekordów tworzonych w obszarze roboczym analizy dzienników.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2017
ms.author: bwren
ms.openlocfilehash: 7a7deb4d7a287b2e9613e6035a7ffd7bb6f14f9c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2017
ms.locfileid: "26782034"
---
# <a name="windows-event-log-data-sources-in-log-analytics"></a>Źródła danych dziennika zdarzeń systemu Windows w analizy dzienników
Dzienniki zdarzeń systemu Windows są jednym z najbardziej typowych [źródeł danych](log-analytics-data-sources.md) do zbierania danych za pomocą agentów systemu Windows, ponieważ wiele aplikacji zapisu do dziennika zdarzeń systemu Windows.  Oprócz określenia żadnych dzienników niestandardowych tworzony przez aplikacje, które należy monitorować może zbierać zdarzenia z dzienników standardowe, takie jak systemu i aplikacji.

![Zdarzenia systemu Windows](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Dzienniki zdarzeń Konfigurowanie systemu Windows
Konfigurowanie dzienników zdarzeń systemu Windows z [danych menu Ustawienia usługi Analiza dzienników](log-analytics-data-sources.md#configuring-data-sources).

Analiza dzienników zbiera tylko zdarzenia z dzienników zdarzeń systemu Windows, które są określone w ustawieniach.  Dziennik zdarzeń można dodać wpisać nazwę dziennika, a następnie klikając polecenie  **+** .  Dla każdego dziennika są zbierane tylko zdarzenia dla wybranego ważności.  Sprawdzanie ważności dla określonego dziennika, który chcesz zebrać.  Nie można podać wszelkie dodatkowe kryteria, aby filtrować zdarzenia.

Podczas wpisywania nazwy dziennika zdarzeń analizy dzienników zawiera sugestie dotyczące typowych nazw w dzienniku zdarzeń. Jeśli dziennik, który ma zostać dodany, nie ma na liście, należy go dodać, wpisując w pełną nazwę dziennika. Pełna nazwa dziennika można znaleźć za pomocą Podglądu zdarzeń. W Podglądzie zdarzeń, otwórz *właściwości* strony dziennika i skopiuj ciąg z *imię i nazwisko* pola.

![Skonfiguruj zdarzenia systemu Windows](media/log-analytics-data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Zbieranie danych
Analiza dzienników zbiera każdego zdarzenia odpowiadający wybranej ważności z monitorowanych dziennika zdarzeń zdarzenie zostało utworzone.  Agent rejestruje jego miejsce w każdym dzienniku zdarzeń zbierane z.  Jeśli w danym okresie czasu agent przejdzie do trybu offline, następnie analizy dzienników zbiera dane zdarzeń z którym ostatnio przerwał, nawet jeśli te zdarzenia zostały utworzone podczas agent jest w trybie offline.  Istnieje możliwość dla tych zdarzeń nie można pobrać, jeśli dziennik zdarzeń jest zawijana niepobranych zdarzenia zostaną zastąpione, gdy agent jest w trybie offline.

>[!NOTE]
>Analiza dzienników nie są zbierane zdarzenia inspekcji utworzone przez program SQL Server ze źródła *MSSQLSERVER* o identyfikatorze zdarzenia 18453, który zawiera słowa kluczowe — *klasycznego* lub *Powodzenie inspekcji* i słowo kluczowe *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Właściwości rekordy zdarzeń systemu Windows
Rekordy zdarzeń systemu Windows mają typ **zdarzeń** i mieć właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| Computer (Komputer) |Nazwa komputera, na którym zostały zebrane zdarzenia. |
| EventCategory |Kategoria zdarzenia. |
| EventData |Dane wszystkich zdarzeń w formacie nieprzetworzonym. |
| Identyfikator zdarzenia |Numer zdarzenia. |
| eventLevel |Waga zdarzenia w liczbowe. |
| EventLevelName |Waga zdarzenia w postaci tekstu. |
| Dziennik zdarzeń |Nazwa dziennika zdarzeń, które zostały zebrane zdarzenia. |
| ParameterXml |Wartości parametrów zdarzenia w formacie XML. |
| ManagementGroupName |Nazwa grupy zarządzania agentami programu System Center Operations Manager.  Dla innych agentów ta wartość jest AOI-<workspace ID> |
| RenderedDescription |Opis zdarzenia przy użyciu wartości parametrów |
| Element źródłowy |Źródło zdarzenia. |
| SourceSystem |Typ agenta, które zostały zebrane zdarzenia. <br> Połącz OpsManager — agent systemu Windows, bądź bezpośrednio lub zarządzanych w programie Operations Manager <br> Linux — wszystkich agentów systemu Linux  <br> AzureStorage — Diagnostyka Azure |
| TimeGenerated |Data i godzina utworzenia zdarzenia w systemie Windows. |
| UserName |Nazwa użytkownika konta, które są rejestrowane zdarzenia. |

## <a name="log-searches-with-windows-events"></a>Wyszukiwań dziennika zdarzeń systemu Windows
Poniższa tabela zawiera różne przykłady wyszukiwania dziennika, które pobierają rekordy zdarzeń systemu Windows.

| Zapytanie | Opis |
|:---|:---|
| Wydarzenie |Wszystkie zdarzenia systemu Windows. |
| Zdarzenie &#124; gdzie EventLevelName == "error" |Wszystkie zdarzenia systemu Windows z ważność błędu. |
| Zdarzenie &#124; Podsumowanie funkcji count() przez źródło |Liczba systemu Windows zdarzenia według źródła. |
| Zdarzenie &#124; gdzie EventLevelName == "error" &#124; Podsumowanie funkcji count() przez źródło |Liczba Windows Błąd źródła. |


## <a name="next-steps"></a>Kolejne kroki
* Konfigurowanie analizy dzienników do gromadzenia innych [źródeł danych](log-analytics-data-sources.md) do analizy.
* Dowiedz się więcej o [dziennika wyszukiwania](log-analytics-log-searches.md) analizować dane zebrane ze źródeł danych i rozwiązania.  
* Użyj [pola niestandardowe](log-analytics-custom-fields.md) do analizowania rekordów zdarzeń do poszczególnych pól.
* Skonfiguruj [zbiór liczników wydajności](log-analytics-data-sources-performance-counters.md) z agentów systemu Windows.
