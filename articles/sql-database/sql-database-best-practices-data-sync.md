---
title: Najlepsze rozwiązania dotyczące funkcji Data Sync
description: Dowiedz się więcej o najlepszych rozwiązaniach dotyczących konfigurowania i uruchamiania usługi Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 2b72d52463164c2a059fce316cc11a63aad62e7c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380945"
---
# <a name="best-practices-for-sql-data-sync"></a>Najlepsze rozwiązania dotyczące funkcji SQL Data Sync 

W tym artykule opisano najlepsze rozwiązania dotyczące synchronizacji danych SQL usługi Azure.

Omówienie usługi SQL Data Sync zawiera temat [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](sql-database-sync-data.md).

> [!IMPORTANT]
> Usługa Azure SQL Data Sync **nie** obsługuje obecnie wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="security-and-reliability"></a><a name="security-and-reliability"></a>Bezpieczeństwo i niezawodność

### <a name="client-agent"></a>Agent klienta

-   Zainstaluj agenta klienta przy użyciu najmniej uprzywilejowanego konta użytkownika, które ma dostęp do usługi sieciowej.  
-   Zainstaluj agenta klienta na komputerze, który nie jest lokalnym komputerem programu SQL Server.  
-   Nie rejestruj lokalnej bazy danych z więcej niż jednym agentem.    
    -   Unikaj tego, nawet jeśli synchronizujesz różne tabele dla różnych grup synchronizacji.  
    -   Rejestrowanie lokalnej bazy danych z wieloma agentami klienta stanowi wyzwanie po usunięciu jednej z grup synchronizacji.

### <a name="database-accounts-with-least-required-privileges"></a>Konta bazy danych z najmniej wymaganymi uprawnieniami

-   **Do konfiguracji synchronizacji**. Tworzenie/zmienianie tabeli; Zmień bazę danych; Tworzenie procedury; Wybierz/ Zmień schemat; Utwórz typ zdefiniowany przez użytkownika.

-   **Do ciągłej synchronizacji**. Wybierz/ Wstawianie / Aktualizowanie / Usuwanie w tabelach, które są wybrane do synchronizacji, oraz w metadanych synchronizacji i tabelach śledzenia; Wykonywanie uprawnień do procedur przechowywanych utworzonych przez usługę; Wykonywanie uprawnień dla typów tabel zdefiniowanych przez użytkownika.

-   **Do usuwania obsługi administracyjnej**. Zmienianie w tabelach części synchronizacji; Zaznaczanie/ Usuwanie w tabelach metadanych synchronizacji; Sterowanie tabelami śledzenia synchronizacji, procedurami przechowywanymi i typami zdefiniowanymi przez użytkownika.

Usługa Azure SQL Database obsługuje tylko jeden zestaw poświadczeń. Aby wykonać te zadania w ramach tego ograniczenia, należy wziąć pod uwagę następujące opcje:

-   Zmień poświadczenia dla różnych faz (na przykład *poświadczenia1* dla instalacji i *poświadczenia2* dla bieżących).  
-   Zmień uprawnienia poświadczeń (czyli zmień uprawnienie po skonfigurowaniu synchronizacji).

## <a name="setup"></a>Konfigurowanie

### <a name="database-considerations-and-constraints"></a><a name="database-considerations-and-constraints"></a>Zagadnienia i ograniczenia dotyczące bazy danych

#### <a name="sql-database-instance-size"></a>Rozmiar wystąpienia bazy danych SQL

Podczas tworzenia nowego wystąpienia bazy danych SQL, należy ustawić maksymalny rozmiar, tak aby zawsze był większy niż baza danych, które można wdrożyć. Jeśli nie ustawisz maksymalnego rozmiaru na większy niż wdrożona baza danych, synchronizacja zakończy się niepowodzeniem. Chociaż sql data sync nie oferuje automatycznego `ALTER DATABASE` wzrostu, można uruchomić polecenie, aby zwiększyć rozmiar bazy danych po jej utworzeniu. Upewnij się, że pozostaniesz w granicach rozmiaru wystąpienia bazy danych SQL.

