---
title: Kody błędów SQL — błąd połączenia z bazą danych | Dokumentacja firmy Microsoft
description: 'Dowiedz się więcej o kodach błędów SQL dla bazy danych SQL klienta aplikacji, takich jak typowych błędów połączenia bazy danych, problemy kopii bazy danych i ogólne błędy. '
keywords: Kod błędu SQL, sql dostępu, błędów połączenia bazy danych, kody błędów sql
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 09/28/2017
ms.author: sstein
ms.openlocfilehash: a4fa0d542d755e783f422a62e9135d06133385f8
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>Kody błędów SQL dla aplikacji klienckich, bazy danych SQL: błędy połączeń i inne problemy z bazy danych

Ten artykuł zawiera listę kodów błędów programu SQL dla aplikacji klienckich bazy danych SQL, w tym błędów połączenia bazy danych, błędów przejściowych (nazywanych również błędów przejściowych) błędy ładu zasobów, problemy kopii bazy danych, puli elastycznej i inne błędy. Większość kategorii dla bazy danych SQL Azure, a nie dotyczą programu Microsoft SQL Server. Zobacz też [komunikaty o błędach systemu](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Błędów połączenia bazy danych, błędów przejściowych i innych tymczasowe błędy
Poniższa tabela zawiera kody błędów SQL błędów utraty połączenia i innych błędów przejściowych, które mogą wystąpić, gdy aplikacja próbuje uzyskać dostępu do bazy danych SQL. Aby uzyskać Rozpoczynanie pracy — samouczki dotyczące nawiązywania połączenia z bazą danych SQL Azure, zobacz [nawiązywania połączenia z bazą danych SQL Azure](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Najbardziej typowych błędów połączenia bazy danych i wystąpienia błędu przejściowego błędów
Infrastruktury platformy Azure ma możliwość dynamicznie zmieniać swoją serwerów w przypadku złożonych zadań wystąpienia w usłudze baza danych SQL.  To zachowanie dynamicznej może spowodować program kliencki utratę połączenia z bazą danych SQL. Tego rodzaju błędu jest nazywany *błędu przejściowego*.

Zdecydowanie zaleca się, że program kliencki ma Logika ponawiania, dzięki czemu można przywrócić połączenia po nadaniu czasu wystąpienia błędu przejściowego rozwiązuje się.  Firma Microsoft zaleca opóźnienie 5 sekund przed ponowną próbą wykonania Twojego pierwszego. Ponawianie próby opóźnieniem krótszy niż 5 sekund ryzyka przeciążając uda się rozpoznać usługi w chmurze. Każda kolejne próby opóźnienie powinien być zwiększany wykładniczo maksymalnie 60 sekund.

Błąd przejściowy błędy manifestu zwykle jako jeden z następujących komunikatów o błędach z programów klienckich:

* Baza danych &lt;db_name&gt; na serwerze &lt;Azure_instance&gt; nie jest obecnie dostępna. Ponów próbę połączenia później. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną i podaj identyfikator śledzenia sesji &lt;session_id&gt;
* Baza danych &lt;db_name&gt; na serwerze &lt;Azure_instance&gt; nie jest obecnie dostępna. Ponów próbę połączenia później. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną i podaj identyfikator śledzenia sesji &lt;session_id&gt;. (Program Microsoft SQL Server, błąd: 40613)
* Istniejące połączenie zostało zamknięte przez hosta zdalnego.
* System.Data.Entity.Core.EntityCommandExecutionException: Wystąpił błąd podczas wykonywania definicji polecenia. Zobacz wyjątek wewnętrzny, aby uzyskać szczegółowe informacje. ---> System.Data.SqlClient.SqlException: Wystąpił błąd poziomu transportu podczas odbierania wyników z serwera. (Dostawca: Dostawca sesji, błąd: 19 — połączenie fizyczne nie jest używany)
* Próba połączenia pomocniczej bazy danych nie powiodła się, ponieważ baza danych jest w trakcie reconfguration i jest zajęty, zastosowanie nowych stron w trakcie wykonywania transakcja active w głównej bazie danych. 

Przykłady kodu logika ponowień zobacz:

* [Biblioteki połączeń dla bazy danych SQL i programu SQL Server](sql-database-libraries.md) 
* [Akcje, aby naprawić błędy połączenia i błędom przejściowym w bazie danych SQL](sql-database-connectivity-issues.md)

Omówienie *okresu blokowania* dla klientów używających ADO.NET jest dostępna w [programu SQL Server połączenia buforowanie (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Kody błędów błędu przejściowego
Następujące błędy są przejściowych i należy wykonać ponownie logiki aplikacji: 

| Kod błędu | Ważność | Opis |
| ---:| ---:|:--- |
| 4060 |16 |Nie można otworzyć bazy danych "%.&#x2a; ls" żądanego podczas logowania. Logowanie nie powiodło się. |
| 40197 |17 |Usługa napotkała błąd podczas przetwarzania żądania. Spróbuj ponownie. Kod błędu: %d.<br/><br/>Ten błąd jest wyświetlany, gdy usługa nie działa z powodu oprogramowania lub modernizacji sprzętu, awarii sprzętu lub inne problemy trybu failover. Kod błędu: (%d) osadzone w komunikacie błąd 40197 udostępnia dodatkowe informacje o rodzaju awarii lub trybu failover, który wystąpił. Przykładowe kody są osadzone w komunikacie błąd 40197 błędów to 40020, 40143 40166 i 40540.<br/><br/>Automatyczne ponowne łączenie z serwerem bazy danych SQL łączy dobrej kopii bazy danych. Aplikacja musi catch 40197, dziennik błędów osadzony kod błędu: (%d) w wiadomości do rozwiązywania problemów i ponowić próbę połączenia z bazą danych SQL, dopóki zasoby są dostępne, a połączenie zostanie nawiązane ponownie. |
| 40501 |20 |Usługa jest obecnie zajęta. Ponów żądanie po 10 sekundach. Identyfikator zdarzenia: %ls. Kod: %d.<br/><br/>Aby uzyskać więcej informacji, zobacz:<br/>• [Limity zasobów bazy danych SQL azure](sql-database-service-tiers.md). |
| 40613 |17 |Baza danych '%.&#x2a;ls' na serwerze '%.&#x2a;ls' nie jest obecnie dostępna. Ponów próbę połączenia później. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną i podaj identyfikator śledzenia sesji '%.&#x2a;ls'. |
| 49918 |16 |Nie można przetworzyć żądania. Za mało zasobów do przetworzenia żądania.<br/><br/>Usługa jest obecnie zajęta. Ponów żądanie później. |
| 49919 |16 |Nie można procesu Utwórz lub zaktualizuj zapytanie. Zbyt wiele operacji tworzenia lub aktualizacji w toku dla subskrypcji "% ld".<br/><br/>Usługa jest zajęta przetwarzania wielu utworzyć lub zaktualizować żądań dla subskrypcji lub serwera. Żądania są aktualnie zablokowane do optymalizacji zasobów. Zapytanie [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) dla oczekujących operacji. Zaczekaj do czasu tworzenia lub aktualizacji zakończeniu żądania lub usuń jedno z oczekujących żądań i ponów żądanie później. |
| 49920 |16 |Nie można przetworzyć żądania. Za dużo operacji w toku dla subskrypcji "% ld".<br/><br/>Usługa jest zajęty przetwarzaniem wiele żądań dla tej subskrypcji. Żądania są aktualnie zablokowane do optymalizacji zasobów. Zapytanie [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) stanu operacji. Poczekaj, aż oczekujących żądań zakończeniu lub usuń jedno z oczekujących żądań i ponów żądanie później. |
| 4221 |16 |Logowanie do odczytu pomocniczej nie powiodło się z powodu długim czasie oczekiwania na "HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING". Replika nie jest dostępne dla nazwy logowania, ponieważ brakuje dla transakcji, które były aktywny, gdy replika była odtwarzania wersji wiersza. Problem można rozwiązać przez wycofanie lub zatwierdzania aktywnych transakcji w replice podstawowej. Wystąpienia tego warunku można zminimalizować przez unikanie transakcjach zapisu długa na serwerze podstawowym. |

## <a name="database-copy-errors"></a>Błędy kopiowania bazy danych
Następujące błędy mogą wystąpić podczas kopiowania bazy danych w bazie danych SQL Azure. Więcej informacji znajdziesz w artykule [Kopiowanie bazy danych usługi Azure SQL Database](sql-database-copy.md).

| Kod błędu | Ważność | Opis |
| ---:| ---:|:--- |
| 40635 |16 |Klient o adresie IP '%.&#x2a;ls' jest tymczasowo wyłączona. |
| 40637 |16 |Utwórz kopię bazy danych jest obecnie wyłączona. |
| 40561 |16 |Kopiowanie bazy danych nie powiodło się. Źródłowa lub docelowa baza danych nie istnieje. |
| 40562 |16 |Kopiowanie bazy danych nie powiodło się. Źródłowa baza danych została porzucona. |
| 40563 |16 |Kopiowanie bazy danych nie powiodło się. Docelowa baza danych została porzucona. |
| 40564 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40565 |16 |Kopiowanie bazy danych nie powiodło się. Dozwolone jest nie więcej niż 1 kopia równoczesnych bazy danych z tego samego źródła. Porzuć docelową bazę danych i spróbuj ponownie później. |
| 40566 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40567 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40568 |16 |Kopiowanie bazy danych nie powiodło się. Źródłowa baza danych stała się niedostępna. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40569 |16 |Kopiowanie bazy danych nie powiodło się. Docelowa baza danych stała się niedostępna. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40570 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie później. |
| 40571 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie później. |

## <a name="resource-governance-errors"></a>Błędy ładu zasobów
Następujące błędy są spowodowane nadmiernego wykorzystania zasobów podczas pracy z bazą danych SQL Azure. Na przykład:

* Transakcja została Otwórz zbyt długo.
* Transakcja jest zbyt wiele blokują.
* Aplikacja zużywa zbyt dużej ilości pamięci.
* Aplikacja zużywa zbyt dużo `TempDb` miejsca.

Tematy pokrewne:

* Bardziej szczegółowe informacje znajdują się w tym miejscu: [limity zasobów bazy danych SQL Azure](sql-database-service-tiers.md).

| Kod błędu | Ważność | Opis |
| ---:| ---:|:--- |
| 10928 |20 |Identyfikator zasobu: %d. Limit %s dla bazy danych jest %d i został osiągnięty. Aby uzyskać więcej informacji, zobacz [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>Identyfikator zasobu wskazuje zasób, który został osiągnięty limit. Dla wątków roboczych, identyfikator zasobu = 1. Dla sesji, identyfikator zasobu = 2.<br/><br/>Aby uzyskać więcej informacji na temat tego błędu i sposobu rozwiązania go zobacz:<br/>• [Limity zasobów bazy danych SQL azure](sql-database-service-tiers.md). |
| 10929 |20 |Identyfikator zasobu: %d. Gwarancji minimalna %s wynosi %d, maksymalny limit wynosi %d, a bieżące użycie dla bazy danych wynosi %d. Jednak serwer jest obecnie zbyt zajęty, aby obsługiwać żądania przekracza %d dla tej bazy danych. Aby uzyskać więcej informacji, zobacz [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). W przeciwnym razie spróbuj ponownie później.<br/><br/>Identyfikator zasobu wskazuje zasób, który został osiągnięty limit. Dla wątków roboczych, identyfikator zasobu = 1. Dla sesji, identyfikator zasobu = 2.<br/><br/>Aby uzyskać więcej informacji na temat tego błędu i sposobu rozwiązania go zobacz:<br/>• [Limity zasobów bazy danych SQL azure](sql-database-service-tiers.md). |
| 40544 |20 |Baza danych osiągnęła swój limit przydziału. Partycji lub Usuń dane, Porzuć indeksy lub zapoznaj się z dokumentacją możliwych rozwiązań. |
| 40549 |16 |Sesja jest przerwana z powodu długotrwałej transakcji. Spróbuj skrócić transakcję. |
| 40550 |16 |Sesja została przerwana, ponieważ uzyskała zbyt wiele blokad. Spróbuj odczytu lub modyfikować mniej wierszy w ramach jednej transakcji. |
| 40551 |16 |Sesja została przerwana z powodu nadmiernego `TEMPDB` użycia. Spróbuj zmodyfikować zapytanie, aby ograniczyć wykorzystanie miejsca w tabelach tymczasowych.<br/><br/>Jeśli używasz obiekty tymczasowe, co pozwala zaoszczędzić miejsce w `TEMPDB` bazy danych, przeciągając obiekty tymczasowe po nie są już potrzebne w sesji. |
| 40552 |16 |Sesja została przerwana z powodu nadmiernego transakcji użycie miejsca na dziennik. Spróbuj modyfikować mniej wierszy w ramach jednej transakcji.<br/><br/>W przypadku zbiorczego wstawia przy użyciu `bcp.exe` narzędzie lub `System.Data.SqlClient.SqlBulkCopy` klasy, spróbuj użyć `-b batchsize` lub `BatchSize` opcji, aby ograniczyć liczbę wierszy jest kopiowana na serwer w każdej transakcji. Odbudowywania indeksu o `ALTER INDEX` instrukcji, spróbuj użyć `REBUILD WITH ONLINE = ON` opcji. |
| 40553 |16 |Sesja została przerwana z powodu nadmiernego wykorzystania pamięci. Spróbuj zmodyfikować zapytanie, tak aby przetwarzało mniejszą liczbę wierszy.<br/><br/>Zmniejszenie liczby `ORDER BY` i `GROUP BY` operacje w kodzie języka Transact-SQL zmniejsza wymagania dotyczące pamięci kwerendy. |

## <a name="elastic-pool-errors"></a>Błędy puli elastycznej
Do tworzenia i używania pule elastyczne odnoszą się następujące błędy:

| Numer_błędu | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
|:--- |:--- |:--- |:--- |:--- |:--- |
| 1132 |EX_RESOURCE |Osiągnięto limit magazynowania puli elastycznej. Użycie magazynu dla puli elastycznej nie może przekraczać MB (%d). |Limit miejsca na pulę elastyczną w MB. |Próba zapisu danych do bazy danych, gdy został osiągnięty limit magazynu elastycznej puli. |Należy rozważyć zwiększenie wartości Dtu i/lub Dodawanie magazynu do puli elastycznej, jeśli to możliwe, aby zwiększyć limit magazynu zmniejszenie ilości miejsca używanego przez pojedyncze bazy danych w puli elastycznej lub usunąć z puli elastycznej bazy danych. |
| 10929 |EX_USER |Gwarancji minimalna %s wynosi %d, maksymalny limit wynosi %d, a bieżące użycie dla bazy danych wynosi %d. Jednak serwer jest obecnie zbyt zajęty, aby obsługiwać żądania przekracza %d dla tej bazy danych. Zobacz [ http://go.microsoft.com/fwlink/?LinkId=267637 ](http://go.microsoft.com/fwlink/?LinkId=267637) Aby uzyskać pomoc. W przeciwnym razie spróbuj ponownie później. |DTU / vCore min. na bazy danych. DTU / vCore max na bazę danych |Całkowita liczba równoczesnych procesów roboczych (liczba żądań) dla wszystkich baz danych w puli elastycznej próba może przekraczać limitu puli. |Należy rozważyć zwiększenie Dtu lub vCores puli elastycznej, jeśli to możliwe, aby zwiększyć limit procesu roboczego lub usunąć z puli elastycznej bazy danych. |
| 40844 |EX_USER |Baza danych '%ls' na serwerze '%ls' jest '%ls' wersji bazy danych w puli elastycznej i nie może mieć relacji ciągła kopia. |Nazwa bazy danych, wersji bazy danych, nazwę serwera |Polecenie StartDatabaseCopy wydawane na inne niż premium bazę danych w puli elastycznej. |Wkrótce |
| 40857 |EX_USER |Nie znaleziono serwera puli elastycznej: '%ls', nazwa puli elastycznej: '%ls'. |Nazwa serwera. Nazwa puli elastycznej |Określona pula elastyczna nie istnieje na określonym serwerze. |Podaj nazwę puli elastycznej prawidłowe. |
| 40858 |EX_USER |Na serwerze istnieje już pula elastyczna '%ls': '%ls' |Nazwa puli elastycznej, nazwa serwera |Określonej puli elastycznej już istnieje w określonym serwera logicznego. |Podaj nową nazwę puli elastycznej. |
| 40859 |EX_USER |Pula elastyczna nie obsługuje warstwy usługi '%ls'. |Warstwa usług puli elastycznej |Warstwa określonej usługi nie jest obsługiwana dla puli elastycznej inicjowania obsługi administracyjnej. |Podaj poprawne edition lub pozostaw pole puste, aby użyć domyślnej warstwy usług warstwy usług. |
| 40860 |EX_USER |Kombinacja puli elastycznej usług i '%ls' cel '%ls' jest nieprawidłowa. |Nazwa puli elastycznej; Nazwa celu poziomu usługi |Elastyczne cel puli i usługi mogą być określone razem tylko wtedy, gdy cel usługi jest określony jako "ElasticPool". |Określ poprawny kombinacja puli elastycznej i cel usługi. |
| 40861 |EX_USER |Wersja bazy danych "%. *ls nie może być inna niż warstwa usług puli elastycznej, czyli "%.* ls. |Wersja bazy danych, warstwa usług puli elastycznej |Wersja bazy danych jest inna niż warstwa usług puli elastycznej. |Nie można określić wersji bazy danych, która jest inna niż warstwa usług puli elastycznej.  Należy pamiętać, że wersja bazy danych nie muszą być określone. |
| 40862 |EX_USER |Nazwa puli elastycznej musi być określona, jeśli określono cel usług puli elastycznej. |Brak |Cel usług puli elastycznej nie identyfikuje jednoznacznie puli elastycznej. |Jeśli przy użyciu cel usług puli elastycznej, należy określić nazwę puli elastycznej. |
| 40864 |EX_USER |Liczba jednostek Dtu dla puli elastycznej musi wynosić co najmniej (%d) Dtu dla warstwy usług "%. * ls. |Liczba jednostek Dtu dla puli elastycznej; Warstwa usług puli elastycznej. |Trwa próba skonfigurowania jednostek Dtu dla puli elastycznej poniżej minimalnego limitu. |Ustawienie jednostek Dtu dla elastyczna pula co najmniej minimalny limit ponownych prób. |
| 40865 |EX_USER |Liczba jednostek Dtu dla puli elastycznej nie może przekroczyć (%d) Dtu dla warstwy usług "%. * ls. |Liczba jednostek Dtu dla puli elastycznej; Warstwa usług puli elastycznej. |Trwa próba skonfigurowania jednostek Dtu dla puli elastycznej przekracza maksymalny limit. |Ustawienie jednostek Dtu dla puli elastycznej nie może przekraczać maksymalny limit ponownych prób. |
| 40867 |EX_USER |Maksymalna wartość DTU na bazę danych musi wynosić co najmniej (%d) dla warstwy usług "%. * ls. |Maksymalnej wartości DTU na bazę danych; Warstwa usług puli elastycznej |Trwa próba skonfigurowania maksymalnej wartości DTU na bazę danych poniżej obsługiwany limit. | onsider przy użyciu warstwa usług puli elastycznej, który obsługuje odpowiednie ustawienie. |
| 40868 |EX_USER |Maksymalna wartość DTU na bazę danych nie może przekroczyć (%d) dla warstwy usług "%. * ls. |Maksymalnej wartości DTU na bazę danych; Warstwa usług puli elastycznej. |Trwa próba skonfigurowania maksymalnej wartości DTU na bazę danych, przekracza obsługiwany limit. | Należy rozważyć użycie warstwa usług puli elastycznej, który obsługuje odpowiednie ustawienie. |
| 40870 |EX_USER |Minimalna wartość DTU na bazę danych nie może przekroczyć (%d) dla warstwy usług "%. * ls. |Minimalna wartość DTU na bazę danych; Warstwa usług puli elastycznej. |Trwa próba skonfigurowania minimalna wartość DTU na bazę danych, przekracza obsługiwany limit. | Należy rozważyć użycie warstwa usług puli elastycznej, który obsługuje odpowiednie ustawienie. |
| 40873 |EX_USER |Liczba baz danych (%d) i minimalna wartość DTU na bazę danych (%d) nie może przekroczyć wartości Dtu puli elastycznej (%d). |Liczba baz danych w puli elastycznej; Minimalna wartość DTU na bazę danych; Liczba jednostek Dtu puli elastycznej. |Próba określenia minimalna wartość DTU dla baz danych w puli elastycznej, który przekracza Dtu puli elastycznej. | Należy rozważyć zwiększenie wartości Dtu puli elastycznej, Zmniejsz minimalna wartość DTU na bazę danych lub Zmniejsz liczbę baz danych w puli elastycznej. |
| 40877 |EX_USER |Nie można usunąć puli elastycznej, chyba że nie zawiera żadnych baz danych. |Brak |Pula elastyczna zawiera jeden lub więcej baz danych i nie można usunąć. |Usunąć z puli elastycznej bazy danych, aby można było go usunąć. |
| 40881 |EX_USER |Pula elastyczna "%. * ls osiągnęła swój limit liczby bazy danych.  Limit liczby bazy danych dla puli elastycznej nie może przekroczyć (%d) dla puli elastycznej (% d) Dtu. |Nazwa puli elastycznej; limit liczby bazy danych w puli elastycznej; Liczba jednostek Edtu puli zasobów. |Próba utworzyć lub dodać do puli elastycznej bazy danych, po osiągnięciu limitu liczby bazy danych w puli elastycznej. | Należy rozważyć zwiększenie wartości Dtu puli elastycznej, jeśli to możliwe, aby zwiększyć limit bazy danych lub usunąć z puli elastycznej bazy danych. |
| 40889 |EX_USER |Dtu lub limitu magazynowania dla puli elastycznej "%. * ls nie można zmniejszyć, ponieważ nie zapewniłoby z wystarczającą ilością wolnego miejsca dla bazy danych. |Nazwa puli elastycznej. |Próba zmniejszyć limit magazynowania puli elastycznej poniżej jego użycia magazynu. | Rozważ zmniejszenie użycia magazynu pojedynczych baz danych w puli elastycznej lub usunąć bazy danych z puli, aby zmniejszyć jej Dtu lub limitu magazynowania. |
| 40891 |EX_USER |Minimalna wartość DTU na bazę danych (%d) nie może przekraczać maksymalnej wartości DTU na bazę danych (%d). |Minimalna wartość DTU na bazę danych; Maksymalna wartość DTU na bazę danych. |Trwa próba skonfigurowania minimalna wartość DTU na bazę danych jest większa niż maksymalna liczba jednostek DTU na bazę danych. |Upewnij się, że minimalna wartość DTU na baz danych nie przekracza maksymalnej wartości DTU na bazę danych. |
| TBD |EX_USER |Rozmiar magazynu dla poszczególnych bazy danych w puli elastycznej nie może przekraczać maksymalny rozmiar dozwolony przez "%. * puli elastycznej warstwy usługi ls. |Warstwa usług puli elastycznej |Maksymalny rozmiar bazy danych przekracza maksymalny rozmiar dozwolony przez warstwa usług puli elastycznej. |Ustaw maksymalny rozmiar bazy danych w ramach limitów maksymalny rozmiar dozwolony przez warstwa usług puli elastycznej. |

Tematy pokrewne:

* [Tworzenie puli elastycznej (C#)](sql-database-elastic-pool-manage-csharp.md) 
* [Zarządzanie puli elastycznej (C#)](sql-database-elastic-pool-manage-csharp.md). 
* [Tworzenie puli elastycznej (PowerShell)](sql-database-elastic-pool-manage-powershell.md) 
* [Monitorowanie i zarządzanie nimi puli elastycznej (PowerShell)](sql-database-elastic-pool-manage-powershell.md).

## <a name="general-errors"></a>Ogólne błędy
Następujące błędy nie należą do żadnych poprzednich kategorii.

| Kod błędu | Ważność | Opis |
| ---:| ---:|:--- |
| 15006 |16 |(AdministratorLogin) nie jest prawidłową nazwą, ponieważ zawiera ona nieprawidłowe znaki. |
| 18452 |14 |Logowanie nie powiodło się. Nazwa logowania pochodzi z niezaufanej domeny i nie można używać z systemu Windows authentication.%.&#x2a;ls (nazwy logowania systemu Windows nie są obsługiwane w tej wersji programu SQL Server). |
| 18456 |14 |Logowanie nie powiodło się dla użytkownika '%.&#x2a;ls'.%.&#x2a;ls%.&#x2a;ls(Błąd logowania dla użytkownika "%.&#x2a;ls". Nie można zmienić hasła. Zmiany hasła podczas logowania nie jest obsługiwana w tej wersji programu SQL Server.) |
| 18470 |14 |Logowanie nie powiodło się dla użytkownika '%.&#x2a;ls'. Przyczyna: Konto jest disabled.%.&#x2a;ls |
| 40014 |16 |Nie można używać wielu baz danych w tej samej transakcji. |
| 40054 |16 |Tabele bez indeksu klastrowanego są nieobsługiwane w tej wersji programu SQL Server. Utwórz indeks klastrowany i spróbuj ponownie. |
| 40133 |15 |Ta operacja jest nieobsługiwana w tej wersji programu SQL Server. |
| 40506 |16 |Określony identyfikator SID jest nieprawidłowy dla tej wersji programu SQL Server. |
| 40507 |16 |'%.&#x2a;ls' ls nie można wywołać z parametrami w tej wersji programu SQL Server. |
| 40508 |16 |UŻYJ instrukcja nie jest obsługiwana w celu przełączania się między bazami danych. Użyj nowego połączenia, aby nawiązać połączenie z inną bazą danych. |
| 40510 |16 |Instrukcja '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server |
| 40511 |16 |Wbudowana funkcja '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40512 |16 |Przestarzałe funkcja '%ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40513 |16 |Serwer zmiennej '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40514 |16 |'%ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40515 |16 |Odwołanie do nazwy bazy danych i/lub serwera w '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40516 |16 |Globalne obiekty tymczasowe są nieobsługiwane w tej wersji programu SQL Server. |
| 40517 |16 |Słowo kluczowe lub opcja instrukcji '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40518 |16 |Polecenie DBCC '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40520 |16 |Zabezpieczana klasa "% S_MSG" jest nieobsługiwane w tej wersji programu SQL Server. |
| 40521 |16 |Zabezpieczana klasa "% S_MSG" jest nieobsługiwane w zakresie serwera w tej wersji programu SQL Server. |
| 40522 |16 |Typ podmiotu zabezpieczeń '%.&#x2a;ls' w bazie danych nie jest obsługiwane w tej wersji programu SQL Server. |
| 40523 |16 |Tworzenie '%.&#x2a;ls' niejawnego użytkownika nie jest obsługiwane w tej wersji programu SQL Server. Jawnie Utwórz użytkownika przed jego użyciem. |
| 40524 |16 |Typ danych '%.&#x2a;ls' nie jest obsługiwana w tej wersji programu SQL Server. |
| 40525 |16 |Z "%.ls" nie jest obsługiwane w tej wersji programu SQL Server. |
| 40526 |16 |'%.&#x2a;ls' dostawcy zestawu wierszy ls nie jest obsługiwany w tej wersji programu SQL Server. |
| 40527 |16 |Połączonych serwerów nie są obsługiwane w tej wersji programu SQL Server. |
| 40528 |16 |Użytkowników nie można mapować na certyfikaty, klucze asymetryczne lub nazwy logowania systemu Windows w tej wersji programu SQL Server. |
| 40529 |16 |Wbudowana funkcja '%.&#x2a;ls' w personifikacji kontekst nie jest obsługiwany w tej wersji programu SQL Server. |
| 40532 |11 |Nie można otworzyć serwera "%.&#x2a;ls" żądanego podczas logowania. Logowanie nie powiodło się. |
| 40553 |16 |Sesja została przerwana z powodu nadmiernego wykorzystania pamięci. Spróbuj zmodyfikować zapytanie, tak aby przetwarzało mniejszą liczbę wierszy.<br/><br/> Zmniejszenie liczby `ORDER BY` i `GROUP BY` operacje w kodzie języka Transact-SQL pozwala zmniejszyć wymagania dotyczące pamięci kwerendy. |
| 40604 |16 |Można nie CREATE/ALTER DATABASE, ponieważ może to spowodować przekroczenie przydziału serwera. |
| 40606 |16 |Dołączanie bazy danych nie jest obsługiwane w tej wersji programu SQL Server. |
| 40607 |16 |Nazwy logowania systemu Windows nie są obsługiwane w tej wersji programu SQL Server. |
| 40611 |16 |Serwery mogą mieć co najwyżej 128 reguł zapory. |
| 40614 |16 |Początkowy adres IP reguły zapory nie może przekraczać końcowy adres IP. |
| 40615 |16 |Nie można otworzyć serwera "{0}" żądanego podczas logowania. Klient o adresie IP "{1}" nie jest dozwolony dostęp do serwera.<br /><br />Aby włączyć dostęp, użyj portalu bazy danych SQL lub uruchom sp\_ustawić\_zapory\_reguła wzorca bazy danych, aby utworzyć regułę zapory dla tego adresu IP lub zakresu adresów. Może upłynąć do pięciu minut, aby ta zmiana została uwzględniona. |
| 40617 |16 |Nazwa reguły zapory, która rozpoczyna się nazwą (zasada) jest zbyt długa. Maksymalna długość to 128. |
| 40618 |16 |Nazwa reguły zapory nie może być pusta. |
| 40620 |16 |Logowanie nie powiodło się dla użytkownika "%.&#x2a;ls". Nie można zmienić hasła. Zmiana hasła podczas logowania nie jest obsługiwana w tej wersji programu SQL Server. |
| 40627 |20 |Operacja na serwerze "{0}" i bazy danych "{1}" jest w toku. Poczekaj kilka minut przed podjęciem ponownej próby. |
| 40630 |16 |Sprawdzenie poprawności hasła nie powiodło się. Hasło nie spełnia wymagań zasad, ponieważ jest za krótkie. |
| 40631 |16 |Należy określić hasło jest zbyt długa. Hasło powinna mieć nie więcej niż 128 znaków. |
| 40632 |16 |Sprawdzenie poprawności hasła nie powiodło się. Hasło nie spełnia wymagań zasad, ponieważ nie jest wystarczająco złożone. |
| 40636 |16 |Nie można użyć zastrzeżonej nazwy bazy danych '%.&#x2a;ls' w tej operacji. |
| 40638 |16 |Nieprawidłowy identyfikator subskrypcji (identyfikator subskrypcji). Subskrypcja nie istnieje. |
| 40639 |16 |Żądanie nie jest zgodny ze schematem: (błąd schematu). |
| 40640 |20 |Serwer napotkał nieoczekiwany wyjątek. |
| 40641 |16 |Określona lokalizacja jest nieprawidłowa. |
| 40642 |17 |Serwer jest obecnie zbyt zajęty. Spróbuj ponownie później. |
| 40643 |16 |Wartość określony nagłówek x-ms-version jest nieprawidłowa. |
| 40644 |14 |Nie można autoryzować dostępu do określonej subskrypcji. |
| 40645 |16 |ServerName (servername) nie może być pusta ani mieć wartości null. Go może zawierać tylko małe litery ''-'z', cyfry 0-9 i myślnika. Łącznik nie może prowadzić ani końcu nazwy. |
| 40646 |16 |Identyfikator subskrypcji nie może być pusta. |
| 40647 |16 |Subskrypcja (identyfikator subskrypcji) nie ma serwera (servername). |
| 40648 |17 |Wykonano zbyt wiele żądań. Spróbuj ponownie później. |
| 40649 |16 |Określono nieprawidłowy typ zawartości. Obsługiwane jest tylko aplikacja/xml. |
| 40650 |16 |Subskrypcja (identyfikator subskrypcji) nie istnieje lub nie jest gotowa dla tej operacji. |
| 40651 |16 |Nie można utworzyć serwera, ponieważ subskrypcja (identyfikator subskrypcji) jest wyłączona. |
| 40652 |16 |Nie można przenieść ani utworzyć serwera. Subskrypcja (identyfikator subskrypcji) przekroczy limit przydziału serwera. |
| 40671 |17 |Błąd komunikacji między bramą a usługi zarządzania. Spróbuj ponownie później. |
| 40852 |16 |Nie można otworzyć bazy danych "%. \*ls na serwerze "%. \*ls żądanego podczas logowania. Dostęp do bazy danych jest dozwolone tylko przy użyciu parametrów połączenia z włączoną obsługą zabezpieczeń. Aby uzyskać dostęp do tej bazy danych, zmodyfikuj parametry połączenia zawierają bezpieczne, na serwerze FQDN -.database.windows "Nazwa serwera".net powinien zostać zmodyfikowane w celu .database "name server". `secure`. windows.net. |
| 40914 | 16 | Nie można otworzyć serwera "*[nazwa serwera]*" żądanego podczas logowania. Klient nie może uzyskać dostęp do serwera.<br /><br />Aby rozwiązać problem, należy rozważyć dodanie [reguły sieci wirtualnej](sql-database-vnet-service-endpoint-rule-overview.md). |
| 45168 |16 |System SQL Azure jest obciążony i umieszcza górny limit współbieżnych operacji CRUD bazy danych dla pojedynczego serwera (np. Tworzenie bazy danych). Serwer określony w komunikacie o błędzie przekroczyła maksymalną liczbę równoczesnych połączeń. Spróbuj ponownie później. |
| 45169 |16 |System SQL azure jest obciążony i umieszcza górny limit liczby operacji CRUD równoczesnych serwera dla pojedynczego subskrypcji (np. Utwórz serwera). Subskrypcja określona w komunikacie o błędzie przekroczyła maksymalną liczbę równoczesnych połączeń, a żądanie zostało odrzucone. Spróbuj ponownie później. |

## <a name="next-steps"></a>Kolejne kroki
* Przeczytaj informacje o [funkcje bazy danych Azure SQL](sql-database-features.md).
* Przeczytaj informacje o [warstw usług](sql-database-service-tiers.md).

