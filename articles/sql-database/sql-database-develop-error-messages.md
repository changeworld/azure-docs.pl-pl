---
title: Kody błędów SQL — błąd połączenia bazy danych | Dokumentacja firmy Microsoft
description: 'Więcej informacji na temat kody błędów SQL dla aplikacji klienckich bazy danych SQL, takich jak typowych błędów połączenia bazy danych, problemy z kopii bazy danych i ogólne błędy. '
keywords: Kod błędu SQL, sql dostępu, błąd połączenia bazy danych, kody błędów sql
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/06/2019
ms.openlocfilehash: 2682f98628f3c1cf22a2c3767f52bedbc148fa62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60723501"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>Kody błędów SQL dla aplikacji klienckich bazy danych SQL: Błędy połączenia bazy danych i inne problemy

Ten artykuł zawiera listę kodów błędów programu SQL dla aplikacji klienckich bazy danych SQL, w tym błędów połączenia bazy danych, błędy przejściowe (nazywane również błędów przejściowych), błędy nadzoru zasobów, problemów kopii bazy danych, puli elastycznej i inne błędy. Większość kategorii dla usługi Azure SQL Database, a nie dotyczą programu Microsoft SQL Server. Zobacz też [komunikaty o błędach systemu](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Błędy połączenia bazy danych, błędy przejściowe i innych tymczasowe błędy

Poniższa tabela zawiera kody błędów SQL dla błędów utraty połączenia i innych błędów przejściowych, które mogą wystąpić, gdy aplikacja próbuje dostępu do bazy danych SQL. W celu uzyskania wprowadzenie samouczki na temat do łączenia z usługą Azure SQL Database, zobacz [nawiązywania połączenia z bazą danych SQL Azure](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Najbardziej typowe błędy połączenia bazy danych i błędów przejściowych błędów

Infrastruktura platformy Azure ma możliwość dynamicznego ponownego konfigurowania serwerów w przypadku wystąpienia dużych obciążeń w usłudze SQL Database.  To dynamiczne zachowanie może powodować, że program kliencki utraci połączenie z usługą SQL Database. Ten rodzaj błędu nazywa się *błędu przejściowego*.

Zdecydowanie zaleca się program kliencki ma logikę ponawiania próby, aby dało się je można ponownie ustanowić połączenie po nadaniu Popraw sam czas błędu przejściowego.  Firma Microsoft zaleca opóźnienia na 5 sekund przed swoje pierwsze ponowienie. Ponawianie próby po opóźnieniu mniej niż 5 sekund ryzyka przeciąża usługę w chmurze. Na każdym kolejnym ponowieniem próby opóźnienie powinien rosnąć wykładniczo maksymalnie do 60 sekund.

Błędów przejściowych błędów zwykle objawiać się jedną z następujących komunikatów o błędach z programów klienckich:

* Baza danych &lt;db_name&gt; na serwerze &lt;Azure_instance&gt; nie jest obecnie dostępna. Ponów próbę połączenia później. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną i podaj identyfikator śledzenia sesji &lt;session_id&gt;
* Baza danych &lt;db_name&gt; na serwerze &lt;Azure_instance&gt; nie jest obecnie dostępna. Ponów próbę połączenia później. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną i podaj identyfikator śledzenia sesji &lt;session_id&gt;. (Microsoft SQL Server, Error: 40613)
* Istniejące połączenie zostało zamknięte przez hosta zdalnego.
* System.Data.Entity.Core.EntityCommandExecutionException: Wystąpił błąd podczas wykonywania definicji polecenia. Zobacz wyjątek wewnętrzny, aby uzyskać szczegółowe informacje. ---> System.Data.SqlClient.SqlException: Poziom transportu wystąpił błąd podczas odbierania wyników z serwera. (Dostawca: Dostawcy sesji, błąd: 19 - fizyczne połączenie nie jest używany)
* Próba połączenia pomocniczej bazy danych nie powiodła się, ponieważ baza danych jest w trakcie ponownej konfiguracji i jest zajęty, zastosowanie nowych stron w trakcie wykonywania aktywnej transakcji w głównej bazie danych. 

Aby uzyskać przykłady kodu logikę ponawiania próby zobacz:

* [Biblioteki połączeń dla usługi SQL Database i programu SQL Server](sql-database-libraries.md) 
* [Działania, aby naprawić błędy połączeń i błędów przejściowych w usłudze SQL Database](sql-database-connectivity-issues.md)

Omówienie *czasu blokowania* dla klientów używających ADO.NET jest dostępna w [programu SQL Server połączenia puli (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Kody błędów przejściowych błędów

Następujące błędy są przejściowe i powinno być ponowione w aplikacji logiki: 

| Kod błędu | Severity | Opis |
| ---:| ---:|:--- |
| 4060 |16 |Nie można otworzyć bazy danych "%.&#x2a; ls" żądanego podczas logowania. Logowanie nie powiodło się. Aby uzyskać więcej informacji, zobacz [błędy 4000 do 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |Usługa napotkała błąd podczas przetwarzania Twojego żądania. Spróbuj ponownie. Kod błędu: %d.<br/><br/>Ten błąd jest wyświetlany, gdy usługa nie działa z powodu oprogramowania lub Modernizacje sprzętu, awarii sprzętu lub innych problemów trybu failover. Kod błędu: (%d) osadzane komunikat o błędzie 40197 zawiera dodatkowe informacje dotyczące rodzaju awarii lub pracy awaryjnej, który wystąpił. Niektóre przykłady błąd, który kodów są osadzane komunikat o błędzie 40197 są 40020, 40143, 40166 i 40540.<br/><br/>Ponowne nawiązywanie połączenia z serwerem bazy danych SQL Database automatycznie łączy dobrej kondycji kopię bazy danych. Aplikację należy przechwytywać 40197, dziennik błędów osadzony kod błędu: (%d) w ramach komunikatu w celu rozwiązywania problemów i ponowić próbę połączenia do bazy danych SQL, dopóki zasoby są dostępne, a następnie połączenie zostanie nawiązane ponownie. Aby uzyskać więcej informacji, zobacz [błędów przejściowych](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |Usługa jest obecnie zajęta. Ponów żądanie po 10 sekundach. Identyfikator zdarzenia: %ls. Kod: %d. Aby uzyskać więcej informacji, zobacz: <br/>&bull; &nbsp;[Limity zasobów na serwerze bazy danych](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[Limity oparty na jednostkach DTU dla pojedynczej bazy danych](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[Oparte na jednostkach DTU limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[oparty na rdzeniach wirtualnych limity dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[oparty na rdzeniach wirtualnych limity dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md).|
| 40613 |17 |Baza danych '%.&#x2a;ls' na serwerze '%.&#x2a;ls' nie jest obecnie dostępna. Ponów próbę połączenia później. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną i podaj identyfikator śledzenia sesji '%.&#x2a;ls'.<br/><br/> Ten błąd może wystąpić, jeśli istnieje już istniejące dedykowane połączenie administratora (DAC) ustalone w bazie danych. Aby uzyskać więcej informacji, zobacz [błędów przejściowych](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |Nie można przetworzyć żądania. Za mało zasobów do przetwarzania żądania.<br/><br/>Usługa jest obecnie zajęta. Ponów próbę żądania później. Aby uzyskać więcej informacji, zobacz: <br/>&bull; &nbsp;[Limity zasobów na serwerze bazy danych](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[Limity oparty na jednostkach DTU dla pojedynczej bazy danych](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[Oparte na jednostkach DTU limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[oparty na rdzeniach wirtualnych limity dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[oparty na rdzeniach wirtualnych limity dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md). |
| 49919 |16 |Nie można przetworzyć tworzenia lub aktualizacji żądania. Zbyt wiele operacji tworzenia lub aktualizacji w toku dla subskrypcji "% ld".<br/><br/>Usługa jest zajęta przetwarzania wielu Tworzenie lub żądania dla Twojej subskrypcji lub serwera aktualizacji. Żądania są obecnie zablokowane do optymalizacji zasobów. Zapytanie [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) dla oczekujących operacji. Poczekaj, aż do czasu tworzenia lub aktualizacji żądania są ukończone lub usuń jedno z oczekujących żądań i ponów żądanie później. Aby uzyskać więcej informacji, zobacz: <br/>&bull; &nbsp;[Limity zasobów na serwerze bazy danych](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[Limity oparty na jednostkach DTU dla pojedynczej bazy danych](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[Oparte na jednostkach DTU limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[oparty na rdzeniach wirtualnych limity dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[oparty na rdzeniach wirtualnych limity dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md). |
| 49920 |16 |Nie można przetworzyć żądania. Za dużo operacji w toku dla subskrypcji "% ld".<br/><br/>Usługa jest zajęta przetwarzaniem wiele żądań dla tej subskrypcji. Żądania są obecnie zablokowane do optymalizacji zasobów. Zapytanie [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) dotyczące stanu operacji. Zaczekaj, aż oczekujące żądania są zakończone lub usuń jedno z oczekujących żądań i ponów żądanie później. Aby uzyskać więcej informacji, zobacz: <br/>&bull; &nbsp;[Limity zasobów na serwerze bazy danych](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[Limity oparty na jednostkach DTU dla pojedynczej bazy danych](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[Oparte na jednostkach DTU limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[oparty na rdzeniach wirtualnych limity dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[oparty na rdzeniach wirtualnych limity dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md). |
| 4221 |16 |Zaloguj się do pomocniczej odczytu nie powiodło się z powodu długiego oczekiwania na "HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING". Replika jest niedostępna dla nazwy logowania, ponieważ brakuje wersji wierszy, transakcje, które były aktywne podczas odtwarzania repliki. Ten problem można rozwiązać przez przywrócenie lub Zatwierdź aktywne transakcje w replice podstawowej. Wystąpienia tego warunku można zminimalizować, unikając długich transakcji zapisu na serwerze podstawowym. |

## <a name="database-copy-errors"></a>Błędy kopiowania bazy danych

Następujące błędy mogą podczas kopiowania bazy danych w usłudze Azure SQL Database. Więcej informacji znajdziesz w artykule [Kopiowanie bazy danych usługi Azure SQL Database](sql-database-copy.md).

| Kod błędu | Severity | Opis |
| ---:| ---:|:--- |
| 40635 |16 |Klient o adresie IP '%.&#x2a;ls' jest tymczasowo wyłączona. |
| 40637 |16 |Utwórz kopię bazy danych jest obecnie wyłączona. |
| 40561 |16 |Kopiowanie bazy danych nie powiodło się. Źródłowa lub docelowa baza danych nie istnieje. |
| 40562 |16 |Kopiowanie bazy danych nie powiodło się. Źródłowa baza danych została porzucona. |
| 40563 |16 |Kopiowanie bazy danych nie powiodło się. Docelowa baza danych została porzucona. |
| 40564 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40565 |16 |Kopiowanie bazy danych nie powiodło się. Nie więcej niż 1 kopia współbieżnych bazy danych z tego samego źródła jest dozwolone. Porzuć docelową bazę danych i spróbuj ponownie później. |
| 40566 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40567 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40568 |16 |Kopiowanie bazy danych nie powiodło się. Źródłowa baza danych stała się niedostępna. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40569 |16 |Kopiowanie bazy danych nie powiodło się. Docelowa baza danych stała się niedostępna. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40570 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie później. |
| 40571 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie później. |

## <a name="resource-governance-errors"></a>Błędy nadzoru zasobów

Następujące błędy są spowodowane nadmiernego wykorzystania zasobów podczas pracy z usługą Azure SQL Database. Na przykład:

* Transakcja została otwarta do zbyt długo.
* Transakcji zawiera zbyt wiele blokad.
* Aplikacja zużywa zbyt dużej ilości pamięci.
* Aplikacja zużywa zbyt dużo `TempDb` miejsca.

Tematy pokrewne:

* Aby uzyskać więcej informacji, zobacz:
  * [Limity zasobów na serwerze bazy danych](sql-database-resource-limits-database-server.md)
  * [Limity oparty na jednostkach DTU dla pojedynczej bazy danych](sql-database-service-tiers-dtu.md)
  * [Oparte na jednostkach DTU limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)
  * [oparty na rdzeniach wirtualnych limity dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)
  * [oparty na rdzeniach wirtualnych limity dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md)
  * [Limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md). 

| Kod błędu | Severity | Opis |
| ---:| ---:|:--- |
| 10928 |20 |Identyfikator zasobu: %d. Limit %s dla bazy danych wynosi %d i został osiągnięty. Aby uzyskać więcej informacji, zobacz [limity zasobów bazy danych SQL Database dla pojedynczych i puli baz danych](sql-database-resource-limits-database-server.md).<br/><br/>Identyfikator zasobu wskazuje zasób, który osiągnął limit. Dla wątków, identyfikator zasobu = 1. Dla sesji, identyfikator zasobu = 2.<br/><br/>Aby uzyskać więcej informacji na temat tego błędu i sposobie jego rozwiązania zobacz: <br/>&bull; &nbsp;[Limity zasobów na serwerze bazy danych](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[Limity oparty na jednostkach DTU dla pojedynczej bazy danych](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[Oparte na jednostkach DTU limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[oparty na rdzeniach wirtualnych limity dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[oparty na rdzeniach wirtualnych limity dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md). |
| 10929 |20 |Identyfikator zasobu: %d. Gwarancji minimalne %s to %d, maksymalny limit to %d, a bieżące użycie dla bazy danych to %d. Jednak serwer jest obecnie zbyt zajęty, aby obsługiwać żądania przekracza %d dla tej bazy danych. Identyfikator zasobu wskazuje zasób, który osiągnął limit. Dla wątków, identyfikator zasobu = 1. Dla sesji, identyfikator zasobu = 2. Aby uzyskać więcej informacji, zobacz: <br/>&bull; &nbsp;[Limity zasobów na serwerze bazy danych](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[Limity oparty na jednostkach DTU dla pojedynczej bazy danych](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[Oparte na jednostkach DTU limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[oparty na rdzeniach wirtualnych limity dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[oparty na rdzeniach wirtualnych limity dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md). <br/>W przeciwnym razie spróbuj ponownie później. |
| 40544 |20 |Baza danych osiągnęła limit przydziału rozmiaru. Partycji lub Usuń dane, Porzuć indeksy lub zapoznaj się z dokumentacją, aby ustalić możliwe rozwiązania. Skalowanie bazy danych, zobacz [skalowanie pojedynczej bazy danych zasobów](sql-database-single-database-scale.md) i [skalowanie elastycznej puli zasobów](sql-database-elastic-pool-scale.md).|
| 40549 |16 |Sesja jest przerwana z powodu długotrwałej transakcji. Spróbuj skrócić transakcję. Aby uzyskać informacji na temat dzielenia na partie, zobacz [jak zwiększyć wydajność aplikacji bazy danych SQL za pomocą adapterów przetwarzania wsadowego](sql-database-use-batching-to-improve-performance.md).|
| 40550 |16 |Sesja została przerwana, ponieważ uzyskała zbyt wiele blokad. Spróbuj odczytu lub modyfikować mniej wierszy w ramach jednej transakcji. Aby uzyskać informacji na temat dzielenia na partie, zobacz [jak zwiększyć wydajność aplikacji bazy danych SQL za pomocą adapterów przetwarzania wsadowego](sql-database-use-batching-to-improve-performance.md).|
| 40551 |16 |Sesja została przerwana z powodu nadmiernego `TEMPDB` użycia. Spróbuj zmodyfikować zapytanie, aby zmniejszyć użycie miejsca na tabeli tymczasowej.<br/><br/>Jeśli używasz obiektów tymczasowych oszczędzania przestrzeni dyskowej w `TEMPDB` bazy danych po ich nie są już potrzebne w sesji, porzucając obiekty tymczasowe. Aby uzyskać więcej informacji na temat użycia bazy danych tempdb w bazie danych SQL, zobacz [bazy danych Tempdb w bazie danych SQL](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |Sesja została przerwana z powodu użycia miejsca na dziennik transakcji nadmierne. Spróbuj modyfikować mniej wierszy w ramach jednej transakcji. Aby uzyskać informacji na temat dzielenia na partie, zobacz [jak zwiększyć wydajność aplikacji bazy danych SQL za pomocą adapterów przetwarzania wsadowego](sql-database-use-batching-to-improve-performance.md).<br/><br/>Jeśli wykonujesz zbiorcze wstawia przy użyciu `bcp.exe` narzędzia lub `System.Data.SqlClient.SqlBulkCopy` klasy, spróbuj użyć `-b batchsize` lub `BatchSize` opcji, aby ograniczyć liczbę wierszy kopiowana na serwer w każdej transakcji. Do odbudowywania indeksu o `ALTER INDEX` instrukcji, spróbuj użyć `REBUILD WITH ONLINE = ON` opcji. Aby uzyskać informacji na temat rozmiarów dziennika transakcji dla model zakupu w rdzeniach wirtualnych zobacz: <br/>&bull; &nbsp;[oparty na rdzeniach wirtualnych limity dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[oparty na rdzeniach wirtualnych limity dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md).|
| 40553 |16 |Sesja została przerwana z powodu nadmiernego wykorzystania pamięci. Spróbuj zmodyfikować zapytanie, tak aby przetwarzało mniejszą liczbę wierszy.<br/><br/>Zmniejszenie liczby `ORDER BY` i `GROUP BY` operacji w kodzie języka Transact-SQL zmniejsza wymagania dotyczące pamięci zapytania. Skalowanie bazy danych, zobacz [skalowanie pojedynczej bazy danych zasobów](sql-database-single-database-scale.md) i [skalowanie elastycznej puli zasobów](sql-database-elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Błędy puli elastycznej

Następujące błędy odnoszą się do tworzenia i używania pul elastycznych:

| Kod błędu | Severity | Opis | Działania naprawcze |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Osiągnięto limit przestrzeni dyskowej w puli elastycznej. Użycie magazynu dla puli elastycznej nie może przekroczyć (%d) MB. Podjęto próbę zapisu danych do bazy danych, gdy został osiągnięty limit magazynu elastycznej puli. Aby uzyskać informacji na temat limitów zasobów Zobacz: <br/>&bull; &nbsp;[Oparte na jednostkach DTU limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[oparty na rdzeniach wirtualnych limity dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md). <br/> |Należy rozważyć zwiększenie Dtu i/lub Dodawanie magazynu do elastycznej puli, jeśli jest to możliwe, aby zwiększyć limit przestrzeni dyskowej, zmniejszenie miejsca używanego przez poszczególnych baz danych w puli elastycznej lub usuwać bazy danych z puli elastycznej. Do skalowania elastycznej puli, zobacz [skalowanie elastycznej puli zasobów](sql-database-elastic-pool-scale.md).|
| 10929 | 16 |Gwarancji minimalne %s to %d, maksymalny limit to %d, a bieżące użycie dla bazy danych to %d. Jednak serwer jest obecnie zbyt zajęty, aby obsługiwać żądania przekracza %d dla tej bazy danych. Aby uzyskać informacji na temat limitów zasobów Zobacz: <br/>&bull; &nbsp;[Oparte na jednostkach DTU limity dla pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[oparty na rdzeniach wirtualnych limity dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md). <br/> W przeciwnym razie spróbuj ponownie później. Jednostka DTU / minimalna liczba rdzeni wirtualnych na bazę danych; Jednostka DTU / maksymalna liczba na bazę danych rdzeni wirtualnych. Całkowita liczba współbieżnych procesów roboczych (żądań) we wszystkich bazach danych w puli elastycznej próba przekracza limit puli. |Należy rozważyć zwiększenie liczby jednostek Dtu lub rdzeni wirtualnych w puli elastycznej w miarę możliwości w celu zwiększenia limitu jego procesów roboczych lub usuwać bazy danych z puli elastycznej. |
| 40844 | 16 |Baza danych "%ls" na serwerze "%ls" jest bazą danych w wersji "%ls" w puli elastycznej i nie może mieć relacji ciągłych kopii.  |ND |
| 40857 | 16 |Nie znaleziono serwera puli elastycznej: "%ls", nazwa puli elastycznej: "%ls". Określona pula elastyczna nie istnieje na określonym serwerze. | Podaj nazwę puli elastycznej prawidłowe. |
| 40858 | 16 |Pula elastyczna "%ls" już istnieje na serwerze: "%ls". Określona pula elastyczna już istnieje na określonym serwerze SQL Database. | Podaj nową nazwę puli elastycznej. |
| 40859 | 16 |Pula elastyczna nie obsługuje warstwy usług "%ls". Warstwa określonej usługi nie jest obsługiwana dla puli elastycznej inicjowania obsługi administracyjnej. |Podaj poprawną wersję, lub pozostaw puste, aby użyć domyślnej warstwy usługi warstwy usług. |
| 40860 | 16 |Kombinacja puli elastycznej "%ls" i usługi cel "%ls" jest nieprawidłowa. Elastycznej puli i usługi warstwy mogą jednocześnie można określić tylko wtedy, gdy typ zasobu jest określony jako "ElasticPool". |Określ poprawny kombinacja puli elastycznej i warstwy usług. |
| 40861 | 16 |Wersja bazy danych "%. *ls nie może być inna niż warstwa usług puli elastycznej, czyli "%.* ls. Wersja bazy danych jest inna niż warstwa usług puli elastycznej. |Nie można określić wersji bazy danych, która jest inna niż warstwa usług puli elastycznej.  Należy pamiętać, że wersja bazy danych nie muszą być określone. |
| 40862 | 16 |Nazwa puli elastycznej musi być określona, jeśli określono cel usług puli elastycznej. Cel usług puli elastycznej nie identyfikuje jednoznacznie puli elastycznej. |Określ nazwę puli elastycznej, jeśli za pomocą cel usług puli elastycznej. |
| 40864 | 16 |Liczba jednostek Dtu dla puli elastycznej musi wynosić co najmniej (%d) liczby jednostek Dtu, dla warstwy usług "%. * ls. Trwa próba skonfigurowania jednostek Dtu dla puli elastycznej poniżej minimalnego limitu. |Ustawienie liczby jednostek Dtu dla elastycznej puli co najmniej minimalny limit ponownych prób. |
| 40865 | 16 |Liczba jednostek Dtu dla puli elastycznej nie może przekroczyć (%d) liczby jednostek Dtu, dla warstwy usług "%. * ls. Trwa próba skonfigurowania jednostek Dtu dla puli elastycznej przekracza maksymalny limit. |Ustawienie liczby jednostek Dtu dla puli elastycznej nie może przekraczać maksymalny limit ponownych prób. |
| 40867 | 16 |Maksymalna wartość DTU na bazę danych musi wynosić co najmniej (%d) dla warstwy usług "%. * ls. Trwa próba skonfigurowania maksymalna liczba jednostek DTU na bazę danych poniżej obsługiwany limit. | Należy rozważyć użycie warstwa usług puli elastycznej, która obsługuje odpowiednie ustawienie. |
| 40868 | 16 |Maksymalna wartość DTU na bazę danych nie może przekroczyć (%d) dla warstwy usług "%. * ls. Trwa próba skonfigurowania maksymalna liczba jednostek DTU na bazę danych przekracza obsługiwany limit. | Należy rozważyć użycie warstwa usług puli elastycznej, która obsługuje odpowiednie ustawienie. |
| 40870 | 16 |Minimalna wartość DTU na bazę danych nie może przekroczyć (%d) dla warstwy usług "%. * ls. Trwa próba skonfigurowania minimalna wartość DTU na bazę danych przekracza obsługiwany limit. | Należy rozważyć użycie warstwa usług puli elastycznej, która obsługuje odpowiednie ustawienie. |
| 40873 | 16 |Liczba baz danych (%d) i minimalna wartość DTU na bazę danych (%d) nie może przekraczać liczby jednostek Dtu puli elastycznej (%d). Podjęto próbę określenia minimalnej liczby jednostek DTU dla baz danych w elastycznej puli, która przekracza wartość Dtu puli elastycznej. | Należy rozważyć zwiększenie liczby jednostek Dtu puli elastycznej, Zmniejsz minimalna wartość DTU na bazę danych lub Zmniejsz liczbę baz danych w puli elastycznej. |
| 40877 | 16 |Nie można usunąć puli elastycznej, chyba że nie zawiera żadnych baz danych. Elastyczna pula zawiera jeden lub więcej baz danych i nie można usunąć. |Usunąć z puli elastycznej bazy danych, aby można było go usunąć. |
| 40881 | 16 |Pula elastyczna "%. * ls osiągnął limit liczby bazy danych.  Limit liczby bazy danych dla puli elastycznej nie może przekroczyć (%d) dla puli elastycznej przy użyciu jednostek Dtu (%d). Podjęto próbę utworzyć lub dodać do puli elastycznej bazy danych, gdy został osiągnięty limit liczby bazy danych w puli elastycznej. | Należy rozważyć zwiększenie liczby jednostek Dtu puli elastycznej, jeśli jest to możliwe, aby zwiększyć limit bazy danych lub usuwać bazy danych z puli elastycznej. |
| 40889 | 16 |Liczba jednostek Dtu lub limitu magazynowania dla puli elastycznej "%. * ls nie można zmniejszyć, ponieważ nie zapewniłoby z wystarczającą ilością wolnego miejsca dla baz danych. Podjęto próbę zmniejszyć limit magazynowania puli elastycznej poniżej jego użycie magazynu. | Rozważ zmniejszenie użycia pamięci masowej pojedynczych baz danych w puli elastycznej lub usunięcie baz danych z puli, aby zmniejszyć jego Dtu lub limitu magazynowania. |
| 40891 | 16 |Minimalna wartość DTU na bazę danych (%d) nie może przekroczyć maksymalnej wartości DTU na bazę danych (%d). Trwa próba skonfigurowania minimalna wartość DTU na bazę danych jest wyższa niż maksymalna liczba jednostek DTU na bazę danych. |Upewnij się, że minimalna wartość DTU na bazy danych nie przekracza maksymalnej wartości DTU na bazę danych. |
| TBD | 16 |Rozmiar magazynu dla poszczególnych baz danych w puli elastycznej nie może przekraczać maksymalny rozmiar dozwolony przez "%. * ls usług puli elastycznej warstwy. Maksymalny rozmiar bazy danych przekracza maksymalny rozmiar dozwolony przez warstwę usług puli elastycznej. |Ustaw maksymalny rozmiar bazy danych w ramach limitów maksymalny rozmiar dozwolony przez warstwę usług puli elastycznej. |

Tematy pokrewne:

* [Tworzenie puli elastycznej (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Zarządzanie pulą elastyczną (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Tworzenie puli elastycznej (PowerShell)](sql-database-elastic-pool-manage-powershell.md)
* [Monitorowanie i zarządzanie pulą elastyczną (PowerShell)](sql-database-elastic-pool-manage-powershell.md)

## <a name="general-errors"></a>Ogólne błędy

Następujące błędy, które nie należą do żadnych poprzednich kategorii.

| Kod błędu | Severity | Opis |
| ---:| ---:|:--- |
| [15006](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-15000-to-15999) |16 |(AdministratorLogin) nie jest prawidłową nazwą, ponieważ zawiera ona nieprawidłowe znaki.|
| [18452](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |Logowanie nie powiodło się. Nazwa logowania pochodzi z niezaufanej domeny i nie można używać z systemu Windows authentication.%.&#x2a;ls (nazwy logowania systemu Windows nie są obsługiwane w tej wersji programu SQL Server). |
| [18456](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |Nie można zalogować użytkownika "%. &#x2a;ls'.%. &#x2a;ls %. &#x2a;ls (nie można zalogować użytkownika "%.&#x2a; ls".) |
| [18470](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |Logowanie nie powiodło się dla użytkownika '%.&#x2a;ls'. Przyczyna: Konto jest disabled.%. &#x2a;ls |
| 40014 |16 |Nie można używać wielu baz danych w ramach jednej transakcji. |
| 40054 |16 |Tabele bez indeksu klastrowanego są nieobsługiwane w tej wersji programu SQL Server. Utwórz indeks klastrowany i spróbuj ponownie. |
| 40133 |15 |Ta operacja jest nieobsługiwana w tej wersji programu SQL Server. |
| 40506 |16 |Określony identyfikator SID jest nieprawidłowy dla tej wersji programu SQL Server. |
| 40507 |16 |'%.&#x2a;ls' ls nie można wywołać z parametrami w tej wersji programu SQL Server. |
| 40508 |16 |Użycie instrukcji use nie jest obsługiwana, aby przełączyć się między bazami danych. Użyj nowego połączenia, aby nawiązać połączenie z inną bazą danych. |
| 40510 |16 |Instrukcja '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server |
| 40511 |16 |Wbudowana funkcja '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40512 |16 |Przestarzałe funkcja "%ls" nie jest obsługiwana w tej wersji programu SQL Server. |
| 40513 |16 |Serwer zmiennej '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40514 |16 |"%ls" nie jest obsługiwane w tej wersji programu SQL Server. |
| 40515 |16 |Odwołanie do nazwy bazy danych i/lub serwera w '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40516 |16 |Globalne obiekty tymczasowe są nieobsługiwane w tej wersji programu SQL Server. |
| 40517 |16 |Słowo kluczowe lub opcja instrukcji '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40518 |16 |Polecenie DBCC '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40520 |16 |Zabezpieczana klasa "% S_MSG" jest nieobsługiwane w tej wersji programu SQL Server. |
| 40521 |16 |Zabezpieczana klasa "% S_MSG" jest nieobsługiwana w zakresie serwera w tej wersji programu SQL Server. |
| 40522 |16 |Typ podmiotu zabezpieczeń '%.&#x2a;ls' w bazie danych nie jest obsługiwane w tej wersji programu SQL Server. |
| 40523 |16 |Tworzenie '%.&#x2a;ls' niejawnego użytkownika nie jest obsługiwane w tej wersji programu SQL Server. Jawnie Utwórz użytkownika, przed jego użyciem. |
| 40524 |16 |Typ danych '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40525 |16 |Za pomocą "%.ls" nie jest obsługiwane w tej wersji programu SQL Server. |
| 40526 |16 |'%.&#x2a;ls' dostawcy zestawu wierszy ls nie jest obsługiwany w tej wersji programu SQL Server. |
| 40527 |16 |Połączone serwery są nieobsługiwane w tej wersji programu SQL Server. |
| 40528 |16 |Użytkowników nie można mapować na certyfikaty, klucze asymetryczne lub Windows logowania w tej wersji programu SQL Server. |
| 40529 |16 |Wbudowana funkcja '%.&#x2a;ls' w personifikacji kontekst nie jest obsługiwany w tej wersji programu SQL Server. |
| 40532 |11 |Nie można otworzyć serwera "%.&#x2a;ls" żądanego podczas logowania. Logowanie nie powiodło się. |
| 40553 |16 |Sesja została przerwana z powodu nadmiernego wykorzystania pamięci. Spróbuj zmodyfikować zapytanie, tak aby przetwarzało mniejszą liczbę wierszy.<br/><br/> Zmniejszenie liczby `ORDER BY` i `GROUP BY` operacji w kodzie języka Transact-SQL pozwala zmniejszyć wymagania dotyczące pamięci zapytania. |
| 40604 |16 |Można nie CREATE/ALTER DATABASE, ponieważ może to spowodować przekroczenie przydziału serwera. |
| 40606 |16 |Dołączanie bazy danych nie jest obsługiwane w tej wersji programu SQL Server. |
| 40607 |16 |Windows logowania nie są obsługiwane w tej wersji programu SQL Server. |
| 40611 |16 |Serwery może mieć co najwyżej 128 reguł zapory. |
| 40614 |16 |Początkowy adres IP reguły zapory nie może przekraczać końcowy adres IP. |
| 40615 |16 |Nie można otworzyć serwera "{0}" żądanego podczas logowania. Klient o adresie IP{1}"nie może uzyskać dostępu do serwera.<br /><br />Aby włączyć dostęp, użyj portalu bazy danych SQL, lub uruchom sp\_ustaw\_zapory\_reguły głównej bazy danych, aby utworzyć regułę zapory dla tego adresu IP lub zakresu adresów. Może upłynąć do pięciu minut, zanim ta zmiana zaczęła obowiązywać. |
| 40617 |16 |Nazwa reguły zapory, która rozpoczyna się nazwą (reguły) jest za długa. Maksymalna długość to 128. |
| 40618 |16 |Nazwa reguły zapory nie może być pusta. |
| 40620 |16 |Logowanie nie powiodło się dla użytkownika "%.&#x2a;ls". Nie można zmienić hasła. Zmiana hasła podczas logowania nie jest obsługiwana w tej wersji programu SQL Server. |
| 40627 |20 |Operacja na serwerze "{0}"i bazy danych"{1}" jest w toku. Zaczekaj kilka minut przed podjęciem ponownej próby. |
| 40630 |16 |Sprawdzenie poprawności hasła nie powiodło się. Hasło nie spełnia wymagań zasad, ponieważ jest zbyt krótkie. |
| 40631 |16 |Określone hasło jest za długa. Hasło powinna mieć nie więcej niż 128 znaków. |
| 40632 |16 |Sprawdzenie poprawności hasła nie powiodło się. Hasło nie spełnia wymagań zasad, ponieważ nie jest wystarczająco złożone. |
| 40636 |16 |Nie można użyć zastrzeżonej nazwy bazy danych '%.&#x2a;ls' w tej operacji. |
| 40638 |16 |Nieprawidłowy identyfikator subskrypcji (identyfikator subskrypcji). Subskrypcja nie istnieje. |
| 40639 |16 |Żądanie nie jest zgodny ze schematem: (błąd schematu). |
| 40640 |20 |Serwer napotkał nieoczekiwany wyjątek. |
| 40641 |16 |Określona lokalizacja jest nieprawidłowa. |
| 40642 |17 |Serwer jest obecnie zbyt zajęty. Spróbuj ponownie później. |
| 40643 |16 |Wartość określonego nagłówka x-ms-version jest nieprawidłowa. |
| 40644 |14 |Nie można autoryzować dostępu do określonej subskrypcji. |
| 40645 |16 |ServerName (servername) nie może być pusta ani mieć wartości null. Je mogą tylko składać się z małych liter ""-"z", cyfry od 0 do 9 oraz łącznik. Łącznik może nie ani na końcu nazwy. |
| 40646 |16 |Identyfikator subskrypcji nie może być pusta. |
| 40647 |16 |Subskrypcja (identyfikator subskrypcji) nie ma serwera (servername). |
| 40648 |17 |Wykonano zbyt wiele żądań. Spróbuj ponownie później. |
| 40649 |16 |Określono nieprawidłowy typ zawartości. Obsługiwane jest tylko aplikacja/xml. |
| 40650 |16 |Subskrypcja (identyfikator subskrypcji) nie istnieje lub nie jest gotowa do operacji. |
| 40651 |16 |Nie można utworzyć serwera, ponieważ subskrypcja (identyfikator subskrypcji) jest wyłączona. |
| 40652 |16 |Nie można przenieść ani utworzyć serwera. Subskrypcja (identyfikator subskrypcji) przekroczy przydział serwera. |
| 40671 |17 |Błąd komunikacji między bramą i usługą zarządzania. Spróbuj ponownie później. |
| 40852 |16 |Nie można otworzyć bazy danych "%. \*ls na serwerze "%. \*ls żądanego podczas logowania. Dostęp do bazy danych jest dozwolone tylko przy użyciu parametrów połączenia z włączoną obsługą zabezpieczeń. Aby uzyskać dostęp do tej bazy danych, należy zmodyfikować parametry połączenia zawierają bezpieczny, FQDN -.database.windows "name server" serwera .net należy zmodyfikować, aby .database "name server". `secure`. windows.net. |
| 40914 | 16 | Nie można otworzyć serwera " *[nazwa_serwera]* " żądanego podczas logowania. Klient nie może uzyskać dostęp do serwera.<br /><br />Aby rozwiązać problem, należy rozważyć dodanie [reguły sieci wirtualnej](sql-database-vnet-service-endpoint-rule-overview.md). |
| 45168 |16 |System SQL Azure jest obciążony i umieszcza górny limit współbieżnych operacji CRUD bazy danych dla jednego serwera bazy danych SQL (np. Tworzenie bazy danych). Serwer, który został określony w komunikacie o błędzie przekroczyła maksymalną liczbę równoczesnych połączeń. Spróbuj ponownie później. |
| 45169 |16 |System SQL azure jest obciążony i umieszcza górny limit liczby współbieżnych serwera operacji CRUD dla pojedynczej subskrypcji (np. do tworzenia serwera). Subskrypcja określona w komunikacie o błędzie przekroczyła maksymalną liczbę jednoczesnych połączeń, a żądanie zostało odrzucone. Spróbuj ponownie później. |

## <a name="next-steps"></a>Kolejne kroki

* Przeczytaj o [funkcji usługi Azure SQL Database](sql-database-features.md).
* Przeczytaj o [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md).
* Przeczytaj o [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).

