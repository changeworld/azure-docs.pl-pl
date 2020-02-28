---
title: Rozwiązanie usługi alert Management w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Rozwiązanie Zarządzanie alertami w usłudze Log Analytics pomaga analizować wszystkie alerty w danym środowisku.  Oprócz konsolidację alerty wygenerowane w usłudze Log Analytics importuje alerty z połączonych grup zarządzania programu System Center Operations Manager w usłudze Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: 48a825f31a1c5f2eab2fbb71b6f030b8acb5617d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668387"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Rozwiązanie usługi alert Management w usłudze Azure Log Analytics

![Ikona zarządzania alertu](media/alert-management-solution/icon.png)

> [!NOTE]
>  Azure Monitor teraz obsługuje udoskonalone funkcje [zarządzania alertami na dużą skalę](https://aka.ms/azure-alerts-overview), w tym te wygenerowane przez [narzędzia monitorowania, takie jak System Center Operations Manager, Zabbix lub Nagios](https://aka.ms/managing-alerts-other-monitoring-services).
>  


Rozwiązanie zarządzania alertami pomaga analizować wszystkie alerty w repozytorium usługi Log Analytics.  Te alerty mogą pochodzić z różnych źródeł, w tym źródeł [utworzonych przez log Analytics](../../azure-monitor/platform/alerts-overview.md) lub [zaimportowanych z Nagios lub Zabbix](../../azure-monitor/learn/quick-collect-linux-computer.md). Rozwiązanie importuje również alerty z dowolnych [połączonych System Center Operations Manager grup zarządzania](../../azure-monitor/platform/om-agents.md).

## <a name="prerequisites"></a>Wymagania wstępne
Rozwiązanie działa z dowolnymi rekordami w repozytorium Log Analytics z typem **alertu**, dlatego należy wykonać dowolną konfigurację wymaganą do zebrania tych rekordów.

- W przypadku alertów Log Analytics [Utwórz reguły alertów](../../azure-monitor/platform/alerts-overview.md) , aby utworzyć rekordy alertów bezpośrednio w repozytorium.
- W przypadku alertów Nagios i Zabbix [Skonfiguruj te serwery](../../azure-monitor/learn/quick-collect-linux-computer.md) do wysyłania alertów do log Analytics.
- W przypadku alertów System Center Operations Manager [Połącz grupę zarządzania Operations Manager z obszarem roboczym log Analytics](../../azure-monitor/platform/om-agents.md).  Alerty utworzone w programie System Center Operations Manager są importowane do usługi Log Analytics.  

## <a name="configuration"></a>Konfiguracja
Dodaj Alert Management rozwiązanie do obszaru roboczego Log Analytics przy użyciu procesu opisanego w temacie [Dodawanie rozwiązań](../../azure-monitor/insights/solutions.md). Nie są wymagane żadne dalsze czynności konfiguracyjne.

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
| [System Center Operations Manager grupy zarządzania](../../azure-monitor/platform/om-agents.md) |Yes |Alerty, które są generowane na agentów programu Operations Manager są dostarczane z grupą zarządzania i następnie przekazywane do usługi Log Analytics.<br><br>Bezpośrednie połączenie agenta programu Operations Manager do usługi Log Analytics nie jest wymagane. Dane alertu są przekazywane z grupy zarządzania do repozytorium usługi Log Analytics. |


### <a name="collection-frequency"></a>Częstotliwość zbierania
- Rekordy alertów są dostępne do rozwiązania, jak są one przechowywane w repozytorium.
- Dane alertu są wysyłane z grupy zarządzania programu Operations Manager do usługi Log Analytics co trzy minuty.  

## <a name="using-the-solution"></a>Użycie rozwiązania
Po dodaniu Alert Management rozwiązanie do obszaru roboczego Log Analytics kafelek **alert Management** zostanie dodany do pulpitu nawigacyjnego.  Ten Kafelek zawiera liczbę oraz graficzną reprezentację liczby aktywnych alertów, które zostały wygenerowane w ciągu ostatnich 24 godzin.  Nie można zmienić ten zakres czasu.

![Kafelek Zarządzanie alertu](media/alert-management-solution/tile.png)

Kliknij kafelek **alert Management** , aby otworzyć pulpit nawigacyjny **alert Management** .  Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli.  Każda kolumna zawiera listę 10 najważniejszych alertów według liczby odpowiadającego kryteriom tej kolumny dla określonego zakresu i przedziału czasu.  Możesz uruchomić wyszukiwanie w dzienniku, które udostępnia całą listę, klikając pozycję **Zobacz wszystko** u dołu kolumny lub klikając nagłówek kolumny.

| Kolumna | Opis |
|:--- |:--- |
| Alerty krytyczne |Wszystkie alerty o ważności krytycznej, pogrupowane według nazwy alertu.  Kliknij nazwę alertu, aby uruchomić wyszukiwanie w dzienniku zwraca wszystkie rekordy dla tego alertu. |
| Alerty ostrzegawcze |Wszystkie alerty o ważności ostrzeżenia, pogrupowane według nazwy alertu.  Kliknij nazwę alertu, aby uruchomić wyszukiwanie w dzienniku zwraca wszystkie rekordy dla tego alertu. |
| Alerty aktywnych System Center Operations Manager |Wszystkie alerty zebrane z Operations Manager z dowolnym stanem innym niż *zamknięte* pogrupowane według źródła, które wygenerowały alert. |
| Wszystkie aktywne alerty |Wszystkie alerty o dowolnym ważności, pogrupowane według nazwy alertu. Zawiera tylko alerty Operations Manager z dowolnym stanem innym niż *zamknięty*. |

W przypadku przewijania po prawej stronie Pulpit nawigacyjny zawiera kilka typowych zapytań, które można kliknąć, aby wykonać [Wyszukiwanie w dzienniku](../../azure-monitor/log-query/log-query-overview.md) dla danych alertów.

![Zgłoś alert, pulpit nawigacyjny zarządzania](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics
Rozwiązanie Alert Management analizuje każdy rekord z typem **alertu**.  Alerty utworzone przez usługę Log Analytics lub zbierane z Nagios i Zabbix nie są bezpośrednio zbierane przez rozwiązanie.

Rozwiązanie wykonuje Importowanie alertów z System Center Operations Manager i tworzy odpowiadające im rekordy dla każdego typu **alertu** i SourceSystem **OpsManager**.  Te rekordy mają właściwości podane w poniższej tabeli:  

| Właściwość | Opis |
|:--- |:--- |
| `Type` |*Wiadom* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Szczegóły elementu danych, który spowodował alert zostanie wygenerowany w formacie XML. |
| `AlertDescription` |Szczegółowy opis alertu. |
| `AlertId` |Identyfikator GUID alertu. |
| `AlertName` |Nazwa alertu. |
| `AlertPriority` |Priorytet alertu. |
| `AlertSeverity` |Poziom ważności alertu. |
| `AlertState` |Najnowszy stan rozwiązywania alertu. |
| `LastModifiedBy` |Nazwa użytkownika, który ostatnio zmodyfikował alert. |
| `ManagementGroupName` |Nazwa grupy zarządzania, w którym alert został wygenerowany. |
| `RepeatCount` |Liczba przypadków, gdy ten sam alert został wygenerowany dla tego samego monitorowany obiekt od rozwiązania. |
| `ResolvedBy` |Nazwa użytkownika, który rozpoznał alertu. Pusty, jeśli wpis nie został jeszcze rozwiązane. |
| `SourceDisplayName` |Wyświetlana nazwa obiektu monitorowania, który wygenerował alert. |
| `SourceFullName` |Pełna nazwa obiektu monitorowania, który wygenerował alert. |
| `TicketId` |Identyfikator biletu, alert, jeśli środowisko programu System Center Operations Manager jest zintegrowane z procesem przypisywania biletów dla alertów.  Identyfikator pusty biletu nie jest przypisany. |
| `TimeGenerated` |Data i godzina utworzenia alertu. |
| `TimeLastModified` |Data i godzina ostatniej modyfikacji alertu. |
| `TimeRaised` |Data i godzina, który został wygenerowany alert. |
| `TimeResolved` |Data i godzina, który ten alert został rozwiązany. Pusty, jeśli wpis nie został jeszcze rozwiązane. |

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



## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej na temat [alertów w usłudze Log Analytics](../../azure-monitor/platform/alerts-overview.md), aby poznać szczegóły generowania alertów z usługi Log Analytics.
