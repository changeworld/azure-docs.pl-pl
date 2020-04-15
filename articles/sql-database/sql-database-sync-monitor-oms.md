---
title: Monitorowanie synchronizacji danych SQL za pomocą dzienników usługi Azure Monitor
description: Dowiedz się, jak monitorować synchronizację danych SQL usługi Azure przy użyciu dzienników usługi Azure Monitor
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 5f5980f74b24cd972d43e9b05d4a5d623e6e3d2f
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383703"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Monitorowanie synchronizacji danych SQL za pomocą dzienników usługi Azure Monitor 

Aby sprawdzić dziennik aktywności synchronizacji danych SQL i wykryć błędy i ostrzeżenia, wcześniej trzeba było ręcznie sprawdzić synchronizację danych SQL w witrynie Azure portal lub użyć programu PowerShell lub interfejsu API REST. Wykonaj kroki opisane w tym artykule, aby skonfigurować niestandardowe rozwiązanie, które usprawnia monitorowanie synchronizacji danych. Można dostosować to rozwiązanie, aby dopasować swój scenariusz.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Omówienie usługi SQL Data Sync zawiera temat [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](sql-database-sync-data.md).

> [!IMPORTANT]
> Usługa Azure SQL Data Sync **nie** obsługuje obecnie wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Pulpit nawigacyjny monitorowania dla wszystkich grup synchronizacji 

Nie trzeba już przeglądać dzienniki każdej grupy synchronizacji indywidualnie, aby wyszukać problemy. Możesz monitorować wszystkie grupy synchronizacji z dowolnej subskrypcji w jednym miejscu przy użyciu niestandardowego widoku usługi Azure Monitor. Ten widok powierzchni informacji, które mają znaczenie dla klientów SQL Data Sync.

