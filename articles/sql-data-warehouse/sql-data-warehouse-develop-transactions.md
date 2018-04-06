---
title: Transakcje w usłudze SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące implementowania transakcji w magazynie danych SQL Azure związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: ''
ms.assetid: ae621788-e575-41f5-8bfe-fa04dc4b0b53
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 29d53e18539f2c24dd64090b2ac6f9dd4c783961
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="transactions-in-sql-data-warehouse"></a>Transakcje w usłudze SQL Data Warehouse
Jak można oczekiwać, Magazyn danych SQL obsługuje transakcje jako część obciążenia magazynu danych. Jednak aby upewnić się, że wydajność usługi SQL Data Warehouse jest przechowywany na dużą skalę niektóre funkcje są ograniczone w porównaniu z programem SQL Server. W tym artykule wyróżnia różnic oraz listę innych. 

## <a name="transaction-isolation-levels"></a>Poziom izolacji transakcji
Usługa SQL Data Warehouse implementuje transakcje ACID. Jednak izolację Obsługa transakcyjna jest ograniczona do `READ UNCOMMITTED` i nie można zmienić. Można zaimplementować wiele metod, aby zapobiec odczytów danych, jeśli jest to istotny kodowania. Najbardziej popularnych metod korzystaj z CTAS i przełączanie partycji tabeli (często określane jako zmienną wzorca okna), aby uniemożliwić użytkownikom wykonywanie zapytania na danych, który jest nadal przygotowany. Widoki, które wcześniej filtrowania danych jest także popularnych podejście.  

## <a name="transaction-size"></a>Rozmiar transakcji
Rozmiar jest ograniczony transakcji modyfikacji danych. Ten limit dotyczy dzisiaj "na dystrybucji". W związku z tym całkowitej alokacji Oblicza iloczyn limit liczby dystrybucji. Aby przybliżonej maksymalną liczbę wierszy w ramach transakcji podzielić zakończenia dystrybucji przez całkowity rozmiar każdego wiersza. Dla kolumn o zmiennej długości należy wziąć pod uwagę biorąc długość kolumny średnia, a nie o maksymalnym rozmiarze.

Zostały wprowadzone w tabeli poniżej następujące założenia:

* Wystąpił nawet rozkład danych 
* Długość wiersza średni to 250 bajtów

| [DWU][DWU] | Cap na dystrybucji (GiB) | Liczba dystrybucji | Maksymalny rozmiar transakcji (GiB) | # Wierszy na dystrybucji | Maksymalna liczba wierszy na transakcję |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1.5 |60 |90 |6 000 000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5 |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

Limit rozmiaru transakcji jest stosowane osobno do każdej operacji lub transakcji. Nie została zastosowana we wszystkich równoczesnych transakcji. W związku z tym każdą transakcję może zapisywać ilość danych w dzienniku. 

Aby zoptymalizować oraz zminimalizować ilość danych zapisywane w dzienniku, zapoznaj się [transakcji najlepsze rozwiązania] [ Transactions best practices] artykułu.

> [!WARNING]
> Rozmiar maksymalny transakcji można osiągnąć jedynie do wyznaczania wartości skrótu, a nawet ROUND_ROBIN rozproszonych tabel w przypadku rozpowszechniania danych. Jeśli transakcja jest zapisywania danych w sposób spowodowałoby zafałszowanie dystrybucje limit jest prawdopodobnie zostanie osiągnięty przed rozmiar maksymalny transakcji.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Stan transakcji
Magazyn danych SQL funkcja XACT_STATE() raport transakcji nie powiodło się przy użyciu wartości -2. Oznacza to, że transakcja nie powiodła się i jest oznaczony do wycofania tylko

