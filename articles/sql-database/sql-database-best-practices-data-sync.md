---
title: Najlepsze rozwiązania dotyczące synchronizacji danych SQL Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat najlepszych rozwiązań do konfigurowania i uruchamiania synchronizacji danych SQL Azure.
services: sql-database
ms.date: 04/01/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: xiwu
manager: craigg
ms.openlocfilehash: b53c72f1df4f2fc2509d91220d08aff4682b6620
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025822"
---
# <a name="best-practices-for-sql-data-sync"></a>Najlepsze rozwiązania dotyczące funkcji SQL Data Sync 

W tym artykule opisano najlepsze rozwiązania dla synchronizacji danych SQL Azure.

Omówienie synchronizacji danych SQL, zobacz [synchronizacji danych między wieloma bazami danych chmury i lokalnych z synchronizacji danych SQL Azure](sql-database-sync-data.md).

## <a name="security-and-reliability"></a> Bezpieczeństwo i niezawodność

### <a name="client-agent"></a>Agent klienta

-   Zainstaluj agenta klienta przy użyciu najmniej uprzywilejowane konta użytkownika, który ma dostęp do usługi sieci.  
-   Zainstaluj agenta klienta na komputerze, który nie jest lokalnym komputera serwera SQL.  
-   Nie można zarejestrować lokalną bazą danych z więcej niż jednego agenta.    
    -   Unikaj, nawet wtedy, gdy trwa synchronizacja różnych tabel dla różnych synchronizacji grupy.  
    -   Rejestrowanie lokalną bazą danych w wiele wyzwań stanowi agentów klientów po usunięciu jednej z grup synchronizacji.

### <a name="database-accounts-with-least-required-privileges"></a>Bazy danych konta z najmniejszymi uprawnieniami wymagany

-   **Synchronizacja ustawień**. Utwórz Alter Table; Zmiany bazy danych; Utwórz procedurę; Wybierz opcję / Alter Schema; Utwórz typ zdefiniowany przez użytkownika.

-   **Trwającej synchronizacji**. Wybierz / wstawiania / aktualizacji / usuwania w tabelach, które są wybrane do synchronizacji i synchronizacji metadanych i tabele; śledzenia Uprawnienia do wykonywania na procedury składowane są tworzone przez usługę; Uprawnienia do wykonywania w typach tabel zdefiniowanych przez użytkownika.

-   **W przypadku anulowania obsługi**. Zmiany w tabelach część synchronizacji; Zaznacz / Usuń w tabelach metadane synchronizacji; Kontrola na synchronizacji śledzenia tabele, procedury składowane i typy danych zdefiniowane przez użytkownika.

Baza danych SQL Azure obsługuje tylko jednego zestawu poświadczeń. Aby wykonać te zadania w ramach tego ograniczenia, należy wziąć pod uwagę następujące opcje:

-   Zmiana poświadczeń dla różnych etapów (na przykład *credentials1* instalacji i *credentials2* dla trwających).  
-   Zmiana uprawnień poświadczenia (to znaczy, Zmień uprawnienia po skonfigurowaniu synchronizacji).

## <a name="setup"></a>Konfigurowanie

### <a name="database-considerations-and-constraints"></a> Zagadnienia dotyczące bazy danych i ograniczenia

#### <a name="sql-database-instance-size"></a>Rozmiar wystąpienia bazy danych SQL

Podczas tworzenia nowego wystąpienia bazy danych SQL, Ustaw maksymalny rozmiar, aby zawsze była większa niż baza danych, którą można wdrożyć. Jeśli nie ustawisz maksymalny rozmiar do większych niż wdrożonej bazy danych synchronizacji kończy się niepowodzeniem. Mimo że synchronizacja danych SQL nie oferują automatyczne zwiększanie rozmiaru, możesz uruchomić `ALTER DATABASE` polecenie, aby zwiększyć rozmiar bazy danych, po jego utworzeniu. Upewnij się, że pozostać w granicach rozmiar wystąpienia bazy danych SQL.

> [!IMPORTANT]
> Synchronizacja danych SQL przechowuje dodatkowe metadane z każdej bazy danych. Upewnij się, że zostało uwzględnione metadanych podczas obliczania potrzebne miejsce. Ilość dodane obciążenia jest powiązana z szerokość tabele (na przykład wąskie tabele wymagają większe obciążenie) i ilość ruchu sieciowego.

