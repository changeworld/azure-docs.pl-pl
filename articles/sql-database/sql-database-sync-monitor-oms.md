---
title: Monitor synchronizacji danych Azure SQL (wersja zapoznawcza) z analizy dzienników | Dokumentacja firmy Microsoft
description: Informacje o sposobie monitorowania synchronizacji danych SQL Azure (wersja zapoznawcza) przy użyciu analizy dzienników
services: sql-database
ms.date: 04/01/2018
ms.topic: conceptual
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: 4c684337a904fe6984e6b5cd5cf88ef8eb976cb6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650251"
---
# <a name="monitor-sql-data-sync-preview-with-log-analytics"></a>Synchronizacja danych SQL Monitor (wersja zapoznawcza) z analizy dzienników 

Sprawdź dziennik aktywności synchronizacji danych SQL i wykrywać błędy i ostrzeżenia, należy wcześniej było ręcznie sprawdzić synchronizacji danych SQL w portalu Azure, lub użyj programu PowerShell lub interfejsu API REST. Wykonaj kroki opisane w tym artykule, aby skonfigurować niestandardowe rozwiązanie zwiększa monitorowania obsługi synchronizacji danych. Można dostosować to rozwiązanie do danego scenariusza.

Omówienie usługi SQL Data Sync zawiera temat [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Preview) (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync — wersja zapoznawcza)](sql-database-sync-data.md).

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Pulpit nawigacyjny monitorowania dla wszystkich grup synchronizacji 

Nie trzeba Przejrzyj dzienniki każdej grupy synchronizacji indywidualnie do wyszukania problemów. Wszystkie grupy synchronizacji można monitorować za pomocą dowolnego z subskrypcji w jednym miejscu, za pomocą widok niestandardowy analizy dzienników. Ten widok udostępnia informacje, które ma znaczenie klientom synchronizacji danych SQL.