> [!NOTE]
> Korzystanie z -2 przy użyciu funkcji XACT_STATE do oznaczania transakcji nie powiodło się reprezentuje inaczej do programu SQL Server. Program SQL Server używa wartość -1 do reprezentowania której transakcji. SQL Server może tolerować błędy wewnątrz transakcji bez konieczności można oznaczyć jako której. Na przykład `SELECT 1/0` może spowodować błąd, ale nie wymusić transakcji, w której stan. SQL Server umożliwia również odczytów w ramach której transakcji. Jednak usługa SQL Data Warehouse nie zezwala na to zrobić. Jeśli wystąpi błąd wewnątrz transakcji SQL Data Warehouse automatycznie przejdzie w stan-2 i nie można wprowadzić dowolne dodatkowe wybierz instrukcji do momentu instrukcja została wycofana. Dlatego jest ważne sprawdzić, czy kod aplikacji, aby sprawdzić, czy używa XACT_STATE() jako użytkownik może zajść potrzeba wprowadzenia modyfikacji kodu.
> 
> 

Na przykład w programie SQL Server można napotkać transakcji, która wygląda następująco:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Jeśli pole kodu jest powyżej, a następnie otrzymasz następujący komunikat o błędzie:

Msg 111233, poziom 16, stan 1, wiersz 1 111233; Bieżąca transakcja została przerwana, a wszystkie oczekujące zmiany mają została wycofana. Przyczyna: W stanie tylko do wycofania transakcji nie zostało jawnie wycofane przed DDL i DML instrukcji SELECT.

Nie również otrzymasz dane wyjściowe funkcji ERROR_ *.

W usłudze SQL Data Warehouse kod wymaga nieco zmiany:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Obserwuje się oczekiwane zachowanie. Błąd w ramach transakcji jest zarządzana i funkcje ERROR_ * Podaj wartości, zgodnie z oczekiwaniami.

Wszystkie zmodyfikowaną jest to, że `ROLLBACK` transakcji ma nastąpić przed odczytu informacji o błędach w `CATCH` bloku.

## <a name="errorline-function"></a>ERROR_LINE() — funkcja
Warto również zauważyć, że magazyn danych SQL wdrożenia lub nie obsługuje ERROR_LINE() — funkcja. Jeśli to w kodzie, należy ją usunąć, aby było zgodne z usługą Magazyn danych SQL. W zamian użyj etykiet zapytania w kodzie do zaimplementowania równoważne funkcje. Zapoznaj się z [etykiety] [ LABEL] artykułu, aby uzyskać więcej informacji na temat tej funkcji.

## <a name="using-throw-and-raiserror"></a>Przy użyciu THROW i RAISERROR
THROW jest bardziej nowoczesnych wykonania na występowanie wyjątków w usłudze SQL Data Warehouse, ale obsługiwana jest również RAISERROR. Istnieje kilka różnic, które warto jednak zwracając uwagę.

* Numery nie mogą znajdować się w zakresie 150 000 100 000 THROW komunikaty o błędach zdefiniowane przez użytkownika
* Komunikaty o błędach RAISERROR są ustalone na poziomie 50 000
* Korzystanie z widoku sys.messages nie jest obsługiwane.

## <a name="limitiations"></a>Limitiations
Magazyn danych SQL ma kilka ograniczeń, które odnoszą się do transakcji.

Są one w następujący sposób:

* Nie transakcji rozproszonych
* Nie transakcji zagnieżdżonych dozwolone
* Bez zapisu punkty dozwolone
* Brak transakcji o nazwie
* Nie zaznaczonych transakcji
* Brak obsługi języka DDL, takie jak `CREATE TABLE` wewnątrz użytkownika zdefiniowanych transakcji

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat optymalizacji transakcji, zobacz [transakcji najlepsze rozwiązania][Transactions best practices].  Aby dowiedzieć się więcej o najlepszych rozwiązaniach innych SQL Data Warehouse, zobacz [najlepsze rozwiązania w zakresie usługi SQL Data Warehouse][SQL Data Warehouse best practices].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[development overview]: ./sql-data-warehouse-overview-develop.md
[Transactions best practices]: ./sql-data-warehouse-develop-best-practices-transactions.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->
