---
title: Najlepsze rozwiązania dotyczące usługi Azure SQL Data Sync | Dokumentacja firmy Microsoft
description: Poznaj najlepsze rozwiązania dotyczące konfigurowania i uruchamiania usługi Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: ''
manager: craigg
ms.date: 08/20/2018
ms.openlocfilehash: 1d292007b06e12b6be28e053bc6def3b12c7befe
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063659"
---
# <a name="best-practices-for-sql-data-sync"></a>Najlepsze rozwiązania dotyczące funkcji SQL Data Sync 

W tym artykule opisano najlepsze rozwiązania dla usługi Azure SQL Data Sync.

Omówienie usługi SQL Data Sync zawiera temat [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](sql-database-sync-data.md).

## <a name="security-and-reliability"></a> Bezpieczeństwo i niezawodność

### <a name="client-agent"></a>Agent klienta

-   Zainstaluj agenta klienta przy użyciu najmniej uprzywilejowane konto użytkownika, które ma dostęp do usługi sieci.  
-   Zainstaluj agenta klienta na komputerze, na którym nie jest komputerem programu SQL Server w środowisku lokalnym.  
-   Nie można zarejestrować lokalnej bazy danych z więcej niż jednego agenta.    
    -   Unikaj, nawet wtedy, gdy objęte synchronizacją z różnych tabel dla różnych synchronizacji grupy.  
    -   Rejestrowanie lokalnej bazy danych z wielu klientów agentów stwarza wyzwania, usunięcie jednej z grup synchronizacji.

### <a name="database-accounts-with-least-required-privileges"></a>Konta bazy danych z co najmniej wymaganych uprawnień

-   **Synchronizacja ustawień**. Utwórz/Alter Table; Instrukcja ALTER Database, Utwórz procedurę; Wybierz opcję / Alter schematu; Utwórz typ zdefiniowany przez użytkownika.

-   **Trwającą synchronizacji**. Wybierz opcję / wstawiania / aktualizacji / usuwania w tabelach, które są wybrane do synchronizacji, a także na synchronizacji metadanych i śledzenie tabel. Uprawnienia do wykonywania na procedury przechowywane utworzone przez usługę; Uprawnienia do wykonywania w typach tabel zdefiniowanych przez użytkownika.

-   **Anulować**. Instrukcja ALTER w tabelach części synchronizacji; Wybierz opcję / Usuwanie tabel metadanych synchronizacji. Kontrolowanie podczas synchronizacji śledzenia tabel, procedur składowanych i typy zdefiniowane przez użytkownika.

Usługa Azure SQL Database obsługuje tylko jeden zestaw poświadczeń. Aby wykonać te zadania w ramach tego ograniczenia, należy wziąć pod uwagę następujące opcje:

-   Zmienić poświadczenia dla różnych etapów (na przykład *credentials1* instalacji i *credentials2* dla trwających).  
-   Zmiana uprawnień poświadczeń (oznacza to, Zmień uprawnienia po skonfigurowaniu synchronizacji).

## <a name="setup"></a>Konfigurowanie

### <a name="database-considerations-and-constraints"></a> Kwestie i ograniczenia

#### <a name="sql-database-instance-size"></a>Rozmiar wystąpienia bazy danych SQL

Podczas tworzenia nowego wystąpienia bazy danych SQL, Ustaw maksymalny rozmiar, tak aby zawsze jest większy niż bazy danych, które można wdrożyć. Jeśli nie zostanie ustawiona maksymalnego rozmiaru do większych niż wdrożonej bazy danych, synchronizacja nie powiedzie się. Mimo że SQL Data Sync nie oferują automatycznego wzrostu, możesz uruchomić `ALTER DATABASE` polecenie, aby zwiększyć rozmiar bazy danych, po jego utworzeniu. Upewnij się, że przekroczysz limity rozmiaru wystąpienia bazy danych SQL.

> [!IMPORTANT]
> SQL Data Sync przechowuje dodatkowe metadane z każdą bazą danych. Upewnij się, uwzględnione te metadane podczas obliczania potrzebne miejsce. Dodatkowa ilość obciążenie jest powiązany z szerokość tabel (na przykład tabele wąskie wymagają większym obciążeniem) oraz ilości ruchu sieciowego.

### <a name="table-considerations-and-constraints"></a> Tabela zagadnienia i ograniczenia

