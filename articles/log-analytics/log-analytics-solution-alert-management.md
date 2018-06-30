---
title: Alert rozwiązania do zarządzania w Azure Log Analytics | Dokumentacja firmy Microsoft
description: Rozwiązanie zarządzania alertami w Log Analytics pomaga analizować wszystkie alerty w danym środowisku.  Oprócz Konsolidacja alertów wygenerowanych w analizy dzienników jego importuje alerty z połączonych grup zarządzania programu System Center Operations Manager do analizy dzienników.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: fe5d534e-0418-4e2f-9073-8025e13271a8
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: eb61a48e8c479db4742d65187b202655f29b032d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131051"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Alert rozwiązania do zarządzania w Analiza dzienników Azure

![Ikona zarządzania alertu](media/log-analytics-solution-alert-management/icon.png)

Rozwiązanie zarządzania alertami pomaga analizować wszystkie alerty w repozytorium analizy dzienników.  Te alerty mogą pochodzić z różnych źródeł, łącznie z tych źródeł [utworzone przez analizy dzienników](log-analytics-alerts.md) lub [zaimportowane z Nagios lub Zabbix](log-analytics-linux-agents.md).  Rozwiązanie również importuje alerty z dowolnej [podłączone grupy zarządzania programu System Center Operations Manager](log-analytics-om-agents.md).

## <a name="prerequisites"></a>Wymagania wstępne
Rozwiązanie współpracuje z żadnych rekordów w repozytorium analizy dzienników z typem **alertu**, dlatego należy wykonać, niezależnie od konfiguracji jest wymagany do zbierania tych rekordów.

- W przypadku alertów analizy dzienników [tworzyć reguły alertów](log-analytics-alerts.md) do tworzenia rekordów alertu bezpośrednio w repozytorium.
- W przypadku alertów Nagios i Zabbix [skonfigurować te serwery](log-analytics-linux-agents.md) do wysyłania alertów do analizy dzienników.
- W przypadku alertów programu System Center Operations Manager [Połącz grupę zarządzania programu Operations Manager do swojego obszaru roboczego analizy dzienników](log-analytics-om-agents.md).  Wszystkie alerty utworzone w programie System Center Operations Manager są importowane do analizy dzienników.  

## <a name="configuration"></a>Konfigurowanie
Dodaj rozwiązanie zarządzania alertami do obszaru roboczego analizy dzienników przy użyciu procesu opisanego w [dodać rozwiązania](log-analytics-add-solutions.md).  Nie są wymagane żadne dalsze czynności konfiguracyjne.

## <a name="management-packs"></a>Pakiety administracyjne
Jeśli grupa zarządzania programu System Center Operations Manager jest podłączony do swojego obszaru roboczego analizy dzienników, następujące pakiety administracyjne są zainstalowane w programie System Center Operations Manager podczas dodawania tego rozwiązania.  Brak konfiguracji lub konserwacji pakiety administracyjne wymagane.  

* Zarządzania alertami programu Microsoft System Center Advisor (Microsoft.IntelligencePacks.AlertManagement)

Aby uzyskać więcej informacji na temat aktualizowania pakietów administracyjnych rozwiązania, zobacz artykuł [Connect Operations Manager to Log Analytics](log-analytics-om-agents.md) (Połączenie programu Operations Manager z usługą Log Analytics).

## <a name="data-collection"></a>Zbieranie danych
### <a name="agents"></a>Agenci
W poniższej tabeli opisano połączone źródła, które obsługuje to rozwiązanie.