> [!IMPORTANT]
> Synchronizacja danych SQL przechowuje dodatkowe metadane z każdą bazą danych. Upewnij się, że uwzględniasz te metadane podczas obliczania wymaganego miejsca. Ilość dodanych narzutów jest związana z szerokością tabel (na przykład wąskie tabele wymagają więcej narzutów) i natężeniem ruchu.

### <a name="table-considerations-and-constraints"></a><a name="table-considerations-and-constraints"></a>Zagadnienia i ograniczenia dotyczące tabeli

#### <a name="selecting-tables"></a>Wybieranie tabel

Nie trzeba uwzględniać wszystkich tabel, które znajdują się w bazie danych w grupie synchronizacji. Tabele uwzględnione w grupie synchronizacji mają wpływ na wydajność i koszty. Uwzględnij tabele i tabele, od których są zależne, w grupie synchronizacji tylko wtedy, gdy wymaga tego firma.

#### <a name="primary-keys"></a>Klucze podstawowe

Każda tabela w grupie synchronizacji musi mieć klucz podstawowy. Usługa SYNCHRONIZACJI DANYCH SQL nie może zsynchronizować tabeli, która nie ma klucza podstawowego.

Przed użyciem synchronizacji danych SQL w produkcji należy przetestować wydajność synchronizacji początkowej i ciągłej.

#### <a name="empty-tables-provide-the-best-performance"></a>Puste tabele zapewniają najlepszą wydajność

Puste tabele zapewniają najlepszą wydajność w czasie inicjowania. Jeśli tabela docelowa jest pusta, synchronizacja danych używa wstawiania zbiorczego do załadowania danych. W przeciwnym razie synchronizacja danych wykonuje porównanie wiersza po wierszu i wstawianie w celu sprawdzenia konfliktów. Jeśli jednak wydajność nie jest problemem, można skonfigurować synchronizację między tabelami, które już zawierają dane.

### <a name="provisioning-destination-databases"></a><a name="provisioning-destination-databases"></a>Inicjowanie obsługi administracyjnej docelowych baz danych

Sql Data Sync zapewnia podstawowych baz danych autoprowizja.

W tej sekcji omówiono ograniczenia inicjowania obsługi administracyjnej w synchronizacji danych SQL.

#### <a name="autoprovisioning-limitations"></a>Ograniczenia automatycznego obsługi administracyjnej

Synchronizacja danych SQL ma następujące ograniczenia dotyczące automatycznego obsługi administracyjnej:

-   Zaznacz tylko kolumny utworzone w tabeli docelowej. Wszystkie kolumny, które nie są częścią grupy synchronizacji nie są aprowizowane w tabelach docelowych.
-   Indeksy są tworzone tylko dla wybranych kolumn. Jeśli indeks tabeli źródłowej zawiera kolumny, które nie są częścią grupy synchronizacji, te indeksy nie są aprowizowane w tabelach docelowych.  
-   Indeksy w kolumnach typu XML nie są aprowizowane.  
-   Ograniczenia CHECK nie są aprowiowane.  
-   Istniejące wyzwalacze w tabelach źródłowych nie są aprowizacji.  
-   Widoki i procedury przechowywane nie są tworzone w docelowej bazie danych.
-   Na update kaskada i na usuwanie kaskadowe akcje na ograniczenia klucza obcego nie są odtworzone w tabelach docelowych.
-   Jeśli masz kolumny dziesiętne lub liczbowe z dokładnością większą niż 28, synchronizacja danych SQL może wystąpić problem przepełnienia konwersji podczas synchronizacji. Zaleca się ograniczenie dokładności kolumn dziesiętnych lub liczbowych do 28 lub mniej.

#### <a name="recommendations"></a>Zalecenia