![Pulpit nawigacyjny monitorowania synchronizacji danych](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Automatyczne powiadomienia e-mail

Nie trzeba już ręcznie sprawdzać dziennika w witrynie Azure portal lub za pośrednictwem programu PowerShell lub interfejsu API REST. Za pomocą [dzienników usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)można tworzyć alerty, które są bezpośrednio adresy e-mail osób, które muszą je zobaczyć, gdy wystąpi błąd.

![Powiadomienia e-mail o synchronizacji danych](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Jak skonfigurować te funkcje monitorowania? 

Zaimplementuj niestandardowe rozwiązanie do monitorowania dzienników usługi Azure Monitor dla synchronizacji danych SQL w mniej niż godzinę, wykonując następujące czynności:

Należy skonfigurować trzy składniki:

-   Zestaw runbook programu PowerShell do przekazywania danych dziennika synchronizacji danych SQL do dzienników usługi Azure Monitor.

-   Alert usługi Azure Monitor dla powiadomień e-mail.

-   Widok monitora platformy Azure do monitorowania.

### <a name="samples-to-download"></a>Przykłady do pobrania

Pobierz następujące dwie próbki:

-   [Dziennik synchronizacji danych PowerShell Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Widok monitora usługi Azure synchronizacji danych](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że skonfigurowałeś następujące czynności:

-   Konto usługi Azure Automation

-   Obszar roboczy usługi Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Podręcznik runbook programu PowerShell w celu uzyskania dziennika synchronizacji danych SQL 

Użyj podręcznika runbook programu PowerShell hostowanego w usłudze Azure Automation, aby pobierać dane dziennika synchronizacji danych SQL i wysyłać je do dzienników usługi Azure Monitor. Przykładowy skrypt jest dołączony. Jako warunek wstępny musisz mieć konto usługi Azure Automation. Następnie należy utworzyć runbook i zaplanować jego uruchomienie. 

### <a name="create-a-runbook"></a>Tworzenie elementu runbook

Aby uzyskać więcej informacji na temat tworzenia eksmisji, zobacz [Mój pierwszy program RunBook programu PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  W obszarze konta usługi Azure Automation wybierz kartę **Elementy runbook w** obszarze Automatyzacja procesów.

2.  Wybierz **pozycję Dodaj księgę ekstyj w** lewym górnym rogu strony Runbooks.

3.  Wybierz **pozycję Importuj istniejący element ekwiduj**.

4.  W obszarze **Plik runbooka**użyj danego `DataSyncLogPowerShellRunbook` pliku. Ustaw **typ eksletu** jako `PowerShell`. Nadaj projektowi runbookowi nazwę.

5.  Wybierz **pozycję Utwórz**. Masz teraz runbook.

6.  W obszarze Konto usługi Azure Automation wybierz kartę **Zmienne** w obszarze Zasoby udostępnione.

7.  Wybierz **pozycję Dodaj zmienną** na stronie Zmienne. Utwórz zmienną do przechowywania ostatniego czasu wykonywania dla ego księgi runbook. Jeśli masz wiele śmięty, potrzebujesz jednej zmiennej dla każdego śmiwoja.

8.  Ustaw nazwę zmiennej jako `DataSyncLogLastUpdatedTime` i ustaw jej typ jako DateTime.

9.  Wybierz projekt runbooka i kliknij przycisk edycji u góry strony.

10. Wprowadzać zmiany wymagane dla konta i konfiguracji synchronizacji danych SQL. (Aby uzyskać bardziej szczegółowe informacje, zobacz przykładowy skrypt).

    1.  Informacje o platformie Azure.

    2.  Synchronizowanie informacji o grupie.

    3.  Usługa Azure Monitor rejestruje informacje. Znajdź te informacje w witrynie Azure Portal | Ustawienia | Połączone źródła. Aby uzyskać więcej informacji na temat wysyłania danych do dzienników usługi Azure Monitor, zobacz [Wysyłanie danych do dzienników usługi Azure Monitor za pomocą interfejsu API modułu zbierającego dane HTTP (wersja zapoznawcza)](../azure-monitor/platform/data-collector-api.md).

11. Uruchom program runbook w okienku testowym. Sprawdź, czy to się udało.

    Jeśli masz błędy, upewnij się, że masz zainstalowany najnowszy moduł programu PowerShell. Możesz zainstalować najnowszy moduł programu PowerShell w **Galerii modułów** na swoim koncie automatyzacji.

12. Kliknij **pozycję Opublikuj**

### <a name="schedule-the-runbook"></a>Planowanie uruchomieniu

Aby zaplanować program runbook:

1.  W obszarze runbook wybierz kartę **Harmonogramy** w obszarze Zasoby.

2.  Wybierz pozycję **Dodaj harmonogram** na stronie Harmonogramy.

3.  Wybierz **pozycję Połącz harmonogram z schematem runbook .**

4.  Wybierz **pozycję Utwórz nowy harmonogram.**

5.  Ustaw **cykl** na cykliczny i ustaw odpowiedni interwał. Użyj tego samego interwału w tym miejscu, w skrypcie i w dziennikach usługi Azure Monitor.

6.  Wybierz **pozycję Utwórz**.

### <a name="check-the-automation"></a>Sprawdź automatyzację

Aby monitorować, czy automatyzacja działa zgodnie z oczekiwaniami, w obszarze **Omówienie** konta automatyzacji znajdź widok **Statystyka zadań** w obszarze **Monitorowanie**. Przypnij ten widok do pulpitu nawigacyjnego, aby ułatwić przeglądanie. Pomyślne przebiegi programu runbook jako "Zakończone" i nieudane przebiegi są wyświetlane jako "Nieudane".

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Tworzenie alertu czytnika usługi Azure Monitor dla powiadomień e-mail

Aby utworzyć alert, który używa dzienników usługi Azure Monitor, wykonaj następujące czynności. Jako warunek wstępny musisz mieć dzienniki usługi Azure Monitor połączone z obszarem roboczym usługi Log Analytics.

1.  W portalu Azure wybierz pozycję **Wyszukiwanie dzienników**.

2.  Utwórz kwerendę, aby wybrać błędy i ostrzeżenia według grupy synchronizacji w wybranym przedziale czasu. Przykład:

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  Po uruchomieniu kwerendy wybierz dzwonek z **napisem Alert**.

4.  W obszarze **Generowanie alertu na podstawie**wybierz opcję **Pomiar metryki**.

    1.  Ustaw wartość agregacji na **większą niż**.

    2.  Po **przekroczeniem wartości**powyżej wprowadź próg, który ma uginieć się przed otrzymaniem powiadomień. Błędy przejściowe są oczekiwane w synchronizacji danych. Aby zmniejszyć hałas, ustaw próg na 5.

5.  W obszarze **Akcje**ustaw **powiadomienie e-mail** na "Tak". Wprowadź żądanych adresatów wiadomości e-mail.

6.  Kliknij pozycję **Zapisz**. Określona adresaci otrzymują teraz powiadomienia e-mail, gdy wystąpią błędy.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Tworzenie widoku monitora platformy Azure do monitorowania

Ten krok tworzy widok usługi Azure Monitor, aby wizualnie monitorować wszystkie określone grupy synchronizacji. Widok zawiera kilka składników:

-   Kafelek przeglądu, który pokazuje, ile błędów, sukcesów i ostrzeżeń mają wszystkie grupy synchronizacji.

-   Kafelek dla wszystkich grup synchronizacji, który pokazuje liczbę błędów i ostrzeżeń na grupę synchronizacji. Grupy bez problemów nie są wyświetlane na tym kafelku.

-   Kafelek dla każdej grupy synchronizacji, który pokazuje liczbę błędów, sukcesów i ostrzeżeń oraz ostatnie komunikaty o błędach.

Aby skonfigurować widok Monitora platformy Azure, wykonaj następujące czynności:

1.  Na stronie głównej obszaru roboczego usługi Log Analytics wybierz plus po lewej stronie, aby otworzyć **projektanta widoku**.

2.  Wybierz **pozycję Importuj** na górnym pasku projektanta widoku. Następnie wybierz przykładowy plik "DataSyncLogOMSView".

3.  Przykładowy widok służy do zarządzania dwiema grupami synchronizacji. Edytuj ten widok, aby dopasować go do scenariusza. Kliknij **edytuj** i wytwórz następujące zmiany:

    1.  W razie potrzeby utwórz nowe obiekty "Lista pączków &" z galerii.

    2.  W każdym kafelku zaktualizuj zapytania o informacje.

        1.  Na każdym kafelku zmień TimeStamp_t interwał zgodnie z potrzebami.

        2.  Na kafelkach dla każdej grupy synchronizacji zaktualizuj nazwy grup synchronizacji.

    3.  Na każdym kafelku zaktualizuj tytuł w razie potrzeby.

4.  Kliknij **przycisk Zapisz,** a widok będzie gotowy.

## <a name="cost-of-this-solution"></a>Koszt tego rozwiązania

W większości przypadków to rozwiązanie jest bezpłatne.

**Automatyzacja platformy Azure:** W zależności od użycia może to być koszt poniesiony na koncie usługi Azure Automation. Pierwsze 500 minut czasu wykonywania zadania w miesiącu jest bezpłatne. W większości przypadków oczekuje się, że to rozwiązanie będzie używać mniej niż 500 minut miesięcznie. Aby uniknąć opłat, zaplanuj uruchomienie ć w odstępie co najmniej dwóch godzin. Aby uzyskać więcej informacji, zobacz [Ustalanie cen automatyzacji](https://azure.microsoft.com/pricing/details/automation/).

**Dzienniki usługi Azure Monitor:** Może to być koszt skojarzony z dziennikami usługi Azure Monitor w zależności od użycia. Warstwa bezpłatna zawiera 500 MB połkniętych danych dziennie. W większości przypadków oczekuje się, że to rozwiązanie pochłonie mniej niż 500 MB dziennie. Aby zmniejszyć użycie, należy użyć filtrowania tylko dla awarii zawartego w żyłaku. Jeśli używasz więcej niż 500 MB dziennie, uaktualnij do warstwy płatnej, aby uniknąć ryzyka zatrzymania analityki po osiągnięciu ograniczenia. Aby uzyskać więcej informacji, zobacz [Azure Monitor dzienniki cen](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Przykłady kodu

Pobierz przykłady kodu opisane w tym artykule z następujących lokalizacji:

-   [Dziennik synchronizacji danych PowerShell Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Widok monitora usługi Azure synchronizacji danych](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat usługi SQL Data Sync, zobacz:

-   Omówienie — [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync](sql-database-sync-data.md) (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)
-   Konfigurowanie synchronizacji danych
    - W portalu — [Tutorial: Set up SQL Data Sync to sync data between Azure SQL Database and SQL Server on-premises](sql-database-get-started-sql-data-sync.md) (Samouczek: konfigurowanie usługi SQL Data Sync w celu synchronizowania danych między usługą Azure SQL Database i lokalnym programem SQL Server)
    - Z programem PowerShell
        -  [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL Database i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agent synchronizacji danych — [Data Sync Agent for Azure SQL Data Sync](sql-database-data-sync-agent.md) (Agent synchronizacji danych dla usługi Azure SQL Data Sync)
-   Najlepsze rozwiązania — [Best practices for Azure SQL Data Sync](sql-database-best-practices-data-sync.md) (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)
-   Rozwiązywanie problemów — [Troubleshoot issues with Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md) (Rozwiązywanie problemów z usługą Azure SQL Data Sync)
-   Aktualizowanie schematu synchronizacji
    -   Za pomocą języka Transact-SQL — [Automate the replication of schema changes in Azure SQL Data Sync](sql-database-update-sync-schema.md) (Automatyzowanie replikacji zmian schematu w usłudze Azure SQL Data Sync)
    -   Używanie programu PowerShell — [Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji](scripts/sql-database-sync-update-schema.md)

Aby uzyskać więcej informacji na temat usługi SQL Database, zobacz:

-   [Omówienie usługi SQL Database](sql-database-technical-overview.md)
-   [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