### <a name="table-considerations-and-constraints"></a> Zagadnienia dotyczące tabeli i ograniczenia

#### <a name="selecting-tables"></a>Wybieranie tabel

Nie masz uwzględnić wszystkie tabele w bazie danych w grupie synchronizacji. Tabele, które obejmują grupy synchronizacji mają wpływ na wydajność i koszty. Zawiera tabele i tabele, które są zależne, w grupie synchronizacji tylko wtedy, gdy wymaga się potrzeb biznesowych.

#### <a name="primary-keys"></a>Klucze podstawowe

Każda tabela w grupie synchronizacji musi mieć klucz podstawowy. Usługa synchronizacji danych SQL nie można zsynchronizować tabelę, która nie ma klucza podstawowego.

Przed użyciem synchronizacji danych SQL w środowisku produkcyjnym należy przetestować wydajność wstępnych i bieżących synchronizacji.

### <a name="provisioning-destination-databases"></a> Inicjowanie obsługi administracyjnej docelowej bazy danych

Synchronizacja danych SQL zawiera autoprovisioning podstawowej bazy danych.

W tej sekcji omówiono ograniczenia obsługi synchronizacji danych SQL.

#### <a name="autoprovisioning-limitations"></a>Ograniczenia Autoprovisioning

Synchronizacja danych SQL ma następujące ograniczenia na autoprovisioning:

-   Wybierz kolumny, które są tworzone w tabeli docelowej.  
    Kolumn, które nie należą do grupy synchronizacji nie są udostępniane w tabeli docelowej.
-   Indeksy są tworzone tylko dla wybranych kolumn.  
    Jeśli indeks tabeli źródła zawiera kolumny, które nie należą do grupy synchronizacji, te indeksy nie są udostępniane w tabeli docelowej.  
-   Indeksy w kolumnach typu XML nie są udostępniane.  
-   Sprawdź, czy ograniczenia nie są udostępniane.  
-   Istniejące wyzwalacze w tabelach źródła nie jest zainicjowana.  
-   Widoków i procedur składowanych nie są tworzone w docelowej bazie danych.

#### <a name="recommendations"></a>Zalecenia

-   Możliwości autoprovisioning synchronizacji danych SQL należy używać tylko wtedy, gdy okaże się, usługa.  
-   W środowisku produkcyjnym należy udostępnić schemat bazy danych.

### <a name="locate-hub"></a> Gdzie można znaleźć bazy danych Centrum

#### <a name="enterprise-to-cloud-scenario"></a>Scenariusz przedsiębiorstwa do chmury

Aby zminimalizować czas oczekiwania, pozostawienia bazy danych Centrum bliski największy koncentracja ruchu bazy danych grupy synchronizacji.

#### <a name="cloud-to-cloud-scenario"></a>Scenariusz chmury do chmury

-   Gdy wszystkie bazy danych w grupie synchronizacji znajdują się w jednym centrum danych, koncentratora powinien znajdować się w tym samym centrum danych. Ta konfiguracja zmniejsza opóźnienia i koszty transferu danych między centrami danych.
-   W przypadku baz danych w grupie synchronizacji w wielu centrach danych, koncentratora powinien znajdować się w tym samym centrum danych, ponieważ większość baz danych i ruchu bazy danych.

#### <a name="mixed-scenarios"></a>Scenariusze mieszanych

Zastosuj wytycznymi powyższych konfiguracji grupy synchronizacji złożonych, takie jak te, które są różnych scenariuszy przedsiębiorstwa z chmurą i chmury z chmurą.

## <a name="sync"></a>Sync

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a> Unikaj powolne i kosztowne synchronizacji początkowej

W tej sekcji omówiono synchronizacji początkowej synchronizacji grupy. Dowiedz się, jak zapobiegać synchronizacji początkowej z wydłużenie i jest droższy niż jest to konieczne.

#### <a name="how-initial-sync-works"></a>Jak początkowej działania synchronizacji