-   Funkcja automatycznego obsługi administracyjnej synchronizacji danych SQL umożliwia korzystanie z usługi tylko podczas wypróbowywania usługi.  
-   W przypadku produkcji aprowizuj schemat bazy danych.

### <a name="where-to-locate-the-hub-database"></a><a name="locate-hub"></a>Gdzie zlokalizować bazę danych koncentratora

#### <a name="enterprise-to-cloud-scenario"></a>Scenariusz między przedsiębiorstwami a chmurą

Aby zminimalizować opóźnienia, należy zachować bazy danych koncentratora blisko największego stężenia ruchu bazy danych grupy synchronizacji.

#### <a name="cloud-to-cloud-scenario"></a>Scenariusz między chmurami

-   Gdy wszystkie bazy danych w grupie synchronizacji znajdują się w jednym centrum danych, centrum powinno znajdować się w tym samym centrum danych. Ta konfiguracja zmniejsza opóźnienia i koszt transferu danych między centrami danych.
-   Gdy bazy danych w grupie synchronizacji znajdują się w wielu centrach danych, centrum powinno znajdować się w tym samym centrum danych, co większość baz danych i ruchu bazy danych.

#### <a name="mixed-scenarios"></a>Scenariusze mieszane

Zastosuj powyższe wskazówki do złożonych konfiguracji grup synchronizacji, takich jak te, które są kombinacją scenariuszy między przedsiębiorstwami i chmurą do chmury.

## <a name="sync"></a>Sync

### <a name="avoid-slow-and-costly-initial-sync"></a><a name="avoid-a-slow-and-costly-initial-synchronization"></a>Unikaj powolnej i kosztownej synchronizacji początkowej

W tej sekcji omówimy początkową synchronizację grupy synchronizacji. Dowiedz się, jak zapobiec dłuższej synchronizacji początkowej i większej niż jest to konieczne.

#### <a name="how-initial-sync-works"></a>Jak działa synchronizacja początkowa

Podczas tworzenia grupy synchronizacji należy rozpocząć od danych tylko w jednej bazie danych. Jeśli masz dane w wielu bazach danych, SQL Data Sync traktuje każdy wiersz jako konflikt, który musi zostać rozwiązany. To rozwiązanie konfliktu powoduje, że synchronizacja początkowa jest powolna. Jeśli masz dane w wielu bazach danych, synchronizacja początkowa może potrwać od kilku dni do kilku miesięcy, w zależności od rozmiaru bazy danych.

Jeśli bazy danych znajdują się w różnych centrach danych, każdy wiersz musi być przesuń między różnymi centrami danych. Zwiększa to koszt synchronizacji początkowej.

#### <a name="recommendation"></a>Zalecenie

Jeśli to możliwe, zacznij od danych tylko w jednej z baz danych grupy synchronizacji.

### <a name="design-to-avoid-sync-loops"></a><a name="design-to-avoid-synchronization-loops"></a>Projektowanie w celu uniknięcia pętli synchronizacji

Pętla synchronizacji występuje, gdy istnieją odwołania cykliczne w grupie synchronizacji. W tym scenariuszu każda zmiana w jednej bazie danych jest bez końca i cyklicznie replikowane za pośrednictwem baz danych w grupie synchronizacji.   

Upewnij się, że unikasz pętli synchronizacji, ponieważ powodują one obniżenie wydajności i mogą znacznie zwiększyć koszty.

### <a name="changes-that-fail-to-propagate"></a><a name="handling-changes-that-fail-to-propagate"></a>Zmiany, których nie można propagować

#### <a name="reasons-that-changes-fail-to-propagate"></a>Powody, dla których zmiany nie są propagowane

Zmiany mogą nie zostać rozsyłane z jednego z następujących powodów:

-   Niezgodność schematu/typu danych.
-   Wstawianie wartości null w kolumnach nienastępujących wartości null.
-   Naruszenie ograniczeń klucza obcego.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Co się stanie, gdy zmiany nie będą propagowane?