| Połączone źródło | Pomoc techniczna | Opis |
|:--- |:--- |:--- |
| [Agenci dla systemu Windows](log-analytics-windows-agent.md) | Nie |Bezpośrednie agentów systemu Windows nie generują alerty.  Można tworzyć alerty analizy dziennika zdarzeń i zebrać danych wydajności z systemu Windows agentów. |
| [Agenci dla systemu Linux](log-analytics-linux-agents.md) | Nie |Bezpośrednie agentów systemu Linux nie generują alerty.  Dziennik analizy alerty mogą być tworzone z zdarzenia i dane wydajności zebrane z agentów systemu Linux.  Nagios i Zabbix alerty są zbierane z tych serwerów, które wymagają agenta systemu Linux. |
| [Grupa zarządzania programu System Center Operations Manager](log-analytics-om-agents.md) |Yes |Alerty, które są generowane na agenty programu Operations Manager są dostarczane do grupy zarządzania i następnie przekazywane do analizy dzienników.<br><br>Połączenie bezpośrednie agenty programu Operations Manager do analizy dzienników nie jest wymagane. Dane alertów jest przekazywany z grupy zarządzania do repozytorium analizy dzienników. |


### <a name="collection-frequency"></a>Częstotliwość zbierania
- Alert rekordy są dostępne do rozwiązania, jak są przechowywane w repozytorium.
- Dane alertów są wysyłane z grupy zarządzania programu Operations Manager do analizy dzienników co trzy minuty.  

## <a name="using-the-solution"></a>Użycie rozwiązania
Po dodaniu rozwiązania zarządzania alertami do obszaru roboczego analizy dzienników **zarządzania alertami** kafelka jest dodawany do pulpitu nawigacyjnego.  Ten Kafelek Wyświetla graficzną reprezentację liczby aktywnych alertów, które zostały wygenerowane w ciągu ostatnich 24 godzin i liczba.  Nie można zmienić tego zakresu czasu.

![Alert kafelka zarządzania](media/log-analytics-solution-alert-management/tile.png)

Polecenie **zarządzania alertami** Kafelek, aby otworzyć **zarządzania alertami** pulpitu nawigacyjnego.  Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli.  Każda kolumna zawiera listę top 10 alertów według liczby spełniających kryteria tej kolumny. dla określonego zakresu i zakres czasu.  Możesz uruchomić wyszukiwanie dziennika, które zawiera całą listę klikając **zobaczyć wszystkie** u dołu kolumny lub przez kliknięcie nagłówka kolumny.

| Kolumna | Opis |
|:--- |:--- |
| Alerty krytyczne |Wszystkie alerty o ważności krytyczna pogrupowane według nazwę alertu.  Kliknij nazwę alertu, aby uruchomić wyszukiwanie dziennika zwracanie wszystkich rekordów dla tego alertu. |
| Alerty ostrzegawcze |Wszystkie alerty o ważności ostrzeżenie pogrupowane według nazwę alertu.  Kliknij nazwę alertu, aby uruchomić wyszukiwanie dziennika zwracanie wszystkich rekordów dla tego alertu. |
| Aktywne alerty programu SCOM |Wszystkie alerty zebranych z programu Operations Manager przez każdy stan inny niż *zamknięte* pogrupowane według źródła, który wygenerował alert. |
| Wszystkie aktywne alerty |Wszystkie alerty o wszelkich ważność pogrupowane według nazwę alertu. Tylko innych niż obejmuje alertów programu Operations Manager z jakimkolwiek stanem stanów *zamknięte*. |

Przewiń w prawo, pulpit nawigacyjny wyświetla kilka typowych zapytań, które można kliknąć przeprowadzić [wyszukiwania dziennika](log-analytics-log-searches.md) dla danych alertów.

![Pulpit nawigacyjny zarządzania alertu](media/log-analytics-solution-alert-management/dashboard.png)


## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics
Rozwiązanie zarządzania alertami analizuje żadnych rekordów z typem **alertu**.  Alerty utworzone przez analizy dzienników lub zbierane z Nagios lub Zabbix nie są bezpośrednio zbierane przez rozwiązanie.

Rozwiązanie zaimportować alerty z programu System Center Operations Manager i tworzy odpowiedni rekord dla każdego typu **alertu** i SourceSystem z **OpsManager**.  Te rekordy są właściwości w poniższej tabeli:  

