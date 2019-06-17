---
title: Rozwiązanie usługi alert Management w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Rozwiązanie Zarządzanie alertami w usłudze Log Analytics pomaga analizować wszystkie alerty w danym środowisku.  Oprócz konsolidację alerty wygenerowane w usłudze Log Analytics importuje alerty z połączonych grup zarządzania programu System Center Operations Manager w usłudze Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: fe5d534e-0418-4e2f-9073-8025e13271a8
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 06532369efb802606eb13a4b38a8579a3528f999
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60777041"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Rozwiązanie usługi alert Management w usłudze Azure Log Analytics

![Ikona zarządzania alertu](media/alert-management-solution/icon.png)

> [!NOTE]
>  Usługa Azure Monitor teraz obsługuje ulepszonych funkcji [Zarządzanie alertami na dużą skalę](https://aka.ms/azure-alerts-overview), włącznie z wygenerowanymi przez [monitorowania narzędzi, takich jak SCOM, Zabbix lub Nagios](https://aka.ms/managing-alerts-other-monitoring-services).
>  


Rozwiązanie zarządzania alertami pomaga analizować wszystkie alerty w repozytorium usługi Log Analytics.  Te alerty mogą pochodzić z różnych źródeł, w tym tych źródeł [utworzone przez usługę Log Analytics](../../azure-monitor/platform/alerts-overview.md) lub [zaimportowane z Nagios i Zabbix](../../azure-monitor/learn/quick-collect-linux-computer.md). Rozwiązanie również importuje alerty z dowolnego [podłączone grupy zarządzania programu System Center Operations Manager](../../azure-monitor/platform/om-agents.md).

## <a name="prerequisites"></a>Wymagania wstępne
To rozwiązanie działa ze wszystkimi rekordami w repozytorium usługi Log Analytics z typem **alertu**, więc należy wykonać niezależnie od konfiguracji jest wymagany do zbierania tych rekordów.

- W przypadku alertów usługi Log Analytics [tworzyć reguły alertów](../../azure-monitor/platform/alerts-overview.md) utworzyć rekordy alertów bezpośrednio w repozytorium.
- Alerty programów Nagios i Zabbix [Konfigurowanie tych serwerów](../../azure-monitor/learn/quick-collect-linux-computer.md) do wysyłania alertów do usługi Log Analytics.
- W przypadku alertów programu System Center Operations Manager [Połącz grupę zarządzania programu Operations Manager do obszaru roboczego usługi Log Analytics](../../azure-monitor/platform/om-agents.md).  Alerty utworzone w programie System Center Operations Manager są importowane do usługi Log Analytics.  

## <a name="configuration"></a>Konfigurowanie
Dodawanie rozwiązania do zarządzania alertami z obszarem roboczym usługi Log Analytics przy użyciu procesu opisanego w [Dodawanie rozwiązań](../../azure-monitor/insights/solutions.md). Nie są wymagane żadne dalsze czynności konfiguracyjne.

## <a name="management-packs"></a>Pakiety administracyjne
Jeśli grupa zarządzania programu System Center Operations Manager jest połączona z obszarem roboczym usługi Log Analytics, następujące pakiety administracyjne są instalowane w programie System Center Operations Manager po dodaniu tego rozwiązania.  Nie istnieje żadna konfiguracja ani Obsługa pakiety administracyjne wymagane.

* Zarządzanie alertami programu Microsoft System Center Advisor (Microsoft.IntelligencePacks.AlertManagement)

Aby uzyskać więcej informacji na temat aktualizowania pakietów administracyjnych rozwiązania, zobacz artykuł [Connect Operations Manager to Log Analytics](../../azure-monitor/platform/om-agents.md) (Połączenie programu Operations Manager z usługą Log Analytics).

## <a name="data-collection"></a>Zbieranie danych
### <a name="agents"></a>Agenci
W poniższej tabeli opisano połączone źródła, które obsługuje to rozwiązanie.

| Połączone źródło | Pomoc techniczna | Opis |
|:--- |:--- |:--- |
| [Agenci dla systemu Windows](agent-windows.md) | Nie |Agentów bezpośrednich Windows nie generują alerty.  Można tworzyć alertów usługi log Analytics na podstawie zdarzenia i dane wydajności zbierane z Windows agentów. |
| [Agenci dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nie |Bezpośredni agenci dla systemu Linux nie generują alerty.  Alertów usługi log Analytics można utworzyć zdarzenia i dane dotyczące wydajności zbierane z agentów dla systemu Linux.  Alerty programów Nagios i Zabbix są zbierane przy użyciu tych serwerów, które wymagają agenta systemu Linux. |
| [Grupa zarządzania programu System Center Operations Manager](../../azure-monitor/platform/om-agents.md) |Yes |Alerty, które są generowane na agentów programu Operations Manager są dostarczane z grupą zarządzania i następnie przekazywane do usługi Log Analytics.<br><br>Bezpośrednie połączenie agenta programu Operations Manager do usługi Log Analytics nie jest wymagane. Dane alertu są przekazywane z grupy zarządzania do repozytorium usługi Log Analytics. |


### <a name="collection-frequency"></a>Częstotliwość zbierania
- Rekordy alertów są dostępne do rozwiązania, jak są one przechowywane w repozytorium.
- Dane alertu są wysyłane z grupy zarządzania programu Operations Manager do usługi Log Analytics co trzy minuty.  

## <a name="using-the-solution"></a>Użycie rozwiązania
Po dodaniu rozwiązania do zarządzania alertami do obszaru roboczego usługi Log Analytics, **zarządzania alertami** zostanie dodany Kafelek do pulpitu nawigacyjnego.  Ten Kafelek zawiera liczbę oraz graficzną reprezentację liczby aktywnych alertów, które zostały wygenerowane w ciągu ostatnich 24 godzin.  Nie można zmienić ten zakres czasu.

![Kafelek Zarządzanie alertu](media/alert-management-solution/tile.png)

Kliknij pozycję **zarządzania alertami** Kafelek, aby otworzyć **zarządzania alertami** pulpitu nawigacyjnego.  Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli.  Każda kolumna zawiera listę 10 najważniejszych alertów według liczby odpowiadającego kryteriom tej kolumny dla określonego zakresu i przedziału czasu.  Można uruchomić wyszukiwanie w dzienniku, który zawiera całą listę, klikając pozycję **holograficznych** na dole kolumny lub przez kliknięcie nagłówka kolumny.

| Kolumna | Opis |
|:--- |:--- |
| Alerty krytyczne |Wszystkie alerty o ważności krytycznej, pogrupowane według nazwy alertu.  Kliknij nazwę alertu, aby uruchomić wyszukiwanie w dzienniku zwraca wszystkie rekordy dla tego alertu. |
| Alerty ostrzegawcze |Wszystkie alerty o ważności ostrzeżenia, pogrupowane według nazwy alertu.  Kliknij nazwę alertu, aby uruchomić wyszukiwanie w dzienniku zwraca wszystkie rekordy dla tego alertu. |
| Aktywne alerty programu SCOM |Wszystkie alerty zbierane z programu Operations Manager z dowolny stan inny niż *zamknięte* pogrupowane według źródła, który wygenerował alert. |
| Wszystkie aktywne alerty |Wszystkie alerty o dowolnym ważności, pogrupowane według nazwy alertu. Zawiera tylko alertów programu Operations Manager z dowolny stan inny niż *zamknięte*. |

Po przewinięciu w prawo, pulpit nawigacyjny zawiera listę kilku typowych zapytań, które możesz kliknąć do wykonywania [wyszukiwanie w dzienniku](../../azure-monitor/log-query/log-query-overview.md) alertów danych.

![Zgłoś alert, pulpit nawigacyjny zarządzania](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics
Rozwiązanie zarządzania alertami analizuje każdy rekord o typie **alertu**.  Alerty utworzone przez usługę Log Analytics lub zbierane z Nagios i Zabbix nie są bezpośrednio zbierane przez rozwiązanie.

Rozwiązanie zaimportować alerty z programu System Center Operations Manager i tworzy odpowiedni rekord dla każdego typu **alertu** i system źródłowy **OpsManager**.  Te rekordy mają właściwości podane w poniższej tabeli:  

| Właściwość | Opis |
|:--- |:--- |
| Typ |*Zgłoś alert* |
| SourceSystem |*OpsManager* |
| AlertContext |Szczegóły elementu danych, który spowodował alert zostanie wygenerowany w formacie XML. |
| AlertDescription |Szczegółowy opis alertu. |
| AlertId |Identyfikator GUID alertu. |
| AlertName |Nazwa alertu. |
| AlertPriority |Priorytet alertu. |
| AlertSeverity |Poziom ważności alertu. |
| AlertState |Najnowszy stan rozwiązywania alertu. |
| LastModifiedBy |Nazwa użytkownika, który ostatnio zmodyfikował alert. |
| ManagementGroupName |Nazwa grupy zarządzania, w którym alert został wygenerowany. |
| RepeatCount |Liczba przypadków, gdy ten sam alert został wygenerowany dla tego samego monitorowany obiekt od rozwiązania. |
| ResolvedBy |Nazwa użytkownika, który rozpoznał alertu. Pusty, jeśli wpis nie został jeszcze rozwiązane. |
| SourceDisplayName |Wyświetlana nazwa obiektu monitorowania, który wygenerował alert. |
| SourceFullName |Pełna nazwa obiektu monitorowania, który wygenerował alert. |
| TicketId |Identyfikator biletu, alert, jeśli środowisko programu System Center Operations Manager jest zintegrowane z procesem przypisywania biletów dla alertów.  Identyfikator pusty biletu nie jest przypisany. |
| TimeGenerated |Data i godzina utworzenia alertu. |
| TimeLastModified |Data i godzina ostatniej modyfikacji alertu. |
| TimeRaised |Data i godzina, który został wygenerowany alert. |
| TimeResolved |Data i godzina, który ten alert został rozwiązany. Pusty, jeśli wpis nie został jeszcze rozwiązane. |

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników
Poniższa tabela zawiera przykładowe wyszukiwania dzienników dla rekordów alertu zbieranych przez to rozwiązanie: 

| Zapytanie | Opis |
|:---|:---|
| Alert &#124; gdzie system źródłowy == "OpsManager" i AlertSeverity == "error" i TimeRaised > ago(24h) |Alerty krytyczne zgłoszone w ciągu ostatnich 24 godzin |
| Alert &#124; gdzie AlertSeverity == "ostrzeżenie" i TimeRaised > ago(24h) |Alerty ostrzegawcze zgłoszone w ciągu ostatnich 24 godzin |
| Alert &#124; gdzie system źródłowy == "OpsManager" i stan alertu! = "Zamknięte" i TimeRaised > ago(24h) &#124; podsumowanie liczby = count() by SourceDisplayName |Źródła z aktywnymi alertami zgłoszonymi w ciągu ostatnich 24 godzin |
| Alert &#124; gdzie system źródłowy == "OpsManager" i AlertSeverity == "error" i TimeRaised > ago(24h) i stan alertu! = "Zamknięte" |Alerty krytyczne zgłoszone w ciągu ostatnich 24 godzin, które są nadal aktywne |
| Alert &#124; gdzie system źródłowy == "OpsManager" i TimeRaised > ago(24h) i stan alertu == "Zamknięte" |Alerty zgłoszone w ciągu ostatnich 24 godzin, które teraz są zamknięte |
| Alert &#124; gdzie system źródłowy == "OpsManager" i TimeRaised > ago(1d) &#124; podsumowanie liczby = count() by AlertSeverity |Alerty zgłoszone w ciągu ostatniej doby pogrupowane według ważności |
| Alert &#124; gdzie system źródłowy == "OpsManager" i TimeRaised > ago(1d) &#124; Sortuj według malejącej RepeatCount |Alerty zgłoszone w ciągu ostatniej doby posortowane według wartości liczby powtórzeń |



## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej na temat [alertów w usłudze Log Analytics](../../azure-monitor/platform/alerts-overview.md), aby poznać szczegóły generowania alertów z usługi Log Analytics.