-   Grupa synchronizacji pokazuje, że jest w stanie **ostrzeżenie.**
-   Szczegóły są wyświetlane w przeglądarce dzienników interfejsu użytkownika portalu.
-   Jeśli problem nie zostanie rozwiązany przez 45 dni, baza danych staje się nieaktualna.

> [!NOTE]
> Te zmiany nigdy nie propagują. Jedynym sposobem odzyskania w tym scenariuszu jest ponowne utworzenie grupy synchronizacji.

#### <a name="recommendation"></a>Zalecenie

Regularnie monitoruj kondycję grupy synchronizacji i bazy danych za pośrednictwem interfejsu portalu i dziennika.


## <a name="maintenance"></a>Konserwacja

### <a name="avoid-out-of-date-databases-and-sync-groups"></a><a name="avoid-out-of-date-databases-and-sync-groups"></a>Unikanie nieaktualnych baz danych i grup synchronizacji

Grupa synchronizacji lub baza danych w grupie synchronizacji może stać się nieaktualna. Gdy stan grupy synchronizacji jest **nieaktualny,** przestaje działać. Gdy stan bazy danych jest **nieaktualny,** dane mogą zostać utracone. Najlepiej jest uniknąć tego scenariusza, zamiast próbować odzyskać od niego.

#### <a name="avoid-out-of-date-databases"></a>Unikaj nieaktualnych baz danych

Stan bazy danych jest ustawiony na **nieaktualne,** gdy został on w trybie offline przez 45 dni lub więcej. Aby uniknąć **stanu nieaktualne** w bazie danych, upewnij się, że żadna z baz danych jest w trybie offline przez 45 dni lub dłużej.

#### <a name="avoid-out-of-date-sync-groups"></a>Unikanie nieaktualnych grup synchronizacji

Stan grupy synchronizacji jest ustawiony na **Nieaktualne,** gdy wszelkie zmiany w grupie synchronizacji nie będą propagowane do pozostałej części grupy synchronizacji przez co najmniej 45 dni. Aby uniknąć stanu **Nieaktualne** w grupie synchronizacji, regularnie sprawdzaj dziennik historii grupy synchronizacji. Upewnij się, że wszystkie konflikty zostały rozwiązane i że zmiany są pomyślnie propagowane w bazach danych grup synchronizacji.

Grupa synchronizacji może nie zastosować zmiany z jednego z następujących powodów:

-   Niezgodność schematu między tabelami.
-   Niezgodność danych między tabelami.
-   Wstawianie wiersza z wartością null w kolumnie, która nie zezwala na wartości null.
-   Aktualizowanie wiersza z wartością, która narusza ograniczenie klucza obcego.

Aby zapobiec nieaktualne grupy synchronizacji:

-   Zaktualizuj schemat, aby zezwolić na wartości, które są zawarte w wierszach nie powiodło się.
-   Zaktualizuj wartości klucza obcego, aby uwzględnić wartości zawarte w wierszach, które nie powiodły się.
-   Zaktualizuj wartości danych w wierszu, który nie powiodło się, aby były zgodne ze schematem lub kluczami obcymi w docelowej bazie danych.

### <a name="avoid-deprovisioning-issues"></a><a name="avoid-deprovisioning-issues"></a>Unikanie problemów związanych z usuwaniem obsługi administracyjnej

W niektórych okolicznościach wyrejestrowanie bazy danych za pomocą agenta klienta może spowodować niepowodzenie synchronizacji.

#### <a name="scenario"></a>Scenariusz

1. Grupa synchronizacji A została utworzona przy użyciu wystąpienia bazy danych SQL i lokalnej bazy danych programu SQL Server, która jest skojarzona z agentem lokalnym 1.
2. Ta sama lokalna baza danych jest zarejestrowana u agenta lokalnego 2 (ten agent nie jest skojarzony z żadną grupą synchronizacji).
3. Wyrejestrowanie lokalnej bazy danych z agenta lokalnego 2 powoduje usunięcie tabel śledzenia i meta dla grupy synchronizacji A dla lokalnej bazy danych.
4. Operacje grupy synchronizacji A nie powiodą się, z tym błędem: "Nie można ukończyć bieżącej operacji, ponieważ baza danych nie jest aprowizowana do synchronizacji lub nie masz uprawnień do tabel konfiguracji synchronizacji."