![Pulpitu nawigacyjnego monitorowania synchronizacji danych](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Automatyczne powiadomienia E-mail

Nie trzeba sprawdzić dziennik ręcznie w portalu Azure lub za pomocą programu PowerShell lub interfejsu API REST. Z [analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), można tworzyć alerty, które go bezpośrednio na adresy e-mail osób, którym chcesz wyświetlać je po wystąpieniu błędu.

![Powiadomienia e-mail synchronizacji danych](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Jak można skonfigurować te funkcje monitorowania? 

Implementowanie niestandardowych analizy dzienników monitorowania rozwiązania synchronizacji danych SQL w mniej niż godzinę, wykonując następujące czynności:

Należy skonfigurować trzy składniki:

-   Element runbook programu PowerShell ze źródłem danych dziennika SQL synchronizacji danych do analizy dzienników.

-   Alert analizy dzienników dla powiadomień e-mail.

-   Widok analizy dziennika do monitorowania.

### <a name="samples-to-download"></a>Przykłady do pobrania

Pobierz następujące dwa przykłady:

-   [Runbook programu PowerShell w dzienniku synchronizacji danych](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Widok analizy dziennika synchronizacji danych](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że po skonfigurowaniu następujących czynności:

-   Konto usługi Automatyzacja Azure

-   Obszar roboczy usługi Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Runbook programu PowerShell, aby uzyskać w dzienniku synchronizacji danych SQL 

Element runbook programu PowerShell hostowanych w usłudze Automatyzacja Azure umożliwia ściągania dziennika SQL synchronizacji danych i wysyłania go do analizy dzienników. Przykładowy skrypt jest dołączony. Warunkiem wstępnym musisz mieć konto usługi Automatyzacja Azure. Następnie należy utworzyć element runbook i zaplanuj jego uruchomienie. 

### <a name="create-a-runbook"></a>Tworzenie elementu runbook

Aby uzyskać więcej informacji na temat tworzenia elementu runbook, zobacz [Moje pierwszego elementu runbook programu PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Przy użyciu tego konta usługi Automatyzacja Azure, wybierz **elementów Runbook** kartę w obszarze automatyzacji procesu.

2.  Wybierz **Dodaj element Runbook** w lewym górnym rogu strony elementów Runbook.

3.  Wybierz **importowanie istniejącego elementu Runbook**.

4.  W obszarze **pliku Runbook**, użyj danego `DataSyncLogPowerShellRunbook` pliku. Ustaw **typ elementu Runbook** jako `PowerShell`. Nadaj nazwę elementu runbook.

5.  Wybierz pozycję **Utwórz**. Istnieje już element runbook.

6.  W obszarze Twoje konto usługi Automatyzacja Azure, wybierz **zmienne** kartę w obszarze udostępnionych zasobów.

7.  Wybierz **dodać zmienną** na zmienne. Utwórz zmienną do przechowywania ostatni czas wykonania elementu runbook. Jeśli masz wiele elementów runbook należy jedną zmienną dla każdego elementu runbook.

8.  Ustaw nazwę zmiennej jako `DataSyncLogLastUpdatedTime` i ustaw jej typ jako element DateTime.

9.  Wybierz element runbook, a następnie kliknij przycisk Edytuj w górnej części strony.

10. Zmiany wymagane do konta i konfiguracji synchronizacji danych SQL. (Aby uzyskać szczegółowe informacje, zobacz przykładowy skrypt).

    1.  Azure informacje.

    2.  Informacje o synchronizacji grupy.

    3.  Informacje o OMS. Te informacje można znaleźć w portalu OMS | Ustawienia | Połączonych źródeł. Aby uzyskać więcej informacji na temat wysyłania danych do analizy dzienników, zobacz [wysyłania danych do analizy dzienników przy użyciu protokołu HTTP danych modułu zbierającego interfejsu API (wersja zapoznawcza)](../log-analytics/log-analytics-data-collector-api.md).

11. Uruchom element runbook w okienku testu. Sprawdź, czy było pomyślne.

    Jeśli masz błędy, upewnij się, że masz najnowszą zainstalowany moduł programu PowerShell. Można zainstalować moduł PowerShell najnowsze w **Galeria modułów** na Twoim koncie automatyzacji.

12. Kliknij przycisk **publikowania**

### <a name="schedule-the-runbook"></a>Planowanie elementu runbook

Aby zaplanować elementu runbook:

1.  W obszarze elementu runbook, zaznacz **harmonogramy** kartę w obszarze zasobów.

2.  Wybierz **Dodaj harmonogram** na stronie harmonogramów.

3.  Wybierz **Połącz harmonogram z elementem runbook**.

4.  Wybierz **Utwórz nowy harmonogram.**

5.  Ustaw **cyklu** cykliczny i ustaw interwał ma. Użyj tego samego interwału, skrypt i OMS.

6.  Wybierz pozycję **Utwórz**.

### <a name="check-the-automation"></a>Sprawdź automatyzacji

Można monitorować, czy Twoje automatyzacji działa zgodnie z oczekiwaniami, w obszarze **omówienie** Twojego konta automatyzacji, można znaleźć **statystyki zadania** wyświetlić w obszarze **monitorowanie**. Przypnij ten widok do pulpitu nawigacyjnego w celu łatwego wyświetlania. Uruchamia się pomyślnie Pokaż runbook jako "Ukończone" i działa wyświetlane jako "Nieudane".

## <a name="create-a-log-analytics-reader-alert-for-email-notifications"></a>Utwórz Alert czytnika Analytics dziennik powiadomień E-mail

Aby utworzyć alert, który używa analizy dzienników, wykonaj następujące czynności. Warunkiem wstępnym musisz mieć analizy dzienników połączone z obszaru roboczego analizy dzienników.

1.  W portalu OMS wybierz **wyszukiwania dziennika**.

2.  Utwórz zapytanie, aby wybrać błędy i ostrzeżenia przez grupę synchronizacji z okresu wybranego. Na przykład:

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  Po uruchomieniu kwerendy, wybierz informacją dzwonka **alertu**.

4.  W obszarze **Generowanie alertu na podstawie**, wybierz pozycję **metryki pomiaru**.

    1.  Ustawiono wartość zagregowaną **większa niż**.

    2.  Po **większa niż**, wprowadź wartość progową, którym chcesz otrzymywać powiadomienia. Oczekiwano błędów przejściowych w synchronizacji danych. Do zmniejszenia szumu, należy ustawić próg na wartość 5.

5.  W obszarze **akcje**ustaw **powiadomienia E-mail** "Yes". Wprowadź adresatów żądanej wiadomości e-mail.

6.  Kliknij pozycję **Zapisz**. Podanych odbiorców teraz otrzymywać powiadomienia pocztą e-mail, jeśli wystąpią błędy.

## <a name="create-an-oms-view-for-monitoring"></a>Tworzenie widoku OMS do monitorowania

Spowoduje to utworzenie widoku OMS wizualnie monitorowania wszystkich grup synchronizacji określony. Widok zawiera kilka składników:

-   Kafelka przeglądu, który pokazuje liczbę błędów, sukcesów i ostrzeżenia mają wszystkich grup synchronizacji.

-   Kafelek dla wszystkich grup synchronizacji, który informuje o liczbie błędów i ostrzeżeń dla każdej grupy synchronizacji. Grupy bez problemów nie są wyświetlane dla tego kafelka.

-   Kafelek dla każdej grupy synchronizacji pokazuje liczbę błędów, sukcesów i ostrzeżenia i ostatnie komunikaty o błędach.

Aby skonfigurować widok OMS, wykonaj następujące czynności:

1.  Na stronie głównej OMS wybierz plus po lewej stronie, aby otworzyć **Widok projektanta**.

2.  Wybierz **importu** na górnym pasku projektanta widoku. Następnie wybierz plik przykładowy "DataSyncLogOMSView".

3.  Przykładowy widok jest zarządzania dwie grupy synchronizacji. Edytuj ten widok, aby dopasować danego scenariusza. Kliknij przycisk **Edytuj** i wprowadź następujące zmiany:

    1.  Utwórz nowe obiekty "Lista & pierścień" z galerii zgodnie z potrzebami.

    2.  W każdej kafelka aktualizacji zapytania z informacjami.

        1.  W każdym segmencie zmienić interwał TimeStamp_t zgodnie z potrzebami.

        2.  Na Kafelki dla każdej grupy synchronizacji zaktualizuj nazwy grupy synchronizacji.

    3.  W każdym segmencie Zaktualizuj tytuł zgodnie z potrzebami.

4.  Kliknij przycisk **zapisać** i widoku jest gotowy.

## <a name="cost-of-this-solution"></a>Koszt tego rozwiązania

W większości przypadków to rozwiązanie jest bezpłatna.

**Automatyzacja Azure:** może być pewnych kosztów ponoszonych z kontem usługi Automatyzacja Azure, w zależności od użycie. 500 pierwszych minut zadanie było uruchamiane raz w każdym miesiącu jest bezpłatne. W większości przypadków to rozwiązanie powinien użyć mniej niż 500 minut miesięcznie. Aby uniknąć naliczania opłat, Zaplanuj uruchomienie elementu runbook w interwału dwie lub więcej godzin. Aby uzyskać więcej informacji, zobacz [cennik automatyzacji](https://azure.microsoft.com/pricing/details/automation/).

**Analiza dzienników:** może być skojarzony z analizy dzienników w zależności od użycie koszt. Warstwa bezpłatna obejmuje 500 MB danych pozyskiwane na dzień. W większości przypadków to rozwiązanie ma pozyskiwania mniej niż 500 MB na dzień. Aby zmniejszyć użycie, użyj tylko do awarii filtrowanie zawarte w elemencie runbook. Jeśli używasz więcej niż 500 MB na dzień, Uaktualnij do planu płatną, aby uniknąć zagrożenia analytics zatrzymywanie po osiągnięciu ograniczenia. Aby uzyskać więcej informacji, zobacz [cennik analizy dzienników](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Przykłady kodu

Pobierz przykłady kodu, opisane w tym artykule w następujących lokalizacjach:

-   [Runbook programu PowerShell w dzienniku synchronizacji danych](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Widok analizy dziennika synchronizacji danych](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat usługi SQL Data Sync, zobacz:

-   [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](sql-database-sync-data.md)
-   [Set up Azure SQL Data Sync (Konfigurowanie usługi Azure SQL Data Sync)](sql-database-get-started-sql-data-sync.md)
-   [Best practices for Azure SQL Data Sync (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)](sql-database-best-practices-data-sync.md)
-   [Troubleshoot issues with Azure SQL Data Sync (Rozwiązywanie problemów z usługą Azure SQL Data Sync)](sql-database-troubleshoot-data-sync.md)

-   Pełne przykładowe skrypty programu PowerShell przedstawiające sposób konfigurowania usługi SQL Data Sync:
    -   [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Pobierz dokumentację interfejsu API REST usługi SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Aby uzyskać więcej informacji na temat usługi SQL Database, zobacz:

-   [Omówienie usługi SQL Database](sql-database-technical-overview.md)
-   [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
