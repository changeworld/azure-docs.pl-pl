---
title: Oltp w pamięci poprawia SQL txn perf
description: Zastosuj oltp w pamięci, aby poprawić wydajność transakcyjną w istniejącej bazie danych SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: 653ed75341d5d56ecbe06cb59f0efafa1e68aa0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067266"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Użyj oltp w pamięci, aby zwiększyć wydajność aplikacji w bazie danych SQL

[Oltp w pamięci](sql-database-in-memory.md) może służyć do poprawy wydajności przetwarzania transakcji, pozyskiwania danych i przejściowych scenariuszy danych w bazach danych [warstwy Premium i Business Critical](sql-database-service-tiers-vcore.md) bez zwiększania warstwy cenowej. 

> [!NOTE] 
> Dowiedz się, jak [kworum podwaja obciążenie bazy danych kluczy przy jednoczesnym obniżeniu liczby DTU o 70% dzięki bazie danych SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Wykonaj następujące kroki, aby przyjąć oltp w pamięci w istniejącej bazie danych.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Krok 1: Upewnij się, że korzystasz z bazy danych warstwy Premium i Business Critical

Oltp w pamięci jest obsługiwany tylko w bazach danych warstwy Premium i Business Critical. In-Memory jest obsługiwany, jeśli zwracany wynik wynosi 1 (nie 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* to skrót od *Extreme Transaction Processing*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Krok 2: Identyfikowanie obiektów do migracji do protokołu OLTP w pamięci
SSMS zawiera raport **Przegląd analizy wydajności transakcji,** który można uruchomić w bazie danych z aktywnym obciążeniem. Raport identyfikuje tabele i procedury przechowywane, które są kandydatami do migracji do oltp w pamięci.

W SSMS, aby wygenerować raport:

* W **Eksploratorze obiektów**kliknij prawym przyciskiem myszy węzeł bazy danych.
* Kliknij pozycję **Raporty** > **standardowe raporty** > **Analiza wydajności transakcji .**

Aby uzyskać więcej informacji, zobacz [Określanie, czy tabela lub procedura składowana powinny być przenoszone do](https://msdn.microsoft.com/library/dn205133.aspx)protokołu OLTP w pamięci .

## <a name="step-3-create-a-comparable-test-database"></a>Krok 3: Tworzenie porównywalnej bazy danych testów
Załóżmy, że raport wskazuje, że baza danych ma tabelę, która korzysta z konwersji na tabelę zoptymalizowaną pod kątem pamięci. Zaleca się, aby najpierw przetestować, aby potwierdzić wskazanie przez testowanie.

Potrzebna jest testowa kopia produkcyjnej bazy danych. Testowa baza danych powinna znajdować się na tym samym poziomie warstwy usług co produkcyjna baza danych.

Aby ułatwić testowanie, dostosuj testującą bazę danych w następujący sposób:

1. Połącz się z testo-bazą danych przy użyciu usługi SSMS.
2. Aby uniknąć konieczności z (MIGAWKA) opcja w kwerendach, należy ustawić opcję bazy danych, jak pokazano w następującej instrukcji T-SQL:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Krok 4: Migrowanie tabel
Należy utworzyć i wypełnić zoptymalizowaną pod kątem pamięci kopię tabeli, którą chcesz przetestować. Można go utworzyć za pomocą:

* Przydatny Kreator optymalizacji pamięci w SSMS.
* Instrukcja T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Kreator optymalizacji pamięci w ssms
Aby użyć tej opcji migracji:

1. Połącz się z testowiązki z SSMS.
2. W **Eksploratorze obiektów**kliknij prawym przyciskiem myszy tabelę, a następnie kliknij polecenie **Klasyfikator optymalizacji pamięci**.
   
   * Zostanie wyświetlony kreator **Doradcy optymalizatora pamięci** tabel.
3. W kreatorze kliknij pozycję **Sprawdzanie poprawności migracji** (lub przycisk **Dalej),** aby sprawdzić, czy tabela zawiera nieobsługiwały funkcje, które nie są obsługiwane w tabelach zoptymalizowanych pod kątem pamięci. Aby uzyskać więcej informacji, zobacz:
   
   * *Lista kontrolna optymalizacji pamięci* w [doradcy optymalizacji pamięci](https://msdn.microsoft.com/library/dn284308.aspx).
   * [Konstrukcje Transact-SQL nie są obsługiwane przez protokół OLTP w pamięci](https://msdn.microsoft.com/library/dn246937.aspx).
   * [Migracja do protokołu OLTP w pamięci](https://msdn.microsoft.com/library/dn247639.aspx).
4. Jeśli tabela nie ma nieobsługiwałych funkcji, doradca może wykonać rzeczywisty schemat i migrację danych dla Ciebie.

#### <a name="manual-t-sql"></a>Instrukcja T-SQL
Aby użyć tej opcji migracji:

1. Połącz się z testową bazą danych przy użyciu usługi SSMS (lub podobnego narzędzia).
2. Uzyskaj pełny skrypt T-SQL dla tabeli i jej indeksów.
   
   * W pliku SSMS kliknij prawym przyciskiem myszy węzeł tabeli.
   * Kliknij pozycję **Tabela skryptów jako** > **polecenie UTWÓRZ do** > **nowego okna kwerendy**.
3. W oknie skryptu dodaj with (MEMORY_OPTIMIZED = ON) do instrukcji CREATE TABLE.
4. Jeśli istnieje indeks CLUSTERED, zmień go na NONCLUSTERED.
5. Zmień nazwę istniejącej tabeli za pomocą SP_RENAME.
6. Utwórz nową kopię tabeli zoptymalizowaną pod kątem pamięci, uruchamiając edytowany skrypt CREATE TABLE.
7. Skopiuj dane do tabeli zoptymalizowanej pod kątem pamięci za pomocą insert... WYBIERZ * W:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Krok 5 (opcjonalnie): Migrowanie procedur przechowywanych
Funkcja w pamięci można również zmodyfikować procedurę składowaną w celu zwiększenia wydajności.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Zagadnienia dotyczące natywnie skompilowanych procedur składowanych
Natywnie skompilowana procedura składowana musi mieć następujące opcje na klauzuli T-SQL WITH:

* NATIVE_COMPILATION
* SCHEMABINDING: czyli tabele, które procedura składowana nie może mieć ich definicje kolumn zmienione w jakikolwiek sposób, który miałby wpływ na procedurę składowaną, chyba że upuść procedury składowanej.

Moduł macierzysty musi używać jednego dużego [bloku ATOMIC](https://msdn.microsoft.com/library/dn452281.aspx) do zarządzania transakcjami. Nie ma żadnej roli dla jawnej transakcji BEGIN lub dla transakcji wycofywania. Jeśli kod wykryje naruszenie reguły biznesowej, można zakończyć niepodzielny blok z [throw](https://msdn.microsoft.com/library/ee677615.aspx) instrukcji.

### <a name="typical-create-procedure-for-natively-compiled"></a>Typowa procedura tworzenia dla natywnie skompilowanych
Zazwyczaj T-SQL do tworzenia natywnie skompilowanej procedury składowanej jest podobny do następującego szablonu:

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

* Dla TRANSACTION_ISOLATION_LEVEL SNAPSHOT jest najczęstszą wartością dla natywnie skompilowanej procedury składowanej. Jednak podzbiór innych wartości są również obsługiwane:
  
  * POWTARZALNY ODCZYT
  * Serializacji
* Wartość LANGUAGE musi być obecna w widoku sys.languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Jak przeprowadzić migrację procedury składowanej
Kroki migracji są następujące:

1. Uzyskaj skrypt procedury tworzenia do zwykłej interpretowanej procedury składowanej.
2. Przepisz jego nagłówek, aby dopasować go do poprzedniego szablonu.
3. Sprawdź, czy procedura składowana kod T-SQL używa żadnych funkcji, które nie są obsługiwane dla natywnie skompilowanych procedur składowanych. W razie potrzeby zaimplementuj obejścia.
   
   * Aby uzyskać szczegółowe informacje, zobacz [Problemy z migracją dla natywnie skompilowanych procedur przechowywanych](https://msdn.microsoft.com/library/dn296678.aspx).
4. Zmień nazwę starej procedury składowanej przy użyciu SP_RENAME. Lub po prostu drop it.
5. Uruchom edytowany skrypt T-SQL procedury tworzenia.

## <a name="step-6-run-your-workload-in-test"></a>Krok 6: Uruchom obciążenie w teście
Uruchom obciążenie w testowej bazie danych, które jest podobne do obciążenia, które działa w produkcyjnej bazie danych. Powinno to ujawnić przyrost wydajności osiągnięty przez użycie funkcji w pamięci dla tabel i procedur przechowywanych.

Główne atrybuty obciążenia to:

* Liczba równoczesnych połączeń.
* Współczynnik odczytu/zapisu.

Aby dostosować i uruchomić obciążenie testowe, należy rozważyć użycie poręcznego narzędzia ostress.exe, które zilustrowane [jest tutaj.](sql-database-in-memory.md)

Aby zminimalizować opóźnienia sieci, uruchom test w tym samym regionie geograficznym platformy Azure, w którym istnieje baza danych.

## <a name="step-7-post-implementation-monitoring"></a>Krok 7: Monitorowanie po wdrożeniu
Należy rozważyć monitorowanie wpływu wydajności implementacji w pamięci w produkcji:

* [Monitoruj pamięć masową w pamięci](sql-database-in-memory-oltp-monitoring.md).
* [Monitorowanie usługi Azure SQL Database przy użyciu dynamicznych widoków zarządzania](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Powiązane linki
* [Oltp w pamięci (optymalizacja w pamięci)](https://msdn.microsoft.com/library/dn133186.aspx)
* [Wprowadzenie do natywnie skompilowanych procedur przechowywanych](https://msdn.microsoft.com/library/dn133184.aspx)
* [Doradca optymalizacji pamięci](https://msdn.microsoft.com/library/dn284308.aspx)

