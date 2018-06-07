---
title: OLTP w pamięci poprawia wydajności transakcji SQL | Dokumentacja firmy Microsoft
description: Przyjąć OLTP w pamięci, aby zwiększyć wydajność transakcyjnych w istniejącej bazy danych SQL.
services: sql-database
author: jodebrui
manager: craigg
ms.reviewer: MightyPen
ms.service: sql-database
ms.custom: develop databases
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: jodebrui
ms.openlocfilehash: a2f0d901abfa0013a6f53bacd72a9f8db2e0fd99
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648051"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Użyj OLTP w pamięci aby poprawić wydajność aplikacji w bazie danych SQL
[OLTP w pamięci](sql-database-in-memory.md) można użyć w celu poprawy wydajności przetwarzania transakcji, wprowadzanie danych i scenariusze przejściowej danych [warstwy Premium i biznesowe krytyczne](sql-database-service-tiers-vcore.md) baz danych bez zwiększania warstwę cenową. 

> [!NOTE] 
> Dowiedz się, jak [kworum podwaja obciążenia klucza bazy danych podczas opuszczania jednostek dtu w warstwie 70% z bazy danych SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Wykonaj poniższe kroki przyjąć OLTP w pamięci w istniejącej bazy danych.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Krok 1: Upewnij się, że w przypadku korzystania z bazy danych warstwy Premium i biznesowe krytyczne
OLTP w pamięci jest obsługiwana tylko w bazach danych warstwy Premium i biznesowe krytyczne. W pamięci jest obsługiwany, jeśli zwrócony wynik jest 1 (nie 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* oznacza *Extreme przetwarzania transakcji*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Krok 2: Zidentyfikować obiekty do migracji do OLTP w pamięci
Obejmuje narzędzia SSMS **omówienie analizy wydajności transakcji** raport, który można uruchomić na bazie danych z aktywnego obciążenia. Raport identyfikuje tabele i procedury składowane, przeznaczone do migracji do OLTP w pamięci.

W programie SSMS do wygenerowania raportu:

* W **Eksplorator obiektów**, kliknij prawym przyciskiem myszy węzeł bazy danych.
* Kliknij przycisk **raporty** > **raportów standardowych** > **omówienie analizy wydajności transakcji**.

Aby uzyskać więcej informacji, zobacz [Określanie tabela lub przechowywane procedury powinny być przenoszone OLTP w pamięci](http://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>Krok 3: Tworzenie testu można porównywać pod względem bazy danych
Załóżmy, że raport wskazuje, że baza danych zawiera tabelę, która będzie korzystać z konwertowanej do tabeli zoptymalizowanej pod kątem pamięci. Firma Microsoft zaleca, należy najpierw przetestować potwierdzenia wskazanie przez testowania.

Należy kopię bazy danych produkcyjnych. Bazy danych testu powinien być na tym samym poziomie warstwy usługi, co w produkcyjnej bazie danych.

Aby ułatwić testowanie, dostosować bazy danych testu w następujący sposób:

1. Połączenia z bazą danych testów przy użyciu narzędzia SSMS.
2. Aby uniknąć konieczności opcji WITH (SNAPSHOT) w zapytaniach, ustawić opcji bazy danych, jak pokazano w poniższych instrukcji T-SQL:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Krok 4: Migrowanie tabel
Należy utworzyć i wypełnić kopii zoptymalizowanych pod kątem pamięci tabeli, która ma zostać przetestowana. Można go utworzyć za pomocą:

* Przydatną Kreator optymalizacji pamięci w programie SSMS.
* Ręczne T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Kreator optymalizacji pamięci w programie SSMS
Aby użyć tej opcji migracji:

1. Połączenia z bazą danych testów z SSMS.
2. W **Eksplorator obiektów**, kliknij prawym przyciskiem myszy w tabeli, a następnie kliknij przycisk **Advisor optymalizacji pamięci**.
   
   * **Advisor optymalizacji pamięci tabeli** zostanie wyświetlony Kreator.
3. W kreatorze kliknij **weryfikacji migracji** (lub **dalej** przycisk) aby zobaczyć, czy tabela zawiera nieobsługiwane funkcje, które nie są obsługiwane w tabelach zoptymalizowanych pod kątem pamięci. Aby uzyskać więcej informacji, zobacz:
   
   * *Lista kontrolna optymalizacji pamięci* w [Advisor optymalizacji pamięci](http://msdn.microsoft.com/library/dn284308.aspx).
   * [Konstrukcje języka Transact-SQL nie są obsługiwane przez OLTP w pamięci](http://msdn.microsoft.com/library/dn246937.aspx).
   * [Migrowanie do OLTP w pamięci](http://msdn.microsoft.com/library/dn247639.aspx).
4. Jeśli tabela nie zawiera nieobsługiwany funkcji, Doradcę można wykonywać rzeczywiste schematu i migrację danych za użytkownika.

#### <a name="manual-t-sql"></a>Ręczne T-SQL
Aby użyć tej opcji migracji:

1. Połączenia z bazą danych testów przy użyciu narzędzia SSMS (lub podobny narzędzia).
2. Uzyskaj pełną skryptu T-SQL dla tabeli i jego indeksy.
   
   * W programie SSMS kliknij prawym przyciskiem myszy węzeł tabeli.
   * Kliknij przycisk **skryptu tabeli jako** > **utworzyć** > **okna nowej kwerendy**.
3. W oknie skryptu, Dodaj WITH (MEMORY_OPTIMIZED = ON) w instrukcji CREATE TABLE.
4. Jeśli istnieje indeks KLASTROWANY, zmień go na NONCLUSTERED.
5. Zmień nazwę istniejącej tabeli za pomocą obiekt SP_RENAME.
6. Utwórz nową kopię zoptymalizowanych pod kątem pamięci tabeli, uruchamiając skrypt CREATE TABLE edytowany.
7. Skopiuj dane do tabeli zoptymalizowanej pod kątem pamięci przy użyciu INSERT... WYBIERZ * DO:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Krok 5 (opcjonalny): Migrowanie procedury składowane
Funkcja w pamięci można również zmodyfikować procedury składowanej zwiększonej wydajności.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Zagadnienia w procedurach składowanych skompilowanych w sposób macierzysty
Procedurę składowaną skompilowanych w sposób macierzysty musi mieć na jej klauzula T-SQL z następujących opcji:

* OPCJĘ WITH NATIVE_COMPILATION
* SCHEMABINDING: tabele, co oznacza, że procedura składowana nie może mieć ich definicje kolumn zmienione w dowolny sposób, które będą wpływać na procedurę składowaną, chyba że porzucić procedury składowanej.

Moduł macierzysty muszą używać jednej big [bloków ATOMIC](http://msdn.microsoft.com/library/dn452281.aspx) zarządzania transakcji. Brak role jawne BEGIN TRANSACTION lub ROLLBACK TRANSACTION. Jeśli kod wykryje naruszenie reguł biznesowych, może obsłużyć atomic bloku [THROW](http://msdn.microsoft.com/library/ee677615.aspx) instrukcji.

### <a name="typical-create-procedure-for-natively-compiled"></a>Typowy CREATE PROCEDURE dla skompilowanych w sposób macierzysty
Zwykle T-SQL, aby utworzyć skompilowanych w sposób macierzysty procedury składowanej jest podobny do następującego szablonu:

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* Dla TRANSACTION_ISOLATION_LEVEL MIGAWKA jest najbardziej typowe wartości dla procedury składowanej skompilowanych w sposób macierzysty. Jednak podzbiór inne wartości są również obsługiwane:
  
  * REPEATABLE READ
  * SERIALIZABLE
* Wartość języka musi być obecny w widoku sys.languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Jak przeprowadzić migrację procedury składowanej
Kroki migracji są:

1. Uzyskaj skryptu CREATE PROCEDURE regularne interpretowany procedury składowanej.
2. Należy zmodyfikować jej nagłówka do dopasowywania poprzedni szablon.
3. Należy upewnić się, czy procedura składowana kodu T-SQL korzysta z żadnych funkcji, które nie są obsługiwane dla procedur składowanych skompilowanych w sposób macierzysty. Implementowanie rozwiązania, w razie potrzeby.
   
   * Aby uzyskać więcej informacji, zobacz [problemy przy migracji dla natywnie skompilowany na procedur składowanych](http://msdn.microsoft.com/library/dn296678.aspx).
4. Zmień nazwę starego procedury składowanej przy użyciu obiekt SP_RENAME. Lub po prostu UPUSZCZANIA.
5. Uruchom skrypt edytowany Tworzenie procedury T-SQL.

## <a name="step-6-run-your-workload-in-test"></a>Krok 6: Uruchamianie obciążenia w teście
Uruchom obciążenia testu bazy danych podobny do obciążeniem, które jest uruchamiany w sieci produkcyjnej bazy danych. To powinno ujawnić, bardziej wydajne realizowane za korzystanie z funkcji w pamięci dla tabel i procedur składowanych.

Atrybuty głównych obciążenia są:

* Liczba równoczesnych połączeń.
* Stosunek odczytu/zapisu.

Aby dostosować i uruchomić test obciążenia, należy rozważyć użycie narzędzia przydatną ostress.exe, które przedstawiono w [tutaj](sql-database-in-memory.md).

Aby zminimalizować opóźnienie sieci, uruchom test, w tym samym regionie geograficznym Azure którym baza danych istnieje.

## <a name="step-7-post-implementation-monitoring"></a>Krok 7: Monitorowanie po wdrożeniu
Należy rozważyć monitorowanie wydajności skutków implementacjach użytkownika w pamięci w środowisku produkcyjnym:

* [Monitorowanie magazynu w pamięci](sql-database-in-memory-oltp-monitoring.md).
* [Monitorowanie usługi Azure SQL Database przy użyciu dynamicznych widoków zarządzania](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Powiązane linki
* [(Optymalizacja w pamięci) OLTP w pamięci](http://msdn.microsoft.com/library/dn133186.aspx)
* [Wprowadzenie do procedur składowanych skompilowanych w sposób macierzysty](http://msdn.microsoft.com/library/dn133184.aspx)
* [Klasyfikator optymalizacji pamięci](http://msdn.microsoft.com/library/dn284308.aspx)