#### <a name="selecting-tables"></a>Wybór tabel

Nie masz uwzględnić wszystkie tabele, które znajdują się w bazie danych w grupie synchronizacji. Tabele, które należy uwzględnić w grupie synchronizacji wpływa na wydajność i koszty. Obejmują tabel i tabel, które są zależne, w grupie synchronizacji tylko wtedy, gdy wymagają tego potrzeb biznesowych.

#### <a name="primary-keys"></a>Klucze podstawowe

Każda tabela w grupie synchronizacji musi mieć klucz podstawowy. Usługa SQL Data Sync nie może zsynchronizować tabelę, która nie ma klucza podstawowego.

Przed użyciem SQL Data Sync w środowisku produkcyjnym należy przetestować wydajność synchronizacji początkowej oraz bieżące.

### <a name="provisioning-destination-databases"></a> Inicjowanie obsługi administracyjnej docelowej bazy danych

SQL Data Sync zawiera autoprovisioning podstawowej bazy danych.

W tej sekcji omówiono ograniczenia obsługi synchronizacji danych SQL.

#### <a name="autoprovisioning-limitations"></a>Ograniczenia Autoprovisioning

SQL Data Sync ma następujące ograniczenia dotyczące autoprovisioning:

-   Wybierz kolumny, które są tworzone w tabeli docelowej. Wszelkie kolumny, które nie należą do grupy synchronizacji nie są aprowizowane w tabelach docelowych.
-   Indeksy są tworzone tylko dla wybranych kolumn. Jeśli źródło tabeli indeksu zawiera kolumny, które nie należą do grupy synchronizacji, te indeksy nie są aprowizowane w tabelach docelowych.  
-   Indeksy w kolumnach typu XML nie są aprowizowane.  
-   Ograniczenia CHECK nie zainicjowano obsługi administracyjnej.  
-   Istniejące wyzwalacze w tabelach źródłowych nie są aprowizowane.  
-   Widoki i procedury składowane nie są tworzone w docelowej bazie danych.
-   UPDATE CASCADE i ON DELETE CASCADE akcje na ograniczenia klucza obcego nie są odtwarzane w tabelach docelowych.
-   W przypadku kolumn dziesiętnego lub liczbowego o dokładności większej niż 28 SQL Data Sync może wystąpić problem przepełnienia konwersji podczas synchronizacji. Zaleca się ograniczenie dokładność dziesiętnego lub liczbowego kolumn do 28 lub mniej.

#### <a name="recommendations"></a>Zalecenia

-   Tylko wtedy, gdy są wypróbowanie usługi, należy użyć funkcji autoprovisioning SQL Data Sync.  
-   W środowisku produkcyjnym obsługi administracyjnej schematu bazy danych.

### <a name="locate-hub"></a> Decyzję o lokalizacji bazy danych Centrum

#### <a name="enterprise-to-cloud-scenario"></a>Scenariusz przedsiębiorstwa do chmury

Aby zminimalizować czas oczekiwania, zachować baza danych koncentratora blisko największy koncentracja grupy synchronizacji związanego z bazy danych.

#### <a name="cloud-to-cloud-scenario"></a>Scenariusz chmury do chmury

-   W przypadku wszystkich baz danych w grupie synchronizacji w jednym centrum danych, Centrum powinien znajdować się w jednym centrum danych. Ta konfiguracja ogranicza opóźnienia i koszty transferu danych między centrami danych.
-   W przypadku baz danych w grupie synchronizacji w wielu centrach danych, Centrum powinien znajdować się w jednym centrum danych, ponieważ większość baz danych i ruchu bazy danych.

#### <a name="mixed-scenarios"></a>Scenariusze mieszanych

Zastosowanie poprzedniego wytyczne do złożonych synchronizacji konfiguracji grupy, takie jak te, które są różne scenariusze przedsiębiorstwa do chmury i z chmury do chmury.

## <a name="sync"></a>Sync

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a> Należy unikać powolne i kosztownych początkowej synchronizacji

W tej sekcji omówiono początkowa synchronizacja grupy synchronizacji. Dowiedz się, jak ułatwia zapobieganie synchronizacji początkowej, trwa dłużej, i jest droższy niż jest to konieczne.

#### <a name="how-initial-sync-works"></a>Początkowa synchronizacja działa

