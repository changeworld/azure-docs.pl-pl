---
title: Przetwarzanie OLTP danych w pamięci zwiększa wydajności transakcji SQL | Dokumentacja firmy Microsoft
description: Przyjąć OLTP w pamięci, aby zwiększyć wydajność transakcji w istniejącej bazy danych SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: MightyPen
manager: craigg
ms.date: 11/07/2018
ms.openlocfilehash: ad66253d33b2e99f0be79bfaddc86b3274f5cab0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337793"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Użyj OLTP w pamięci, aby poprawić wydajność aplikacji w usłudze SQL Database

[Przetwarzanie OLTP danych w pamięci](sql-database-in-memory.md) pozwala zwiększyć wydajność przetwarzania transakcji, pozyskiwania danych i scenariuszy danych przejściowych w [w warstwie Premium i krytyczne dla działania firmy](sql-database-service-tiers-vcore.md) baz danych bez zwiększania warstwę cenową. 

> [!NOTE] 
> Dowiedz się, jak [kworum rozwiązanie quorum zwiększa dwukrotnie obciążenie klucza bazy danych podczas obniżania jednostek DTU o 70% z usługą SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Wykonaj następujące kroki, aby wdrażanie przetwarzania OLTP w pamięci w istniejącej bazy danych.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Krok 1: Upewnij się, że używasz bazę danych warstwy Premium i krytyczne dla działania firmy

Przetwarzanie OLTP danych w pamięci jest obsługiwana tylko w przypadku baz danych w warstwie Premium i krytyczne dla działania firmy. W pamięci jest obsługiwany, jeśli zwrócony wynik wyniesie 1 (nie 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* oznacza *Extreme przetwarzanie transakcji*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Krok 2: Zidentyfikować obiekty do migracji do OLTP w pamięci
SSMS obejmuje **przegląd analizy wydajności transakcji** raport, który możesz uruchomić względem bazy danych z aktywnego obciążenia. Raport identyfikuje tabel i procedur składowanych, które nadają się do migracji do OLTP w pamięci.

W programie SSMS podczas generowania raportu:

* W **Eksplorator obiektów**, kliknij prawym przyciskiem myszy węzeł bazy danych.
* Kliknij przycisk **raporty** > **raportów standardowych** > **przegląd analizy wydajności transakcji**.

Aby uzyskać więcej informacji, zobacz [Określanie, czy tabela przechowywane procedury powinno być przenoszone do OLTP w pamięci](https://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>Krok 3: Utwórz bazę danych porównywalne testu
Załóżmy, że raport wskazuje, że baza danych zawiera tabelę, która używającym konwertowane do tabeli zoptymalizowanej pod kątem pamięci. Firma Microsoft zaleca, należy najpierw przetestować potwierdzenia wskazanie przez testowanie.

Należy kopię produkcyjnej bazy danych. Bazy danych testu powinien być na tym samym poziomie warstwy usługi jako produkcyjnej bazy danych.

Aby ułatwić testowanie, dostosować test bazy danych w następujący sposób:

1. Łączenie z bazą danych testów przy użyciu programu SSMS.
2. Aby uniknąć konieczności opcji WITH (SNAPSHOT) w zapytaniach, należy ustawić opcji bazy danych, jak pokazano w następującej instrukcji języka T-SQL:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Krok 4: Migrowanie tabel
Należy utworzyć i wypełnić kopię zoptymalizowane pod kątem pamięci tabeli, którą chcesz przetestować. Można go utworzyć przy użyciu:

* Przydatny Kreator optymalizacji pamięci w programie SSMS.
* Ręczne języka T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Kreator optymalizacji pamięci w programie SSMS
Aby użyć tej opcji migracji:

1. Połącz z bazą danych testów za pomocą programu SSMS.
2. W **Eksplorator obiektów**, kliknij prawym przyciskiem myszy w tabeli, a następnie kliknij **Advisor optymalizacji pamięci**.
   
   * **Tabeli pamięci Optymalizator Advisor** zostanie wyświetlony Kreator.
3. W kreatorze kliknij pozycję **weryfikacji migracji** (lub **dalej** przycisk) aby zobaczyć, czy tabela zawiera nieobsługiwane funkcje, które nie są obsługiwane w tabelach zoptymalizowanych pod kątem pamięci. Aby uzyskać więcej informacji, zobacz:
   
   * *Lista kontrolna optymalizacji pamięci* w [Advisor optymalizacji pamięci](https://msdn.microsoft.com/library/dn284308.aspx).
   * [Konstrukcji języka Transact-SQL, które nie są obsługiwane przez OLTP w pamięci](https://msdn.microsoft.com/library/dn246937.aspx).
   * [Migrowanie do OLTP w pamięci](https://msdn.microsoft.com/library/dn247639.aspx).
4. Jeśli tabela nie ma żadnych nieobsługiwanych funkcji, doradcy przeprowadzać rzeczywiste schematu i migracji danych dla Ciebie.

#### <a name="manual-t-sql"></a>Ręczne języka T-SQL
Aby użyć tej opcji migracji:

1. Połączenia z bazą danych testów przy użyciu programu SSMS (lub podobnej użyteczności).
2. Uzyskaj kompletny skrypt języka T-SQL w tabeli i jej indeksów.
   
   * W programie SSMS kliknij prawym przyciskiem myszy węzeł tabeli.
   * Kliknij przycisk **skrypt tabeli jako** > **utworzyć** > **okno nowego zapytania**.
3. W oknie skryptu Dodaj WITH (MEMORY_OPTIMIZED = ON) do instrukcji CREATE TABLE.
4. W przypadku indeksu CLUSTERED, można go zmienić na NONCLUSTERED.
5. Za pomocą SP_RENAME, Zmień nazwę istniejącej tabeli.
6. Utwórz nowa kopia zoptymalizowane pod kątem pamięci tabeli poprzez uruchomienie skryptu CREATE TABLE edytowany.
7. Kopiuj dane do tabeli zoptymalizowanej pod kątem pamięci przy użyciu INSERT... WYBIERZ POZYCJĘ * DO:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Krok 5 (opcjonalnie): Migrowanie procedur składowanych
Funkcja w pamięci, można również zmodyfikować procedury składowanej w celu zwiększenia wydajności.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Zagadnienia dotyczące za pomocą procedur składowanych skompilowanych w sposób macierzysty
Procedury składowanej skompilowanej w sposób macierzysty musi mieć następujące opcje w jej klauzula języka T-SQL przy użyciu:

* OPCJĘ WITH NATIVE_COMPILATION
* SCHEMABINDING: co oznacza, tabel, które procedury składowanej nie może mieć ich definicje kolumn, zmienić w dowolny sposób, które będą wpływać na procedury składowanej, chyba że usuniesz procedury składowanej.

Moduł macierzysty muszą używać jednej big [bloków ATOMIC](https://msdn.microsoft.com/library/dn452281.aspx) zarządzania transakcji. Nie ma żadnej roli, rozpocząć transakcji jawnej lub ROLLBACK TRANSACTION. Jeśli kod wykryje naruszenie reguły biznesowej, może zostać przerwany atomic bloku [THROW](https://msdn.microsoft.com/library/ee677615.aspx) instrukcji.

### <a name="typical-create-procedure-for-natively-compiled"></a>Typowa procedura tworzenia dla skompilowanych w sposób macierzysty
Zazwyczaj języka T-SQL, aby utworzyć procedury składowanej skompilowanej w sposób macierzysty są podobne do następującego szablonu:

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

* Dla TRANSACTION_ISOLATION_LEVEL MIGAWKA jest najbardziej typowych wartości dla procedury składowanej skompilowanej w sposób macierzysty. Jednak podzbiór inne wartości są również obsługiwane:
  
  * ODCZYT POWTARZALNY
  * SERIALIZABLE
* Wartość języka musi być obecny w widoku sys.languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Jak przeprowadzić migrację procedury składowanej
Procedura migracji jest następująca:

1. Uzyskaj skryptu CREATE PROCEDURE regularne interpretowane procedury składowanej.
2. Należy zmodyfikować jego nagłówka do dopasowywania poprzedni szablon.
3. Należy upewnić się, czy procedura składowana kod T-SQL korzysta z żadnych funkcji, które nie są obsługiwane dla procedur składowanych skompilowanych w sposób macierzysty. Jeśli to konieczne, należy zaimplementować rozwiązania problemu.
   
   * Aby uzyskać szczegółowe informacje, zobacz [problemy z migracją natywnie kompilowane składowane](https://msdn.microsoft.com/library/dn296678.aspx).
4. Zmień nazwę starego procedury składowanej przy użyciu SP_RENAME. Lub po prostu usunąć ją.
5. Uruchom skrypt edytowanych Tworzenie procedury języka T-SQL.

## <a name="step-6-run-your-workload-in-test"></a>Krok 6: Uruchamianie obciążenia w teście
Uruchamianie obciążenia w bazie danych testowych podobne do obciążenia, które jest uruchamiane w produkcyjnej bazie danych. To powinno ujawnić, przyrost wydajności realizowane za korzystanie z funkcji w pamięci dla tabel i procedur składowanych.

Atrybuty głównych obciążenia to:

* Liczba równoczesnych połączeń.
* Współczynnik odczyt/zapis.

Aby dostosować i uruchomić test obciążenia, należy wziąć pod uwagę przy użyciu narzędzia przydatne ostress.exe przedstawionych [tutaj](sql-database-in-memory.md).

Aby zminimalizować opóźnienie sieci, należy uruchomić test w tym samym regionie platformy Azure geograficznym, gdy baza danych istnieje.

## <a name="step-7-post-implementation-monitoring"></a>Krok 7: Po wdrożeniu monitorowania
Należy rozważyć monitorowanie wydajności efekty swojej implementacji w pamięci w środowisku produkcyjnym:

* [Monitorowanie magazynu w pamięci](sql-database-in-memory-oltp-monitoring.md).
* [Monitorowanie usługi Azure SQL Database przy użyciu dynamicznych widoków zarządzania](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Powiązane linki
* [(Optymalizacja w pamięci) OLTP w pamięci](https://msdn.microsoft.com/library/dn133186.aspx)
* [Wprowadzenie do procedur składowanych skompilowanych w sposób macierzysty](https://msdn.microsoft.com/library/dn133184.aspx)
* [Klasyfikator optymalizacji pamięci](https://msdn.microsoft.com/library/dn284308.aspx)