Podczas tworzenia grupy synchronizacji, należy uruchomić z danymi w bazie danych tylko jeden. Jeśli masz dane w wielu baz danych synchronizacji danych SQL traktuje każdy wiersz jako konfliktu, który musi zostać rozpoznane. To rozwiązanie konfliktu powoduje, że synchronizacji początkowej do wolno go. Jeśli masz dane w wielu baz danych początkowej synchronizacji może potrwać od kilku dni i kilka miesięcy, w zależności od rozmiaru bazy danych.

W przypadku baz danych w różnych centrach danych, każdy wiersz musi przejść między różnych centrach danych. Powoduje to zwiększenie wartości początkowej synchronizacji.

#### <a name="recommendation"></a>Zalecenie

Jeśli to możliwe należy rozpocząć od danych tylko jednej grupy synchronizacji baz danych.

### <a name="design-to-avoid-synchronization-loops"></a> Projekt, aby uniknąć tworzenia pętli synchronizacji

Pętla synchronizacji występuje, gdy istnieją odwołania cykliczne w obrębie grupy synchronizacji. W tym scenariuszu każdej zmiany w jednej bazy danych jest nieskończoność i rekurencyjnie replikowana przy użyciu baz danych w grupie synchronizacji.   

Upewnij się, należy unikać pętle synchronizacji, ponieważ powodować spadku wydajności i może znacznie zwiększyć koszty.

### <a name="handling-changes-that-fail-to-propagate"></a> Nie można propagować zmian

#### <a name="reasons-that-changes-fail-to-propagate"></a>Przyczyn, które zmiany nie można propagować

Zmiany może się nie powieść propagację dla jednego z następujących powodów:

-   Niezgodność schematu/typu danych.
-   Wstawianie w kolumny niedopuszczające wartości null.
-   Naruszenie ograniczenia klucza obcego.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Co się stanie w przypadku zmiany nie można propagować?

-   Synchronizowanie pokazuje grupy, które znajduje się w **ostrzeżenie** stanu.
-   Szczegółowe informacje są wyświetlane w portalu przeglądarka dzienników interfejsu użytkownika.
-   Jeśli problem nie zostanie rozwiązany w ciągu 45 dni, bazy danych stanie się nieaktualna.

> [!NOTE]
> Te zmiany nie są propagowane. Jedynym sposobem odzyskania w tym scenariuszu są ponownie utworzyć grupę synchronizacji.

#### <a name="recommendation"></a>Zalecenie

Monitorowanie kondycji grupy i bazy danych synchronizacji regularnie za pomocą interfejsu portalu i dziennika.


## <a name="maintenance"></a>Konserwacja

### <a name="avoid-out-of-date-databases-and-sync-groups"></a> Unikaj nieaktualne baz danych i synchronizacji grupy

Grupy synchronizacji lub bazy danych w grupie synchronizacji może stać się nieaktualne. Gdy stan grupy synchronizacji jest **nieaktualne**, przestanie działać. Gdy stan bazy danych jest **nieaktualne**, dane mogą zostać utracone. Zaleca się uniknąć tego scenariusza, zamiast w trakcie odzyskiwania z niego.

#### <a name="avoid-out-of-date-databases"></a>Unikaj nieaktualne baz danych

Stan bazy danych jest ustawiony na **nieaktualne** gdy było w trybie offline przez co najmniej 45 dni. Aby uniknąć **nieaktualne** stan w bazie danych, upewnij się, że żaden z bazy danych nie jest w trybie offline przez co najmniej 45 dni.

#### <a name="avoid-out-of-date-sync-groups"></a>Unikaj grupy nieaktualne synchronizacji

Stan grupy synchronizacji jest ustawiony na **nieaktualne** po każdej zmianie grupy synchronizacji nie powiedzie się propagowane do pozostałych grupy synchronizacji 45 dni lub więcej. Aby uniknąć **nieaktualne** stanu grupy synchronizacji, regularnie sprawdzać Dziennik historii grupy synchronizacji. Upewnij się, że wszystkie konflikty zostały rozwiązane, oraz czy zmiany pomyślnie są propagowane w całej bazy danych grupy synchronizacji.

Grupy synchronizacji może zakończyć się niepowodzeniem do zastosowania zmiany dla jednego z następujących powodów:

-   Niezgodność schematu między tabelami.
-   Niezgodność danych między tabelami.
-   Wstawienie wiersza z wartością null w kolumnie, który nie dopuszcza wartości null.
-   Aktualizowanie wiersz z wartością, która narusza ograniczenie klucza obcego.

Aby uniknąć grupy nieaktualne synchronizacji:

-   Aktualizacja schematu pozwala na określenie wartości, które znajdują się w wierszach nie powiodło się.
-   Zaktualizuj wartości klucza obcego do uwzględnienia wartości, które znajdują się w wierszach nie powiodło się.
-   Zaktualizuj wartości danych w wierszu nie powiodło się, aby były zgodne z schematu lub klucze obce w docelowej bazie danych.

### <a name="avoid-deprovisioning-issues"></a> Unikaj anulowania obsługi problemów

W niektórych sytuacjach wyrejestrowywania bazy danych przy użyciu agenta klienta może spowodować synchronizację, aby zakończyć się niepowodzeniem.

#### <a name="scenario"></a>Scenariusz

1. Synchronizacji grupy A został utworzony przy użyciu wystąpienia bazy danych SQL i lokalnej bazy danych SQL Server, który jest skojarzony z lokalnego agenta 1.
2. Tę samą bazę danych lokalnych jest zarejestrowany agent lokalny 2 (tego agenta nie jest skojarzony z żadną inną grupą synchronizacji).
3. Wyrejestrowywanie lokalną bazą danych z lokalnego agenta 2 Usuwa śledzenia i tabel metadanych dla synchronizacji A grupy lokalnej bazy danych.
4. Synchronizacji grupy niepowodzenie operacji, z powodu następującego błędu: "bieżący nie można ukończyć operacji ponieważ bazy danych nie jest przeznaczona do synchronizacji lub nie masz uprawnień do tabel konfiguracji synchronizacji."

#### <a name="solution"></a>Rozwiązanie

Aby temu zapobiec, nie zarejestrować bazy danych z więcej niż jednego agenta.

Aby odzyskać z tego scenariusza:

1. Usunąć bazę danych z każdej grupy synchronizacji, którego on należy.  
2. Bazy danych z powrotem dodać do każdej grupy synchronizacji, które zostało usunięte z.  
3. Wdróż każdej grupy synchronizacji dotyczy (Ta akcja inicjuje bazy danych).  

### <a name="modifying-your-sync-group"></a> Modyfikowanie grupy synchronizacji

Nie próbuj usunąć bazę danych z grupy synchronizacji, a następnie Edytuj grupę synchronizacji bez pierwszego wdrażania zmian.

Zamiast tego należy najpierw usunąć bazę danych z grupy synchronizacji. Następnie wdrożenia zmiany i poczekaj, aż anulowania obsługi, aby zakończyć. Po zakończeniu operacji anulowania obsługi, należy edytować grupę synchronizacji i wdrażanie zmiany.

Jeśli próbujesz usunąć bazę danych, a następnie Edytuj grupę synchronizacji bez wdrażanie pierwszej zmian, awarii jednego lub innej operacji. Interfejs portalu może stać się niespójna. Jeśli tak się stanie, Odśwież stronę, aby przywrócić stan.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat synchronizacji danych SQL zobacz:

-   [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](sql-database-sync-data.md)
-   [Set up Azure SQL Data Sync (Konfigurowanie usługi Azure SQL Data Sync)](sql-database-get-started-sql-data-sync.md)
-   [Monitor Azure SQL Data Sync with Log Analytics (Monitorowanie usługi Azure SQL Data Sync za pomocą usługi Log Analytics)](sql-database-sync-monitor-oms.md)
-   [Troubleshoot issues with Azure SQL Data Sync (Rozwiązywanie problemów z usługą Azure SQL Data Sync)](sql-database-troubleshoot-data-sync.md)  
-   Pełne przykładowe skrypty programu PowerShell przedstawiające sposób konfigurowania usługi SQL Data Sync:  
    -   [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Pobierz dokumentację interfejsu API REST usługi SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)  

Aby uzyskać więcej informacji dotyczących bazy danych SQL zobacz:

-   [Omówienie bazy danych SQL](sql-database-technical-overview.md)
-   [Zarządzanie cyklem życia bazy danych](https://msdn.microsoft.com/library/jj907294.aspx)
