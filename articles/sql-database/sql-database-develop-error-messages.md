---
title: Kody błędów SQL — błąd połączenia z bazą danych | Microsoft Docs
description: 'Informacje o kodach błędów SQL dla SQL Database aplikacji klienckich, takich jak typowe błędy połączenia z bazą danych, problemy z kopiowaniem bazy danych i błędy ogólne. '
keywords: kod błędu SQL, dostęp SQL, błąd połączenia z bazą danych, kody błędów SQL
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/06/2019
ms.openlocfilehash: 24bd2cca2e4ed053d51f618d90274e8988a09c26
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568905"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>Kody błędów SQL dla SQL Database aplikacji klienckich: Błędy połączenia z bazą danych i inne problemy

W tym artykule przedstawiono listę kodów błędów SQL dla SQL Database aplikacji klienckich, w tym błędów połączeń z bazą danych, błędów przejściowych (nazywanych także błędami przejściowymi), błędów ładu zasobów, problemów z kopiowaniem bazy danych, elastycznej puli i innych błędów. Większość kategorii jest określonych do Azure SQL Database i nie ma zastosowania do Microsoft SQL Server. Zobacz również [komunikaty o błędach systemu](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Błędy połączenia z bazą danych, błędy przejściowe i inne błędy tymczasowe

W poniższej tabeli przedstawiono kody błędów usługi SQL dla błędów utraty połączenia oraz inne błędy przejściowe, które mogą wystąpić, gdy aplikacja próbuje uzyskać dostęp do SQL Database. Aby poznać samouczki dotyczące łączenia się z usługą Azure SQL Database, zobacz [nawiązywanie połączenia z Azure SQL Database](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Najczęstsze błędy połączenia z bazą danych i błędy przejściowe

Infrastruktura platformy Azure ma możliwość dynamicznego ponownego konfigurowania serwerów w przypadku wystąpienia dużych obciążeń w usłudze SQL Database.  To dynamiczne zachowanie może powodować, że program kliencki utraci połączenie z usługą SQL Database. Ten rodzaj warunku błędu nazywa się *błędem przejściowym*.

Zdecydowanie zaleca się, aby program kliencki miał logikę ponowień, aby można było ponownie nawiązać połączenie po podaniu błędu przejściowego.  Zalecamy opóźnienie przez 5 sekund przed pierwszym ponowieniem próby. Ponawianie próby po opóźnieniu krótszym niż 5 sekund ryzyka przeciążania usługi w chmurze. Dla każdej kolejnej ponownej próby opóźnienie powinno się zwiększyć wykładniczo, maksymalnie 60 sekund.

Błędy przejściowe zwykle manifestu jako jeden z następujących komunikatów o błędach z programów klienckich:

* Baza &lt;danych&gt; db_name na &lt;serwerzeAzure_instance&gt; jest obecnie niedostępna. Spróbuj ponownie nawiązać połączenie później. Jeśli problem będzie nadal występować, skontaktuj się z pomocą techniczną i podaj identyfikator &lt;śledzenia sesji session_id&gt;
* Baza &lt;danych&gt; db_name na &lt;serwerzeAzure_instance&gt; jest obecnie niedostępna. Spróbuj ponownie nawiązać połączenie później. Jeśli problem będzie nadal występować, skontaktuj się z pomocą techniczną i podaj identyfikator &lt;śledzenia sesji session_id.&gt; (Microsoft SQL Server, błąd: 40613)
* Istniejące połączenie zostało wymuszone przez hosta zdalnego.
* System.Data.Entity.Core.EntityCommandExecutionException: Wystąpił błąd podczas wykonywania definicji polecenia. Aby uzyskać szczegółowe informacje, zobacz wyjątek wewnętrzny. ---> System.Data.SqlClient.SqlException: Wystąpił błąd poziomu transportu podczas otrzymywania wyników z serwera. dostawcy Dostawca sesji, błąd: 19 — połączenie fizyczne nie jest możliwe do użycia
* Próba połączenia z pomocniczą bazą danych nie powiodła się, ponieważ baza danych jest w trakcie ponownej konfiguracji i jest zajęta przez zastosowanie nowych stron w trakcie aktywnej transakcji w podstawowej bazie danych. 

Przykłady kodu logiki ponowień można znaleźć w temacie:

* [Biblioteki połączeń dla SQL Database i SQL Server](sql-database-libraries.md) 
* [Akcje usuwania błędów połączeń i błędów przejściowych w SQL Database](sql-database-connectivity-issues.md)

Omówienie *okresu blokowania* dla klientów korzystających z usługi ADO.NET jest dostępny w [puli połączeń SQL Server (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Kody błędów przejściowych

Następujące błędy są przejściowe i należy ponowić próbę w logice aplikacji: 

| Kod błędu | severity | Opis |
| ---:| ---:|:--- |
| 4060 |16 |Nie można otworzyć bazy danych "%.&#x2a; ls" żądanego podczas logowania. Logowanie nie powiodło się. Aby uzyskać więcej informacji, zobacz [błędy 4000 do 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |Usługa napotkała błąd podczas przetwarzania żądania. Spróbuj ponownie. Kod błędu% d.<br/><br/>Ten błąd występuje, gdy usługa nie działa z powodu uaktualnień oprogramowania lub sprzętu, awarii sprzętu lub innych problemów z trybem failover. Kod błędu (% d) osadzony w komunikacie o błędzie 40197 zawiera dodatkowe informacje o rodzaju awarii lub pracy awaryjnej, które wystąpiły. Niektóre przykłady kodów błędów są osadzone w komunikacie o błędzie 40197 to 40020, 40143, 40166 i 40540.<br/><br/>Ponowne nawiązywanie połączenia z serwerem SQL Database automatycznie nawiązuje połączenie z dobrą kopią bazy danych. Aplikacja musi obsłużyć błąd 40197, zarejestrować osadzony kod błędu (% d) w komunikacie w celu rozwiązywania problemów, a następnie spróbować ponownie nawiązać połączenie z SQL Database, dopóki zasoby nie będą dostępne, a połączenie zostanie nawiązane. Aby uzyskać więcej informacji, zobacz [Błędy przejściowe](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |Usługa jest obecnie zajęta. Ponów żądanie po 10 sekundach. Identyfikator zdarzenia:% ls. Kod:% d. Aby uzyskać więcej informacji, zobacz: <br/>&bull;[Limity zasobów serwera bazy danych](sql-database-resource-limits-database-server.md) &nbsp;<br/>&bull;Limity oparte na jednostkach [DTU dla pojedynczych baz danych](sql-database-service-tiers-dtu.md) &nbsp;<br/>&bull;[Limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md) &nbsp;<br/>&bull;[limity rdzeń wirtualny dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md) &nbsp;<br/>&bull;[limity dla pul elastycznych rdzeń wirtualny](sql-database-vcore-resource-limits-elastic-pools.md) &nbsp;<br/>&bull;[Limity zasobów wystąpienia zarządzanego.](sql-database-managed-instance-resource-limits.md) &nbsp;|
| 40613 |17 |Baza danych '%.&#x2a;ls' na serwerze '%.&#x2a;ls' nie jest obecnie dostępna. Spróbuj ponownie nawiązać połączenie później. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną i podaj identyfikator śledzenia sesji '%.&#x2a;ls'.<br/><br/> Ten błąd może wystąpić, jeśli istnieje już istniejące dedykowane połączenie administratora (DAC) dla bazy danych. Aby uzyskać więcej informacji, zobacz [Błędy przejściowe](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |Nie można przetworzyć żądania. Za mało zasobów, aby przetworzyć żądanie.<br/><br/>Usługa jest obecnie zajęta. Spróbuj ponownie wykonać żądanie później. Aby uzyskać więcej informacji, zobacz: <br/>&bull;[Limity zasobów serwera bazy danych](sql-database-resource-limits-database-server.md) &nbsp;<br/>&bull;Limity oparte na jednostkach [DTU dla pojedynczych baz danych](sql-database-service-tiers-dtu.md) &nbsp;<br/>&bull;[Limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md) &nbsp;<br/>&bull;[limity rdzeń wirtualny dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md) &nbsp;<br/>&bull;[limity dla pul elastycznych rdzeń wirtualny](sql-database-vcore-resource-limits-elastic-pools.md) &nbsp;<br/>&bull;[Limity zasobów wystąpienia zarządzanego.](sql-database-managed-instance-resource-limits.md) &nbsp; |
| 49919 |16 |Nie można przetworzyć żądania Create lub Update. Zbyt wiele operacji tworzenia lub aktualizacji w toku dla subskrypcji "% LD".<br/><br/>Usługa jest zajęta przetwarzaniem wielu żądań utworzenia lub aktualizacji dla Twojej subskrypcji lub serwera. Żądania są obecnie blokowane na potrzeby optymalizacji zasobów. Zapytanie [sys. DM _operation_status](https://msdn.microsoft.com/library/dn270022.aspx) dla operacji oczekujących. Zaczekaj na ukończenie oczekujących żądań utworzenia lub aktualizacji lub Usuń jedno z oczekujących żądań, a następnie ponów żądanie później. Aby uzyskać więcej informacji, zobacz: <br/>&bull;[Limity zasobów serwera bazy danych](sql-database-resource-limits-database-server.md) &nbsp;<br/>&bull;Limity oparte na jednostkach [DTU dla pojedynczych baz danych](sql-database-service-tiers-dtu.md) &nbsp;<br/>&bull;[Limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md) &nbsp;<br/>&bull;[limity rdzeń wirtualny dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md) &nbsp;<br/>&bull;[limity dla pul elastycznych rdzeń wirtualny](sql-database-vcore-resource-limits-elastic-pools.md) &nbsp;<br/>&bull;[Limity zasobów wystąpienia zarządzanego.](sql-database-managed-instance-resource-limits.md) &nbsp; |
| 49920 |16 |Nie można przetworzyć żądania. Zbyt wiele operacji w toku dla subskrypcji "% LD".<br/><br/>Usługa jest zajęta przetwarzaniem wielu żądań dla tej subskrypcji. Żądania są obecnie blokowane na potrzeby optymalizacji zasobów. Zapytanie [sys. DM _operation_status](https://msdn.microsoft.com/library/dn270022.aspx) dla stanu operacji. Zaczekaj na zakończenie oczekujących żądań lub Usuń jedno z oczekujących żądań, a następnie ponów żądanie później. Aby uzyskać więcej informacji, zobacz: <br/>&bull;[Limity zasobów serwera bazy danych](sql-database-resource-limits-database-server.md) &nbsp;<br/>&bull;Limity oparte na jednostkach [DTU dla pojedynczych baz danych](sql-database-service-tiers-dtu.md) &nbsp;<br/>&bull;[Limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md) &nbsp;<br/>&bull;[limity rdzeń wirtualny dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md) &nbsp;<br/>&bull;[limity dla pul elastycznych rdzeń wirtualny](sql-database-vcore-resource-limits-elastic-pools.md) &nbsp;<br/>&bull;[Limity zasobów wystąpienia zarządzanego.](sql-database-managed-instance-resource-limits.md) &nbsp; |
| 4221 |16 |Logowanie do odczytu i pomocnicze nie powiodło się z powodu długiego oczekiwania na "HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING". Replika jest niedostępna do logowania, ponieważ brakuje wersji wiersza dla transakcji, które były odtwarzane podczas odtwarzania repliki. Problem można rozwiązać przez wycofanie lub zatwierdzenie aktywnych transakcji w replice podstawowej. Wystąpienia tego warunku można zminimalizować, unikając długich transakcji zapisu na serwerze podstawowym. |

## <a name="database-copy-errors"></a>Błędy kopiowania bazy danych

Podczas kopiowania bazy danych w Azure SQL Database można napotkać następujące błędy. Więcej informacji znajdziesz w artykule [Kopiowanie bazy danych usługi Azure SQL Database](sql-database-copy.md).

| Kod błędu | severity | Opis |
| ---:| ---:|:--- |
| 40635 |16 |Klient o adresie IP '%.&#x2a;ls' jest tymczasowo wyłączona. |
| 40637 |16 |Tworzenie kopii bazy danych jest obecnie wyłączone. |
| 40561 |16 |Kopiowanie bazy danych nie powiodło się. Źródłowa lub docelowa baza danych nie istnieje. |
| 40562 |16 |Kopiowanie bazy danych nie powiodło się. Źródłowa baza danych została porzucona. |
| 40563 |16 |Kopiowanie bazy danych nie powiodło się. Docelowa baza danych została porzucona. |
| 40564 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40565 |16 |Kopiowanie bazy danych nie powiodło się. Dozwolona jest nie więcej niż 1 współbieżna kopia bazy danych z tego samego źródła. Porzuć docelową bazę danych i spróbuj ponownie później. |
| 40566 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40567 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40568 |16 |Kopiowanie bazy danych nie powiodło się. Źródłowa baza danych stanie się niedostępna. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40569 |16 |Kopiowanie bazy danych nie powiodło się. Docelowa baza danych stanie się niedostępna. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40570 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie później. |
| 40571 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie później. |

## <a name="resource-governance-errors"></a>Błędy ładu zasobów

Następujące błędy są spowodowane nadmiernym użyciem zasobów podczas pracy z Azure SQL Database. Na przykład:

* Transakcja została otwarta zbyt długo.
* Transakcja utrzymuje zbyt wiele blokad.
* Aplikacja zużywa zbyt dużo pamięci.
* Aplikacja zużywa zbyt dużo `TempDb` miejsca.

Tematy pokrewne:

* Aby uzyskać więcej informacji, zobacz:
  * [Limity zasobów serwera bazy danych](sql-database-resource-limits-database-server.md)
  * [Limity oparte na jednostkach DTU dla pojedynczych baz danych](sql-database-service-tiers-dtu.md)
  * [Limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)
  * [limity rdzeń wirtualny dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)
  * [limity dla pul elastycznych rdzeń wirtualny](sql-database-vcore-resource-limits-elastic-pools.md)
  * [Limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md). 

| Kod błędu | severity | Opis |
| ---:| ---:|:--- |
| 10928 |20 |Identyfikator zasobu:% d. Limit% s dla bazy danych to% d i został osiągnięty. Aby uzyskać więcej informacji, zobacz [SQL Database limitów zasobów dla jednej i puli baz danych](sql-database-resource-limits-database-server.md).<br/><br/>Identyfikator zasobu wskazuje zasób, który osiągnął limit. W przypadku wątków roboczych identyfikator zasobu = 1. W przypadku sesji identyfikator zasobu = 2.<br/><br/>Aby uzyskać więcej informacji na temat tego błędu i sposobu jego rozwiązania, zobacz: <br/>&bull;[Limity zasobów serwera bazy danych](sql-database-resource-limits-database-server.md) &nbsp;<br/>&bull;Limity oparte na jednostkach [DTU dla pojedynczych baz danych](sql-database-service-tiers-dtu.md) &nbsp;<br/>&bull;[Limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md) &nbsp;<br/>&bull;[limity rdzeń wirtualny dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md) &nbsp;<br/>&bull;[limity dla pul elastycznych rdzeń wirtualny](sql-database-vcore-resource-limits-elastic-pools.md) &nbsp;<br/>&bull;[Limity zasobów wystąpienia zarządzanego.](sql-database-managed-instance-resource-limits.md) &nbsp; |
| 10929 |20 |Identyfikator zasobu:% d. Minimalna gwarancja% s wynosi% d, maksymalny limit wynosi% d, a bieżące użycie bazy danych to% d. Jednak serwer jest obecnie zbyt zajęty, aby obsługiwać żądania większe niż% d dla tej bazy danych. Identyfikator zasobu wskazuje zasób, który osiągnął limit. W przypadku wątków roboczych identyfikator zasobu = 1. W przypadku sesji identyfikator zasobu = 2. Aby uzyskać więcej informacji, zobacz: <br/>&bull;[Limity zasobów serwera bazy danych](sql-database-resource-limits-database-server.md) &nbsp;<br/>&bull;Limity oparte na jednostkach [DTU dla pojedynczych baz danych](sql-database-service-tiers-dtu.md) &nbsp;<br/>&bull;[Limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md) &nbsp;<br/>&bull;[limity rdzeń wirtualny dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md) &nbsp;<br/>&bull;[limity dla pul elastycznych rdzeń wirtualny](sql-database-vcore-resource-limits-elastic-pools.md) &nbsp;<br/>&bull;[Limity zasobów wystąpienia zarządzanego.](sql-database-managed-instance-resource-limits.md) &nbsp; <br/>W przeciwnym razie spróbuj ponownie później. |
| 40544 |20 |Osiągnięto limit przydziału rozmiaru bazy danych. Utwórz partycję lub Usuń dane, Porzuć indeksy lub zapoznaj się z dokumentacją, aby zapoznać się z możliwymi rozwiązaniami. Aby uzyskać skalowanie bazy danych, zobacz [skalowanie zasobów pojedynczych baz danych](sql-database-single-database-scale.md) i [skalowanie zasobów puli elastycznej](sql-database-elastic-pool-scale.md).|
| 40549 |16 |Sesja została przerwana, ponieważ masz długotrwałą transakcję. Spróbuj skrócić swoją transakcję. Aby uzyskać informacje na temat tworzenia pakietów wsadowych, zobacz [jak używać usługi Batch w celu ulepszania SQL Database aplikacji](sql-database-use-batching-to-improve-performance.md).|
| 40550 |16 |Sesja została przerwana, ponieważ uzyskała zbyt wiele blokad. Spróbuj odczytać lub zmodyfikować mniejszą liczbę wierszy w jednej transakcji. Aby uzyskać informacje na temat tworzenia pakietów wsadowych, zobacz [jak używać usługi Batch w celu ulepszania SQL Database aplikacji](sql-database-use-batching-to-improve-performance.md).|
| 40551 |16 |Sesja została przerwana z powodu nadmiernego `TEMPDB` użycia. Spróbuj zmodyfikować zapytanie, aby zmniejszyć użycie obszaru tabeli tymczasowej.<br/><br/>W przypadku korzystania z obiektów tymczasowych należy zaoszczędzić miejsce w `TEMPDB` bazie danych, upuszczając obiekty tymczasowe, gdy nie są już potrzebne przez sesję. Aby uzyskać więcej informacji na temat użycia tempdb w SQL Database, zobacz [baza danych tempdb w SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |Sesja została przerwana z powodu nadmiernego wykorzystania miejsca w dzienniku transakcji. Spróbuj zmodyfikować mniejszą liczbę wierszy w jednej transakcji. Aby uzyskać informacje na temat tworzenia pakietów wsadowych, zobacz [jak używać usługi Batch w celu ulepszania SQL Database aplikacji](sql-database-use-batching-to-improve-performance.md).<br/><br/>Jeśli wykonujesz operacje wstawiania zbiorczego `bcp.exe` za pomocą narzędzia `System.Data.SqlClient.SqlBulkCopy` lub `-b batchsize` klasy, spróbuj użyć opcji `BatchSize` lub, aby ograniczyć liczbę wierszy skopiowanych do serwera w każdej transakcji. W przypadku odbudowywania indeksu przy użyciu `ALTER INDEX` instrukcji spróbuj `REBUILD WITH ONLINE = ON` użyć opcji. Aby uzyskać informacje o rozmiarach dzienników transakcji dla modelu zakupu rdzeń wirtualny, zobacz: <br/>&bull;[limity rdzeń wirtualny dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md) &nbsp;<br/>&bull;[limity dla pul elastycznych rdzeń wirtualny](sql-database-vcore-resource-limits-elastic-pools.md) &nbsp;<br/>&bull;[Limity zasobów wystąpienia zarządzanego.](sql-database-managed-instance-resource-limits.md) &nbsp;|
| 40553 |16 |Sesja została przerwana z powodu nadmiernego użycia pamięci. Spróbuj zmodyfikować zapytanie, aby przetworzyć mniej wierszy.<br/><br/>Zmniejszenie liczby `ORDER BY` i `GROUP BY` operacji w kodzie Transact-SQL zmniejsza wymagania dotyczące pamięci zapytania. Aby uzyskać skalowanie bazy danych, zobacz [skalowanie zasobów pojedynczych baz danych](sql-database-single-database-scale.md) i [skalowanie zasobów puli elastycznej](sql-database-elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Błędy puli elastycznej

Następujące błędy są związane z tworzeniem i używaniem pul elastycznych:

| Kod błędu | severity | Opis | Akcja naprawcza |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Osiągnięto limit magazynowania elastycznej puli. Użycie magazynu dla puli elastycznej nie może przekroczyć (% d) MB. Podjęto próbę zapisu danych w bazie danych, gdy osiągnięto limit magazynowania puli elastycznej. Aby uzyskać informacje na temat limitów zasobów, zobacz: <br/>&bull;[Limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md) &nbsp;<br/>&bull;[limity dla pul elastycznych rdzeń wirtualny.](sql-database-vcore-resource-limits-elastic-pools.md) &nbsp; <br/> |Rozważ zwiększenie DTU i/lub dodanie magazynu do puli elastycznej, jeśli jest to możliwe, aby zwiększyć jej limit magazynowania, zmniejszyć ilość miejsca używanego przez pojedyncze bazy danych w puli elastycznej lub usunąć bazy danych z puli elastycznej. Aby uzyskać skalowanie puli elastycznej, zobacz [skalowanie zasobów w puli elastycznej](sql-database-elastic-pool-scale.md).|
| 10929 | 16 |Minimalna gwarancja% s wynosi% d, maksymalny limit wynosi% d, a bieżące użycie bazy danych to% d. Jednak serwer jest obecnie zbyt zajęty, aby obsługiwać żądania większe niż% d dla tej bazy danych. Aby uzyskać informacje na temat limitów zasobów, zobacz: <br/>&bull;[Limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md) &nbsp;<br/>&bull;[limity dla pul elastycznych rdzeń wirtualny.](sql-database-vcore-resource-limits-elastic-pools.md) &nbsp; <br/> W przeciwnym razie spróbuj ponownie później. Wartość DTU/rdzeń wirtualny min na bazę danych; Maksymalna liczba jednostek DTU/rdzeń wirtualny na bazę danych. Całkowita liczba współbieżnych procesów roboczych (żądań) dla wszystkich baz danych w puli elastycznej podjęła próbę przekroczenia limitu puli. |Rozważ zwiększenie DTU lub rdzeni wirtualnych puli elastycznej, jeśli jest to możliwe, aby zwiększyć jej limit procesów roboczych lub usunąć bazy danych z puli elastycznej. |
| 40844 | 16 |Baza danych "% ls" na serwerze "% ls" jest bazą danych wersji "% ls" w puli elastycznej i nie może mieć relacji ciągłego kopiowania.  |ND |
| 40857 | 16 |Nie znaleziono puli elastycznej dla serwera: "% ls", Nazwa puli elastycznej: "% ls". Określona Pula elastyczna nie istnieje na określonym serwerze. | Podaj prawidłową nazwę puli elastycznej. |
| 40858 | 16 |Pula elastyczna "% ls" już istnieje na serwerze: "% ls". Określona Pula elastyczna już istnieje w określonym SQL Database serwerze. | Podaj nazwę nowej puli elastycznej. |
| 40859 | 16 |Pula elastyczna nie obsługuje warstwy usług "% ls". Określona warstwa usług nie jest obsługiwana w przypadku aprowizacji elastycznej puli. |Wprowadź poprawną wersję lub pozostaw pustą warstwę usługi, aby użyć domyślnej warstwy usług. |
| 40860 | 16 |Kombinacja elastycznej puli "% ls" i celu usługi "% ls" jest nieprawidłowa. Pulę elastyczną i warstwę usług można określić razem tylko wtedy, gdy typ zasobu jest określony jako "ElasticPool". |Określ poprawną kombinację elastycznej puli i warstwy usług. |
| 40861 | 16 |Wersja bazy danych "%. *element ls ' nie może być inny niż warstwa usług puli elastycznej, która jest "%.* ls '. Wersja bazy danych różni się od warstwy usług puli elastycznej. |Nie określaj wersji bazy danych, która różni się od warstwy usług puli elastycznej.  Należy pamiętać, że nie trzeba określać wersji bazy danych. |
| 40862 | 16 |Jeśli określono cel usługi puli elastycznej, należy określić nazwę puli elastycznej. Cel usługi elastycznej puli nie identyfikuje jednoznacznie puli elastycznej. |Określ nazwę puli elastycznej, jeśli jest używany cel usługi puli elastycznej. |
| 40864 | 16 |DTU dla puli elastycznej musi wynosić co najmniej (% d) DTU dla warstwy usług "%. * ls". Podjęto próbę ustawienia DTU dla elastycznej puli poniżej limitu minimalnego. |Spróbuj ustawić co najmniej minimalny limit DTU dla puli elastycznej. |
| 40865 | 16 |DTU dla puli elastycznej nie może przekroczyć (% d) DTU dla warstwy usług "%. * ls". Podjęto próbę ustawienia DTU dla elastycznej puli powyżej maksymalnego limitu. |Ponowienie próby ustawienia DTU dla puli elastycznej nie przekracza maksymalnego limitu. |
| 40867 | 16 |Maksymalna liczba jednostek DTU na bazę danych musi wynosić co najmniej (% d) w przypadku warstwy usług "%. * ls". Podjęto próbę ustawienia maksymalnej liczby jednostek DTU na bazę danych poniżej obsługiwanego limitu. | Rozważ użycie warstwy usług puli elastycznej, która obsługuje odpowiednie ustawienie. |
| 40868 | 16 |Maksymalna liczba jednostek DTU na bazę danych nie może przekroczyć (% d) dla warstwy usług "%. * ls". Podjęto próbę ustawienia maksymalnej liczby jednostek DTU na bazę danych poza obsługiwanym limitem. | Rozważ użycie warstwy usług puli elastycznej, która obsługuje odpowiednie ustawienie. |
| 40870 | 16 |Minimalna wartość DTU na bazę danych nie może przekroczyć (% d) dla warstwy usług "%. * ls". Podjęto próbę ustawienia minimalnej liczby jednostek DTU na bazę danych poza obsługiwanym limitem. | Rozważ użycie warstwy usług puli elastycznej, która obsługuje odpowiednie ustawienie. |
| 40873 | 16 |Liczba baz danych (% d) i minimalna wartość DTU na bazę danych (% d) nie może przekraczać DTU puli elastycznej (% d). Podjęto próbę określenia minimalnej liczby jednostek DTU dla baz danych w puli elastycznej, które przekraczają DTU puli elastycznej. | Rozważ zwiększenie DTU puli elastycznej lub zmniejszenie minimalnej liczby jednostek DTU na bazę danych, lub Zmniejsz liczbę baz danych w puli elastycznej. |
| 40877 | 16 |Nie można usunąć puli elastycznej, jeśli nie zawiera ona żadnych baz danych. Pula elastyczna zawiera co najmniej jedną bazę danych i dlatego nie można jej usunąć. |Usuń bazy danych z elastycznej puli, aby je usunąć. |
| 40881 | 16 |Osiągnięto limit liczby baz danych dla puli elastycznej "%. * ls".  Limit liczby baz danych dla puli elastycznej nie może przekroczyć (% d) dla puli elastycznej z (% d) DTU. Podjęto próbę utworzenia lub dodania bazy danych do puli elastycznej, gdy osiągnięto limit liczby baz danych w puli elastycznej. | Rozważ zwiększenie DTU puli elastycznej, jeśli jest to możliwe, aby zwiększyć jej limit bazy danych, lub usuń bazy danych z puli elastycznej. |
| 40889 | 16 |Nie można zmniejszyć DTU lub limitu magazynu dla puli elastycznej "%. * ls", ponieważ nie zapewnia ona wystarczającej ilości miejsca w magazynie dla swoich baz danych. Podjęto próbę zmniejszenia limitu magazynowania elastycznej puli poniżej użycia magazynu. | Rozważ zmniejszenie użycia magazynu dla poszczególnych baz danych w elastycznej puli lub usuń bazy danych z puli, aby zmniejszyć limit DTU lub magazynu. |
| 40891 | 16 |Minimalna wartość DTU na bazę danych (% d) nie może przekroczyć maksymalnej wartości DTU na bazę danych (% d). Podjęto próbę ustawienia minimalnej wartości DTU na bazę danych wyższą niż maksymalna wartość DTU na bazę danych. |Upewnij się, że minimalna wartość DTU na bazy danych nie przekracza maksymalnej wartości DTU na bazę danych. |
| Do ustalenia | 16 |Rozmiar magazynu dla pojedynczej bazy danych w puli elastycznej nie może przekroczyć maksymalnego rozmiaru dozwolonego przez pulę elastyczną warstwy usług "%. * ls". Maksymalny rozmiar bazy danych przekracza maksymalny rozmiar dozwolony przez warstwę usługi elastycznej puli. |Ustaw maksymalny rozmiar bazy danych w granicach maksymalnego rozmiaru dozwolonego przez warstwę usług Elastic Pool. |

Tematy pokrewne:

* [Tworzenie puli elastycznej (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Zarządzanie pulą elastycznymi (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Tworzenie puli elastycznej (PowerShell)](sql-database-elastic-pool-manage-powershell.md)
* [Monitorowanie puli elastycznej (PowerShell) i zarządzanie nią](sql-database-elastic-pool-manage-powershell.md)

## <a name="general-errors"></a>Błędy ogólne

Następujące błędy nie należą do żadnych poprzednich kategorii.

| Kod błędu | severity | Opis |
| ---:| ---:|:--- |
| [15006](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-15000-to-15999) |16 |(AdministratorLogin) nie jest prawidłową nazwą, ponieważ zawiera nieprawidłowe znaki.|
| [18452](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |Logowanie nie powiodło się. Nazwa logowania pochodzi z niezaufanej domeny i nie można używać z systemu Windows authentication.%.&#x2a;ls (nazwy logowania systemu Windows nie są obsługiwane w tej wersji programu SQL Server). |
| [18456](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |Logowanie użytkownika "% nie powiodło się. &#x2a;ls ".%. &#x2a;% ls%. &#x2a;LS (Logowanie użytkownika "% nie powiodło się&#x2a; . ls ".) |
| [18470](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |Logowanie nie powiodło się dla użytkownika '%.&#x2a;ls'. Przyczyna: Konto jest wyłączone.%. &#x2a;ls |
| 40014 |16 |Nie można użyć wielu baz danych w tej samej transakcji. |
| 40054 |16 |Tabele bez indeksu klastrowanego nie są obsługiwane w tej wersji programu SQL Server. Utwórz klastrowany indeks i spróbuj ponownie. |
| 40133 |15 |Ta operacja nie jest obsługiwana w tej wersji programu SQL Server. |
| 40506 |16 |Określony identyfikator SID jest nieprawidłowy dla tej wersji SQL Server. |
| 40507 |16 |'%.&#x2a;ls' ls nie można wywołać z parametrami w tej wersji programu SQL Server. |
| 40508 |16 |Instrukcja USE nie jest obsługiwana w przypadku przełączania między bazami danych. Użyj nowego połączenia, aby nawiązać połączenie z inną bazą danych. |
| 40510 |16 |Instrukcja '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server |
| 40511 |16 |Wbudowana funkcja '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40512 |16 |Przestarzała funkcja "% ls" nie jest obsługiwana w tej wersji programu SQL Server. |
| 40513 |16 |Serwer zmiennej '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40514 |16 |element "% ls" nie jest obsługiwany w tej wersji programu SQL Server. |
| 40515 |16 |Odwołanie do nazwy bazy danych i/lub serwera w '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40516 |16 |Globalne obiekty tymczasowe są nieobsługiwane w tej wersji programu SQL Server. |
| 40517 |16 |Słowo kluczowe lub opcja instrukcji '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40518 |16 |Polecenie DBCC '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40520 |16 |Zabezpieczana Klasa "% S_MSG" jest nieobsługiwana w tej wersji SQL Server. |
| 40521 |16 |Zabezpieczana Klasa "% S_MSG" nie jest obsługiwana w zakresie serwera w tej wersji programu SQL Server. |
| 40522 |16 |Typ podmiotu zabezpieczeń '%.&#x2a;ls' w bazie danych nie jest obsługiwane w tej wersji programu SQL Server. |
| 40523 |16 |Tworzenie '%.&#x2a;ls' niejawnego użytkownika nie jest obsługiwane w tej wersji programu SQL Server. Jawnie Utwórz użytkownika przed jego użyciem. |
| 40524 |16 |Typ danych '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40525 |16 |Element "%. ls" jest nieobsługiwany w tej wersji programu SQL Server. |
| 40526 |16 |'%.&#x2a;ls' dostawcy zestawu wierszy ls nie jest obsługiwany w tej wersji programu SQL Server. |
| 40527 |16 |Połączone serwery są nieobsługiwane w tej wersji programu SQL Server. |
| 40528 |16 |Użytkowników nie można mapować na certyfikaty, klucze asymetryczne ani nazwy logowania systemu Windows w tej wersji programu SQL Server. |
| 40529 |16 |Wbudowana funkcja '%.&#x2a;ls' w personifikacji kontekst nie jest obsługiwany w tej wersji programu SQL Server. |
| 40532 |11 |Nie można otworzyć serwera "%.&#x2a;ls" żądanego podczas logowania. Logowanie nie powiodło się. |
| 40553 |16 |Sesja została przerwana z powodu nadmiernego użycia pamięci. Spróbuj zmodyfikować zapytanie, aby przetworzyć mniej wierszy.<br/><br/> Zmniejszenie liczby `ORDER BY` i `GROUP BY` operacji w kodzie Transact-SQL pomaga zmniejszyć wymagania dotyczące pamięci zapytania. |
| 40604 |16 |Nie można utworzyć/zmodyfikować bazy danych, ponieważ spowodowałoby to przekroczenie limitu przydziału serwera. |
| 40606 |16 |Dołączanie baz danych nie jest obsługiwane w tej wersji programu SQL Server. |
| 40607 |16 |Nazwy logowania systemu Windows są nieobsługiwane w tej wersji programu SQL Server. |
| 40611 |16 |Serwery mogą mieć co najwyżej 128 reguł zapory. |
| 40614 |16 |Początkowy adres IP reguły zapory nie może być większy niż końcowy adres IP. |
| 40615 |16 |Nie można otworzyć serwera{0}"" żądanego przez nazwę logowania. Klient z adresem IP "{1}" nie może uzyskać dostępu do serwera.<br /><br />Aby włączyć dostęp, użyj portalu SQL Database lub uruchom\_regułę zapory Ustaw\_zaporę\_w bazie danych Master, aby utworzyć regułę zapory dla tego adresu IP lub zakresu adresów. Wprowadzenie zmian może potrwać do 5 minut. |
| 40617 |16 |Nazwa reguły zapory rozpoczynająca się od (Nazwa reguły) jest za długa. Maksymalna długość to 128. |
| 40618 |16 |Nazwa reguły zapory nie może być pusta. |
| 40620 |16 |Logowanie nie powiodło się dla użytkownika "%.&#x2a;ls". Zmienianie hasła nie powiodło się. Zmiana hasła podczas logowania nie jest obsługiwana w tej wersji programu SQL Server. |
| 40627 |20 |Operacja na serwerze "{0}" i w bazie{1}danych "" jest w toku. Zaczekaj kilka minut przed ponowieniem próby. |
| 40630 |16 |Weryfikacja hasła nie powiodła się. Hasło nie spełnia wymagań zasad, ponieważ jest zbyt krótkie. |
| 40631 |16 |Podane hasło jest zbyt długie. Hasło nie może zawierać więcej niż 128 znaków. |
| 40632 |16 |Weryfikacja hasła nie powiodła się. Hasło nie spełnia wymagań zasad, ponieważ nie jest wystarczająco złożone. |
| 40636 |16 |Nie można użyć zastrzeżonej nazwy bazy danych '%.&#x2a;ls' w tej operacji. |
| 40638 |16 |Nieprawidłowy identyfikator subskrypcji (Identyfikator subskrypcji). Subskrypcja nie istnieje. |
| 40639 |16 |Żądanie jest niezgodne ze schematem: (błąd schematu). |
| 40640 |20 |Serwer napotkał nieoczekiwany wyjątek. |
| 40641 |16 |Określona lokalizacja jest nieprawidłowa. |
| 40642 |17 |Serwer jest obecnie zbyt zajęty. Spróbuj ponownie później. |
| 40643 |16 |Określona wartość nagłówka x-MS-Version jest nieprawidłowa. |
| 40644 |14 |Nie można autoryzować dostępu do określonej subskrypcji. |
| 40645 |16 |ServerName (ServerName) nie może być pusty ani mieć wartości null. Może zawierać tylko małe litery "od-a", cyfry 0-9 i łącznik. Łącznik nie może prowadzić do ani kończyć się nazwą. |
| 40646 |16 |Identyfikator subskrypcji nie może być pusty. |
| 40647 |16 |Subskrypcja (Identyfikator subskrypcji) nie ma serwera (ServerName). |
| 40648 |17 |Wykonano zbyt wiele żądań. Spróbuj ponownie później. |
| 40649 |16 |Określono nieprawidłowy typ zawartości. Obsługiwane są tylko aplikacje/XML. |
| 40650 |16 |Subskrypcja (Identyfikator subskrypcji) nie istnieje lub nie jest gotowa do wykonania operacji. |
| 40651 |16 |Nie można utworzyć serwera, ponieważ subskrypcja (Identyfikator subskrypcji) jest wyłączona. |
| 40652 |16 |Nie można przenieść ani utworzyć serwera. Subskrypcja (Identyfikator subskrypcji) spowoduje przekroczenie limitu przydziału serwera. |
| 40671 |17 |Błąd komunikacji między bramą a usługą zarządzania. Spróbuj ponownie później. |
| 40852 |16 |Nie można otworzyć bazy danych "%. \*ls "na serwerze"%. \*element LS zażądał identyfikatora logowania. Dostęp do bazy danych jest dozwolony tylko przy użyciu parametrów połączenia z włączoną obsługą zabezpieczeń. Aby uzyskać dostęp do tej bazy danych, zmodyfikuj parametry połączenia tak, aby zawierały wartość "Secure" w nazwie FQDN serwera — "Nazwa serwera". baza danych. Windows. NET należy zmodyfikować na "Nazwa serwera". baza danych. `secure`. Windows.NET. |
| 40914 | 16 | Nie można otworzyć serwera " *[nazwa serwera]* " żądanego podczas logowania. Klient nie może uzyskać dostępu do serwera.<br /><br />Aby rozwiązać ten problem, należy rozważyć dodanie [reguły sieci wirtualnej](sql-database-vnet-service-endpoint-rule-overview.md). |
| 45168 |16 |System SQL Azure jest objęty obciążeniem i wprowadza górny limit współbieżnych operacji CRUD DB dla jednego serwera SQL Database (np. Create Database). Serwer określony w komunikacie o błędzie przekroczył maksymalną liczbę jednoczesnych połączeń. Spróbuj ponownie później. |
| 45169 |16 |System SQL Azure jest objęty obciążeniem i wprowadza górny limit liczby współbieżnych operacji serwera CRUD dla jednej subskrypcji (np. Create Server). Subskrypcja określona w komunikacie o błędzie przekroczyła maksymalną liczbę jednoczesnych połączeń i żądanie zostało odrzucone. Spróbuj ponownie później. |

## <a name="next-steps"></a>Kolejne kroki

* Przeczytaj o [Azure SQL Database funkcjach](sql-database-features.md).
* Przeczytaj informacje [na temat modelu zakupu opartego na](sql-database-service-tiers-dtu.md)jednostkach DTU.
* Przeczytaj o [modelu zakupu opartego na rdzeń wirtualny](sql-database-service-tiers-vcore.md).