| Właściwość | Opis |
|:--- |:--- |
| Typ |*Alert* |
| SourceSystem |*OpsManager* |
| AlertContext |Szczegóły elementu danych, który spowodował alert zostanie wygenerowany w formacie XML. |
| AlertDescription |Szczegółowy opis alertu. |
| AlertId |Identyfikator GUID alertu. |
| AlertName |Nazwa alertu. |
| AlertPriority |Poziom priorytetu alertu. |
| AlertSeverity |Poziom ważności alertu. |
| AlertState |Najnowszy stan rozwiązania alertu. |
| LastModifiedBy |Nazwa użytkownika, który ostatniej modyfikacji alertu. |
| ManagementGroupName |Nazwa grupy zarządzania, w którym alert został wygenerowany. |
| RepeatCount |Ile razy ten sam alert został wygenerowany dla tego samego monitorowany obiekt od rozwiązywany. |
| ResolvedBy |Nazwa użytkownika, który rozwiązać alert. Pusta, jeśli alert nie zostały rozpoznane. |
| SourceDisplayName |Wyświetlana nazwa obiektu monitorowania, który wygenerował alert. |
| SourceFullName |Pełna nazwa obiektu monitorowania, który wygenerował alert. |
| TicketId |Identyfikator biletu alert, jeśli środowisko programu System Center Operations Manager jest zintegrowany z procesem przypisywania biletów dla alertów.  Identyfikator pusty biletu nie jest przypisany. |
| TimeGenerated |Data i godzina utworzenia alertu. |
| TimeLastModified |Data i godzina ostatniej modyfikacji alertu. |
| TimeRaised |Data i godzina wygenerowania alertu. |
| TimeResolved |Data i godzina, o którym alert został rozwiązany. Pusta, jeśli alert nie zostały rozpoznane. |

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników
W poniższej tabeli przedstawiono przykładowy dziennik wyszukuje rekordy alertów zebranych przez to rozwiązanie: 

| Zapytanie | Opis |
|:---|:---|
| Alert &#124; gdzie SourceSystem == "OpsManager" i AlertSeverity == "error" i TimeRaised > ago(24h) |Alerty krytyczne zgłoszone w ciągu ostatnich 24 godzin |
| Alert &#124; gdzie AlertSeverity == "ostrzeżenie" i TimeRaised > ago(24h) |Alerty ostrzegawcze zgłoszone w ciągu ostatnich 24 godzin |
| Alertu &#124; gdzie SourceSystem == "OpsManager" i stan alertu! = "Zamknięte" i TimeRaised > ago(24h) &#124; Podsumuj Count = count() przez SourceDisplayName |Źródła z aktywnymi alertami zgłoszonymi w ciągu ostatnich 24 godzin |
| Alert &#124; gdzie SourceSystem == "OpsManager" i AlertSeverity == "error" i TimeRaised > ago(24h) i stan alertu! = "Zamknięte" |Alerty krytyczne zgłoszone w ciągu ostatnich 24 godzin, które są nadal aktywne |
| Alert &#124; gdzie SourceSystem == "OpsManager" i TimeRaised > ago(24h) i stan alertu == "Zamknięte" |Alerty zgłoszone w ciągu ostatnich 24 godzin, które teraz są zamknięte |
| Alert &#124; gdzie SourceSystem == "OpsManager" i TimeRaised > ago(1d) &#124; Podsumuj Count = count() przez AlertSeverity |Alerty zgłoszone w ciągu ostatniej doby pogrupowane według ważności |
| Alert &#124; gdzie SourceSystem == "OpsManager" i TimeRaised > ago(1d) &#124; Sortuj według RepeatCount desc |Alerty zgłoszone w ciągu ostatniej doby posortowane według wartości liczby powtórzeń |



## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej na temat [alertów w usłudze Log Analytics](log-analytics-alerts.md), aby poznać szczegóły generowania alertów z usługi Log Analytics.