Podczas tworzenia grupy synchronizacji, należy uruchomić z danymi w tylko jednej bazy danych. Jeśli masz dane w wielu bazach danych SQL Data Sync traktuje każdy wiersz jako konfliktu, który ma zostać rozpoznane. To rozwiązanie konfliktu powoduje, że synchronizacji początkowej do powoli. Jeśli masz dane w wielu bazach danych początkowej synchronizacji może potrwać od kilku dni i kilka miesięcy, w zależności od rozmiaru bazy danych.

Jeśli bazy danych znajdują się w różnych centrach danych, każdy wiersz musi przejść między różnych centrach danych. Zwiększa to koszt synchronizacji początkowej.

#### <a name="recommendation"></a>Zalecenie

Jeśli to możliwe należy rozpocząć od danych tylko w jednej z baz danych w grupie synchronizacji.

### <a name="design-to-avoid-synchronization-loops"></a> Projekt, aby uniknąć pętli synchronizacji

Pętla synchronizacji występuje, gdy istnieją odwołania cykliczne w obrębie grupy synchronizacji. W tym scenariuszu każdej zmiany w jednej bazie danych jest nieskończenie i rekurencyjnie replikowana przy użyciu bazy danych w grupie synchronizacji.   

Upewnij się, należy unikać pętli synchronizacji, ponieważ mogą spowodować obniżenie wydajności i może znacząco zwiększyć koszty.

### <a name="handling-changes-that-fail-to-propagate"></a> Zmiany, które nie są propagowane

#### <a name="reasons-that-changes-fail-to-propagate"></a>Powody, dla których zmiany nie można propagować

Zmiany może zakończyć się niepowodzeniem do propagowania dla jednego z następujących powodów:

-   Niezgodność schematu/typu danych.
-   Wstawianie wartości null w kolumnach innych niż null.
-   Naruszenie ograniczenia klucza obcego.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Co się stanie, gdy zmiany nie można propagować?

-   Synchronizuj przedstawiono grupy, które znajduje się w **ostrzeżenie** stanu.
-   Szczegółowe informacje są wyświetlane w portalu przeglądarka dzienników interfejsu użytkownika.
-   Jeśli problem nie zostanie rozwiązany 45 dniach, baza danych stanie się nieaktualna.

> [!NOTE]
> Zmiany te nie będą przenoszone. Jedynym sposobem odzyskania w tym scenariuszu jest ponownie utworzyć grupy synchronizacji.

#### <a name="recommendation"></a>Zalecenie

Monitorowanie kondycji grupy i bazy danych synchronizacji regularnie za pośrednictwem interfejsu witryny portal i dziennika.


## <a name="maintenance"></a>Konserwacja

### <a name="avoid-out-of-date-databases-and-sync-groups"></a> Należy unikać nieaktualne baz danych i zsynchronizuj grupy

Grupa synchronizacji lub bazy danych w grupie synchronizacji może stać się nieaktualne. Jeśli stan grupą synchronizacji to **nieaktualne**, przestanie działać. Jeśli stan bazy danych to **nieaktualne**, dane mogą zostać utracone. Najlepiej temu zapobiec, zamiast próbować odzyskać z niej.

#### <a name="avoid-out-of-date-databases"></a>Należy unikać nieaktualne baz danych

Stan bazy danych jest ustawiony na **nieaktualne** gdy było w trybie offline dla co najmniej 45 dni. Aby uniknąć **nieaktualne** stan w bazie danych, upewnij się, że żadna z baz danych w trybie offline dla co najmniej 45 dni.

#### <a name="avoid-out-of-date-sync-groups"></a>Należy unikać grup synchronizacji nieaktualne

Stan grupy synchronizacji jest ustawiony na **nieaktualne** po każdej zmianie w grupie synchronizacji nie powiedzie się obejmie pozostałe grupy synchronizacji dla co najmniej 45 dni. Aby uniknąć **nieaktualne** stan grupy synchronizacji, regularnie sprawdzać Dziennik historii grupy synchronizacji. Upewnij się, że wszystkie konflikty są rozwiązywane i czy zmiany są pomyślnie propagowane w całej bazy danych grupy synchronizacji.

Grupa synchronizacji może zakończyć się niepowodzeniem do zastosowania zmiany dla jednego z następujących powodów:

-   Niezgodność schematów między tabelami.
-   Niezgodność danych między tabelami.
-   Wstawianie wierszy z wartością null w kolumnie, który nie dopuszcza wartości null.
-   Aktualizowanie wiersz z wartością, która narusza ograniczenie klucza obcego.

Aby uniknąć grup synchronizacji przestarzałe:

-   Aktualizowanie schematu, aby zezwolić na wartości, które są zawarte w wierszy nie powiodło się.
-   Zaktualizuj wartości klucza obcego, aby uwzględnić wartości, które są zawarte w wierszach nie powiodło się.
-   Zaktualizuj wartości danych w wierszu nie powiodło się, dzięki czemu są one zgodne ze schematem lub klucze obce w docelowej bazie danych.

### <a name="avoid-deprovisioning-issues"></a> Należy unikać problemów o anulowaniu aprowizacji

W niektórych sytuacjach wyrejestrowywania bazy danych przy użyciu agenta klienta może spowodować synchronizacji nie powiedzie się.

#### <a name="scenario"></a>Scenariusz

1. Grupy synchronizacji, A został utworzony przy użyciu wystąpienia bazy danych SQL i lokalnej bazy danych SQL Server, który jest skojarzony z agent lokalny jest 1.
2. Tej samej lokalnej bazy danych jest zarejestrowany w agent lokalny 2 (tego agenta nie jest skojarzona z żadną inną grupą synchronizacji).
3. Wyrejestrowywanie lokalnej bazy danych z lokalnego agenta 2 Usuwa śledzenia i tabel metadanych dla synchronizacji grupy A do lokalnej bazy danych.
4. Synchronizacja grupy niepowodzenia operacji, z powodu następującego błędu: "bieżącej operacji nie można ukończyć ponieważ baza danych nie zostało aprowizowane do celów synchronizacji lub nie masz uprawnień do tabel, Konfiguracja synchronizacji."

#### <a name="solution"></a>Rozwiązanie

Aby temu zapobiec, nie zarejestrujesz bazę danych z więcej niż jednego agenta.

Aby odzyskać z tego scenariusza:

1. Usuń bazę danych z każdej grupy synchronizacji, do którego on należy.  
2. Baza danych z powrotem dodać do każdej grupy synchronizacji, która go usunąć.  
3. Wdróż każdej grupy synchronizacji dotyczy (Ta akcja inicjuje obsługę bazy danych).  

### <a name="modifying-your-sync-group"></a> Modyfikowanie grupy synchronizacji

Nie podejmuj próby Usuń bazę danych z grupą synchronizacji, a następnie Edytuj grupę synchronizacji bez wdrażania pierwszego zmian.

Zamiast tego należy najpierw usunąć bazę danych z grupą synchronizacji. Następnie Wdróż tę zmianę i poczekaj na zakończenie cofania. Po zakończeniu operacji cofania aprowizacji, można edytować grupy synchronizacji i wdrażanie zmiany.

Jeśli spróbujesz usunąć bazę danych, a następnie Edytuj grupę synchronizacji bez wdrażania pierwszego zmian, co najmniej inna operacja kończy się niepowodzeniem. W interfejsie portalu mogą stać się niespójna. Jeśli tak się stanie, Odśwież stronę Aby przywrócić stan prawidłowy.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat SQL Data Sync zobacz:

-   [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](sql-database-sync-data.md)
-   [Set up Azure SQL Data Sync (Konfigurowanie usługi Azure SQL Data Sync)](sql-database-get-started-sql-data-sync.md)
-   [Monitor Azure SQL Data Sync with Log Analytics (Monitorowanie usługi Azure SQL Data Sync za pomocą usługi Log Analytics)](sql-database-sync-monitor-oms.md)
-   [Troubleshoot issues with Azure SQL Data Sync (Rozwiązywanie problemów z usługą Azure SQL Data Sync)](sql-database-troubleshoot-data-sync.md)  
-   Pełne przykładowe skrypty programu PowerShell przedstawiające sposób konfigurowania usługi SQL Data Sync:  
    -   [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)  

Aby uzyskać więcej informacji o usłudze SQL Database zobacz:

-   [Omówienie usługi SQL Database](sql-database-technical-overview.md)
-   [Zarządzanie cyklem życia bazy danych](https://msdn.microsoft.com/library/jj907294.aspx)
