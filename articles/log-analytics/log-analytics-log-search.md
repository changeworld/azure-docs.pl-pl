---
title: Zaloguj wyszukiwanie Azure Log Analytics | Dokumentacja firmy Microsoft
description: Wymagane jest wyszukiwania dziennika, aby pobrać wszystkie dane z analizy dzienników.  W tym artykule opisano sposób nowy dziennik wyszukiwania są używane w analizy dzienników i zapewnia pojęcia, które należy zrozumieć przed utworzeniem jeden.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: bwren
ms.component: na
ms.openlocfilehash: d1cd4f938092c6a1312bd0c0ec9240d459d67e83
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131291"
---
# <a name="understanding-log-searches-in-log-analytics"></a>Opis wyszukiwania dziennika analizy dzienników

Wymagane jest wyszukiwania dziennika, aby pobrać wszystkie dane z analizy dzienników.  Czy w przypadku analizowania danych w portalu, konfigurowanie ma być powiadamiany o określony warunek reguły alertu lub pobierania danych przy użyciu interfejsu API usługi Analiza dzienników, umożliwia wyszukiwanie dziennika Określ dane, które mają.  W tym artykule opisano sposób użycia dziennika wyszukiwania w analizy dzienników i zapewnia pojęcia, które należy zrozumieć przed utworzeniem jeden. Zobacz [następne kroki](#next-steps) sekcji, aby uzyskać więcej informacji na temat tworzenia i edytowania dziennik wyszukiwania i odwołań na język zapytań.

## <a name="where-log-searches-are-used"></a>Gdzie są używane dziennik wyszukiwania

Różne sposoby, który będzie używany dziennik wyszukiwania w analizy dzienników są następujące:

- **Portals.** W repozytorium w portalu Azure można wykonać analizy interakcyjnej danych lub [portal analityka zaawansowane](https://go.microsoft.com/fwlink/?linkid=856587).  Dzięki temu można edytować zapytania i Analizuj wyniki w różnych formatach i wizualizacji.  Większość zapytań, które możesz utworzyć zostanie uruchomiony w jednym z portali i następnie skopiowana po zweryfikowaniu, że działa zgodnie z oczekiwaniami.
- **Reguły alertów.** [Reguły alertów](log-analytics-alerts.md) aktywne identyfikowanie problemów z danych w obszarze roboczym.  Każdej reguły alertu jest oparta na wyszukiwania dziennika, który jest automatycznie uruchamiany w regularnych odstępach czasu.  Wyniki są sprawdzana w celu określenia, czy można utworzyć alertu.
- **Widoki.**  Możesz utworzyć wizualizacje danych do uwzględnienia w pulpity nawigacyjne użytkownika z [Widok projektanta](log-analytics-view-designer.md).  Wyszukiwanie dziennika zawierają dane używane przez [Kafelki](log-analytics-view-designer-tiles.md) i [części wizualizacji](log-analytics-view-designer-parts.md) w każdym widoku.  Możesz można przejść z części wizualizacji do strony wyszukiwania dziennika podczas dalszej analizy danych.
- **Eksportowanie.**  Podczas eksportowania danych z obszaru roboczego analizy dzienników do programu Excel lub [usługi Power BI](log-analytics-powerbi.md), tworzenie wyszukiwania dziennika do definiowania danych do wyeksportowania.
- **PowerShell.** Należy uruchomić skrypt programu PowerShell z wiersza polecenia lub element runbook usługi Automatyzacja Azure, która używa [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) do pobierania danych z analizy dzienników.  To polecenie cmdlet wymaga zapytań w celu określenia danych do pobrania.
- **Interfejs API analizy dziennika.**  [Analizy dzienników logowania interfejsu API search](log-analytics-log-search-api.md) umożliwia dowolnego klienta interfejsu API REST do pobierania danych z obszaru roboczego.  Żądanie interfejsu API zawiera zapytanie, które jest wykonywane na analizy dzienników do określenia danych do pobrania.

![Dziennik wyszukiwania](media/log-analytics-log-search-new/log-search-overview.png)

## <a name="how-log-analytics-data-is-organized"></a>Sposób organizowania danych analizy dzienników
Podczas tworzenia zapytania należy rozpocząć od określania tabel, które znajdują się dane, którego szukasz. Każdy [źródła danych](log-analytics-data-sources.md) i [rozwiązania](../operations-management-suite/operations-management-suite-solutions.md) przechowuje dane w tabelach dedykowanych w obszarze roboczym analizy dzienników.  Dokumentacja dla każdego źródła danych i rozwiązanie zawiera nazwę typu danych, które tworzy i opis każdego z jego właściwości.  Wiele zapytań będzie wymagać tylko dane z jednej tabeli, ale inne osoby mogą Użyj różnych opcji, aby uwzględnić dane z wielu tabel.

![Tabele](media/log-analytics-log-search-new/queries-tables.png)


## <a name="writing-a-query"></a>Pisanie zapytań
Stanowiącej podstawę dziennika jest wyszukiwanie w analizy dzienników [język zapytań szeroką gamę](https://docs.loganalytics.io/) umożliwia pobieranie i analizowanie danych w repozytorium w różny sposób.  Tym samym języku zapytania jest używana do [usługi Application Insights](../application-insights/app-insights-analytics.md).  Dowiedzieć, jak napisać zapytanie jest kluczowym elementem tworzenia dziennik wyszukiwania w analizy dzienników.  Będzie zazwyczaj rozpoczyna się z zapytaniami podstawowa, a następnie postępu za pomocą bardziej zaawansowanych funkcji, zgodnie z wymaganiami staną się bardziej złożonych.

Podstawowa struktura zapytania jest tabeli źródłowej następuje szereg operatory oddzielone znakiem potoku `|`.  Użytkownik może łańcuch wielu operatorów w celu udoskonalenia danych i wykonywanie zaawansowanych funkcji.

Na przykład załóżmy, że chcesz znaleźć górny dziesięć komputerów z większości zdarzeń błędu w ciągu ostatnich dni.

    Event
    | where (EventLevelName == "Error")
    | where (TimeGenerated > ago(1days))
    | summarize ErrorCount = count() by Computer
    | top 10 by ErrorCount desc

Lub może być chcesz wyszukać komputery, których nie zastosowano pulsu w ostatni dzień.

    Heartbeat
    | where TimeGenerated > ago(7d)
    | summarize max(TimeGenerated) by Computer
    | where max_TimeGenerated < ago(1d)  

Jak wykres liniowy z użycie procesora dla każdego komputera w ostatnim tygodniu?

    Perf
    | where ObjectName == "Processor" and CounterName == "% Processor Time"
    | where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
    | summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
    | render timechart    

Szybkie próbki widać, że niezależnie od typu danych, który pracy ze strukturą zapytania jest podobne.  Można podzielić je w dół na różne czynności wysyłania danych z jednego polecenia przez potok do następnego polecenia.

Możesz także zbadać danych między obszarami roboczymi analizy dzienników w ramach subskrypcji.

    union Update, workspace("contoso-workspace").Update
    | where TimeGenerated >= ago(1h)
    | summarize dcount(Computer) by Classification 


Pełna dokumentacja języka zapytań usługi Analiza dzienników Azure tym samouczki i materiały referencyjne dotyczące języka, zobacz [dokumentację dotyczącą języka zapytań usługi Analiza dzienników Azure](https://docs.loganalytics.io/).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [portali, które umożliwiają tworzenie i edytowanie dziennika wyszukiwania](log-analytics-log-search-portals.md).
- Zapoznaj się z [Samouczek o pisaniu zapytań](log-analytics-tutorial-viewdata.md) przy użyciu nowego języka zapytań.
