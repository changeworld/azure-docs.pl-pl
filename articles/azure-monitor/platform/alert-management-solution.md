---
title: Rozwiązanie do zarządzania alertami w usłudze Azure Log Analytics | Dokumenty firmy Microsoft
description: Rozwiązanie do zarządzania alertami w usłudze Log Analytics ułatwia analizowanie wszystkich alertów w twoim środowisku.  Oprócz konsolidacji alertów generowanych w usłudze Log Analytics importuje alerty z połączonych grup zarządzania programu System Center Operations Manager do usługi Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: 48a825f31a1c5f2eab2fbb71b6f030b8acb5617d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668387"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Rozwiązanie do zarządzania alertami w usłudze Azure Log Analytics

![Ikona Zarządzanie alertami](media/alert-management-solution/icon.png)

> [!NOTE]
>  Usługa Azure Monitor obsługuje teraz rozszerzone funkcje [zarządzania alertami na dużą skalę,](https://aka.ms/azure-alerts-overview)w tym alertami generowanymi przez [narzędzia monitorujące, takie jak System Center Operations Manager, Zabbix lub Nagios.](https://aka.ms/managing-alerts-other-monitoring-services)
>  


Rozwiązanie do zarządzania alertami ułatwia analizowanie wszystkich alertów w repozytorium usługi Log Analytics.  Te alerty mogą pochodzić z różnych źródeł, w tym z tych źródeł [utworzonych przez usługa Log Analytics](../../azure-monitor/platform/alerts-overview.md) lub [importowanych z Nagios lub Zabbix.](../../azure-monitor/learn/quick-collect-linux-computer.md) Rozwiązanie importuje również alerty z wszystkich [połączonych grup zarządzania programem Operations Manager.](../../azure-monitor/platform/om-agents.md)

## <a name="prerequisites"></a>Wymagania wstępne
Rozwiązanie współpracuje z dowolnymi rekordami w repozytorium usługi Log Analytics z typem **alertu,** więc należy wykonać dowolną konfigurację wymaganą do zbierania tych rekordów.

- W przypadku alertów usługi Log Analytics [należy utworzyć reguły alertów,](../../azure-monitor/platform/alerts-overview.md) aby utworzyć rekordy alertów bezpośrednio w repozytorium.
- W przypadku alertów Nagios i Zabbix [skonfiguruj te serwery](../../azure-monitor/learn/quick-collect-linux-computer.md) do wysyłania alertów do usługi Log Analytics.
- W przypadku alertów programu System Center Operations Manager [połącz grupę zarządzania programu Operations Manager z obszarem roboczym usługi Log Analytics](../../azure-monitor/platform/om-agents.md).  Wszystkie alerty utworzone w programie System Center Operations Manager są importowane do usługi Log Analytics.  

## <a name="configuration"></a>Konfigurowanie
Dodaj rozwiązanie do zarządzania alertami do obszaru roboczego usługi Log Analytics przy użyciu procesu opisanego w [add solutions](../../azure-monitor/insights/solutions.md). Nie są wymagane żadne dalsze czynności konfiguracyjne.

## <a name="management-packs"></a>Pakiety administracyjne
Jeśli grupa zarządzania programu System Center Operations Manager jest połączona z obszarem roboczym usługi Log Analytics, podczas dodawania tego rozwiązania w programie System Center Operations Manager są instalowane następujące pakiety administracyjne.  Nie jest wymagana konfiguracja ani konserwacja pakietów administracyjnych.

* Zarządzanie alertami doradcy programu Microsoft System Center (Microsoft.IntelligencePacks.AlertManagement)

Aby uzyskać więcej informacji na temat aktualizowania pakietów administracyjnych rozwiązania, zobacz artykuł [Connect Operations Manager to Log Analytics](../../azure-monitor/platform/om-agents.md) (Połączenie programu Operations Manager z usługą Log Analytics).

## <a name="data-collection"></a>Zbieranie danych
### <a name="agents"></a>Agenci
W poniższej tabeli opisano połączone źródła, które obsługuje to rozwiązanie.

| Połączone źródło | Pomoc techniczna | Opis |
|:--- |:--- |:--- |
| [Agenci dla systemu Windows](agent-windows.md) | Nie |Bezpośredni agenci systemu Windows nie generują alertów.  Alerty usługi Log Analytics można tworzyć na podstawie zdarzeń i danych dotyczących wydajności zebranych od agentów systemu Windows. |
| [Agenci dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nie |Bezpośredni agenci Systemu Linux nie generują alertów.  Alerty usługi Log Analytics mogą być tworzone na podstawie zdarzeń i danych dotyczących wydajności zebranych od agentów systemu Linux.  Alerty Nagios i Zabbix są zbierane z tych serwerów, które wymagają agenta Linuksa. |
| [Grupa zarządzania programu System Center Operations Manager](../../azure-monitor/platform/om-agents.md) |Tak |Alerty generowane przez agentów programu Operations Manager są dostarczane do grupy zarządzania, a następnie przekazywane do usługi Log Analytics.<br><br>Bezpośrednie połączenie z agentami programu Operations Manager z usługą Log Analytics nie jest wymagane. Dane alertów są przekazywane z grupy zarządzania do repozytorium usługi Log Analytics. |


### <a name="collection-frequency"></a>Częstotliwość zbierania
- Rekordy alertów są dostępne dla rozwiązania, gdy tylko są przechowywane w repozytorium.
- Dane alertów są wysyłane z grupy zarządzania programu Operations Manager do usługi Log Analytics co trzy minuty.  

## <a name="using-the-solution"></a>Użycie rozwiązania
Po dodaniu rozwiązania zarządzania alertami do obszaru roboczego usługi Log Analytics kafelek **Zarządzanie alertami** zostanie dodany do pulpitu nawigacyjnego.  Ten kafelek wyświetla liczbę i graficzną reprezentację liczby aktualnie aktywnych alertów, które zostały wygenerowane w ciągu ostatnich 24 godzin.  Nie można zmienić tego zakresu czasu.

![Kafelek Zarządzanie alertami](media/alert-management-solution/tile.png)

Kliknij kafelek **Zarządzanie alertami,** aby otworzyć pulpit nawigacyjny **zarządzania alertami.**  Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli.  Każda kolumna zawiera listę 10 najważniejszych alertów według liczby odpowiadających kryteriom tej kolumny dla określonego zakresu i zakresu czasu.  Wyszukiwanie w dzienniku, które zawiera całą listę, można uruchomić, klikając pozycję **Zobacz wszystko** u dołu kolumny lub klikając nagłówek kolumny.

| Kolumna | Opis |
|:--- |:--- |
| Alerty krytyczne |Wszystkie alerty o ważności krytycznej pogrupowane według nazwy alertu.  Kliknij nazwę alertu, aby uruchomić wyszukiwanie dziennika zwracające wszystkie rekordy dla tego alertu. |
| Alerty ostrzegawcze |Wszystkie alerty z ważnością Warning pogrupowane według nazwy alertu.  Kliknij nazwę alertu, aby uruchomić wyszukiwanie dziennika zwracające wszystkie rekordy dla tego alertu. |
| Alerty programu Active System Center Operations Manager |Wszystkie alerty zebrane z programu Operations Manager z dowolnym stanem innym niż *Zamknięte* pogrupowane według źródła, które wygenerowały alert. |
| Wszystkie aktywne alerty |Wszystkie alerty o dowolnym poziomie ważności pogrupowane według nazwy alertu. Obejmuje tylko alerty programu Operations Manager z dowolnym stanem innym niż *Zamknięte*. |

Jeśli przewiniesz w prawo, pulpit nawigacyjny zawiera listę kilku typowych zapytań, które można kliknąć, aby przeprowadzić wyszukiwanie danych alertów [w dzienniku.](../../azure-monitor/log-query/log-query-overview.md)

![Pulpit nawigacyjny zarządzania alertami](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics
Rozwiązanie do zarządzania alertami analizuje dowolny rekord za pomocą typu **Alert**.  Alerty utworzone przez usługa Log Analytics lub zebrane z Nagios lub Zabbix nie są zbierane bezpośrednio przez rozwiązanie.

Rozwiązanie importuje alerty z programu System Center Operations Manager i tworzy odpowiedni rekord dla każdego z typem **alertu** i **sourcesystemem opsmanagera**.  Te rekordy mają właściwości w poniższej tabeli:  

| Właściwość | Opis |
|:--- |:--- |
| `Type` |*Alert* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Szczegóły elementu danych, który spowodował wygenerowanie alertu w formacie XML. |
| `AlertDescription` |Szczegółowy opis wpisu. |
| `AlertId` |Identyfikator GUID alertu. |
| `AlertName` |Nazwa wpisu. |
| `AlertPriority` |Poziom priorytetu alertu. |
| `AlertSeverity` |Poziom ważności alertu. |
| `AlertState` |Najnowszy stan rozdzielczości alertu. |
| `LastModifiedBy` |Nazwa użytkownika, który ostatnio zmodyfikował alert. |
| `ManagementGroupName` |Nazwa grupy zarządzania, w której został wygenerowany alert. |
| `RepeatCount` |Liczba razy ten sam alert został wygenerowany dla tego samego monitorowanego obiektu od czasu rozwiązania. |
| `ResolvedBy` |Nazwa użytkownika, który rozpoznał alert. Puste, jeśli alert nie został jeszcze rozwiązany. |
| `SourceDisplayName` |Wyświetlana nazwa obiektu monitorującego, który wygenerował alert. |
| `SourceFullName` |Pełna nazwa obiektu monitorującego, który wygenerował alert. |
| `TicketId` |Identyfikator biletu dla alertu, jeśli środowisko programu System Center Operations Manager jest zintegrowane z procesem przypisywania biletów do alertów.  Nie jest przypisany identyfikator biletu. |
| `TimeGenerated` |Data i godzina utworzenia alertu. |
| `TimeLastModified` |Data i godzina ostatniej zmiany alertu. |
| `TimeRaised` |Data i godzina wygenerowania alertu. |
| `TimeResolved` |Data i godzina rozwiązania alertu. Puste, jeśli alert nie został jeszcze rozwiązany. |

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników
Poniższa tabela zawiera przykładowe wyszukiwania dzienników rekordów alertów zebranych przez to rozwiązanie: 

| Zapytanie | Opis |
|:---|:---|
| Alert &#124; gdzie SourceSystem == "OpsManager" i AlertSeverity == "error" i TimeRaised > temu(24h) |Alerty krytyczne podniesione w ciągu ostatnich 24 godzin |
| Alert &#124; gdzie AlertSeverity == "ostrzeżenie" i TimeRaised > temu(24h) |Alerty ostrzegawcze podniesione w ciągu ostatnich 24 godzin |
| Alert &#124; gdzie SourceSystem == "OpsManager" i AlertState != "Closed" i TimeRaised > temu(24h) &#124; summarize Count = count() by SourceDisplayName |Źródła z aktywnymi alertami wywoływane w ciągu ostatnich 24 godzin |
| Alert &#124; gdzie SourceSystem == "OpsManager" i AlertSeverity == "error" i TimeRaised > temu(24h) i AlertState != "Closed" |Alerty krytyczne podniesione w ciągu ostatnich 24 godzin, które są nadal aktywne |
| Alert &#124; gdzie SourceSystem == "OpsManager" i TimeRaised > temu(24h) i AlertState == "Zamknięte" |Alerty podniesione w ciągu ostatnich 24 godzin, które są teraz zamknięte |
| Alert &#124; gdzie SourceSystem == "OpsManager" i TimeRaised > temu(1d) &#124; podsumować Count = count() przez AlertSeverity |Alerty podniesione w ciągu ostatnich 1 dnia pogrupowane według ich nasilenia |
| Alert &#124; gdzie SourceSystem == "OpsManager" i TimeRaised > temu(1d) &#124; sortowania według RepeatCount desc |Alerty wywoływane w ciągu ostatniego dnia 1 posortowane według ich wartości liczby powtórzeń |



## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej na temat [alertów w usłudze Log Analytics](../../azure-monitor/platform/alerts-overview.md), aby poznać szczegóły generowania alertów z usługi Log Analytics.