#### <a name="solution"></a>Rozwiązanie

Aby uniknąć tego scenariusza, nie rejestruj bazy danych z więcej niż jednym agentem.

Aby odzyskać dane z tego scenariusza:

1. Usuń bazę danych z każdej grupy synchronizacji, do której należy.  
2. Dodaj bazę danych z powrotem do każdej grupy synchronizacji, z której została usunięta.  
3. Wdrażanie każdej grupy synchronizacji, których dotyczy problem (ta akcja apruje bazę danych).  

### <a name="modifying-a-sync-group"></a><a name="modifying-your-sync-group"></a>Modyfikowanie grupy synchronizacji

Nie próbuj usuwać bazy danych z grupy synchronizacji, a następnie edytować grupę synchronizacji bez uprzedniego wdrożenia jednej ze zmian.

Zamiast tego najpierw usuń bazę danych z grupy synchronizacji. Następnie należy wdrożyć zmiany i czekać na anulowanie obsługi administracyjnej, aby zakończyć. Po zakończeniu anulowania obsługi administracyjnej można edytować grupę synchronizacji i wdrażać zmiany.

Jeśli spróbujesz usunąć bazę danych, a następnie edytować grupę synchronizacji bez uprzedniego wdrożenia jednej ze zmian, jedna lub druga operacja zakończy się niepowodzeniem. Interfejs portalu może stać się niespójny. W takim przypadku odśwież stronę, aby przywrócić prawidłowy stan.

### <a name="avoid-schema-refresh-timeout"></a>Unikanie limitu czasu odświeżania schematu

Jeśli masz złożony schemat do synchronizacji, może wystąpić "limit czasu operacji" podczas odświeżania schematu, jeśli baza danych metadanych synchronizacji ma niższą jednostkę SKU (przykład: basic). 

#### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, skaluj w górę bazę danych metadanych synchronizacji, aby mieć wyższą jednostkę SKU, taką jak S3. 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat synchronizacji danych SQL, zobacz:

-   Omówienie — [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync](sql-database-sync-data.md) (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)
-   Konfigurowanie synchronizacji danych
    - W portalu — [Tutorial: Set up SQL Data Sync to sync data between Azure SQL Database and SQL Server on-premises](sql-database-get-started-sql-data-sync.md) (Samouczek: konfigurowanie usługi SQL Data Sync w celu synchronizowania danych między usługą Azure SQL Database i lokalnym programem SQL Server)
    - Z programem PowerShell
        -  [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL Database i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agent synchronizacji danych — [Data Sync Agent for Azure SQL Data Sync](sql-database-data-sync-agent.md) (Agent synchronizacji danych dla usługi Azure SQL Data Sync)
-   Monitor — [monitorowanie synchronizacji danych SQL za pomocą dzienników usługi Azure Monitor](sql-database-sync-monitor-oms.md)
-   Rozwiązywanie problemów — [Troubleshoot issues with Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md) (Rozwiązywanie problemów z usługą Azure SQL Data Sync)
-   Aktualizowanie schematu synchronizacji
    -   Za pomocą języka Transact-SQL — [Automate the replication of schema changes in Azure SQL Data Sync](sql-database-update-sync-schema.md) (Automatyzowanie replikacji zmian schematu w usłudze Azure SQL Data Sync)
    -   Używanie programu PowerShell — [Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji](scripts/sql-database-sync-update-schema.md)

Aby uzyskać więcej informacji na temat bazy danych SQL, zobacz:

-   [Omówienie bazy danych SQL](sql-database-technical-overview.md)
-   [Zarządzanie cyklem życia bazy danych](https://msdn.microsoft.com/library/jj907294.aspx)
