---
title: Używanie transakcji w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące implementowania transakcji w magazynie danych SQL Azure związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 7fa3d19cc0fca81616969773a40c3d3dbccc4a26
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31596929"
---
# <a name="using-transactions-in-sql-data-warehouse"></a>Używanie transakcji w magazynie danych SQL
Wskazówki dotyczące implementowania transakcji w magazynie danych SQL Azure związane z opracowywaniem rozwiązań.

## <a name="what-to-expect"></a>Czego oczekiwać
Jak można oczekiwać, Magazyn danych SQL obsługuje transakcje jako część obciążenia magazynu danych. Jednak aby upewnić się, że wydajność usługi SQL Data Warehouse jest przechowywany na dużą skalę niektóre funkcje są ograniczone w porównaniu z programem SQL Server. W tym artykule wyróżnia różnic oraz listę innych. 

## <a name="transaction-isolation-levels"></a>Poziom izolacji transakcji
Usługa SQL Data Warehouse implementuje transakcje ACID. Jednak poziom izolacji Obsługa transakcyjna jest ograniczona do READ UNCOMMITTED; Nie można zmienić na tym poziomie. Jeśli READ UNCOMMITTED jest istotny, można zaimplementować liczba kodowania metody zapobiegania odczytów danych. Najbardziej popularnych metod Użyj CTAS i przełączanie partycji tabeli (często określane jako zmienną wzorca okna) aby uniemożliwić użytkownikom wykonywanie zapytania na danych, który jest nadal przygotowany. Widoki, które wcześniej filtrować dane są także popularnych podejście.  

## <a name="transaction-size"></a>Rozmiar transakcji
Rozmiar jest ograniczony transakcji modyfikacji danych. Ten limit dotyczy poszczególnych dystrybucji. W związku z tym całkowitej alokacji Oblicza iloczyn limit liczby dystrybucji. Aby przybliżonej maksymalną liczbę wierszy w ramach transakcji podzielić zakończenia dystrybucji przez całkowity rozmiar każdego wiersza. Dla kolumn o zmiennej długości należy wziąć pod uwagę biorąc długość kolumny średnia, a nie o maksymalnym rozmiarze.

Zostały wprowadzone w tabeli poniżej następujące założenia:

* Wystąpił nawet rozkład danych 
* Długość wiersza średni to 250 bajtów

| [DWU](sql-data-warehouse-overview-what-is.md) | Cap na dystrybucji (GiB) | Liczba dystrybucji | Maksymalny rozmiar transakcji (GiB) | # Wierszy na dystrybucji | Maksymalna liczba wierszy na transakcję |
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

Aby zoptymalizować i zminimalizować ilość danych zapisywane w dzienniku, zapoznaj się [transakcji najlepsze rozwiązania](sql-data-warehouse-develop-best-practices-transactions.md) artykułu.

> [!WARNING]
> Rozmiar maksymalny transakcji można osiągnąć jedynie do wyznaczania wartości skrótu, a nawet ROUND_ROBIN rozproszonych tabel w przypadku rozpowszechniania danych. Jeśli transakcja jest zapisywania danych w sposób spowodowałoby zafałszowanie dystrybucje limit jest prawdopodobnie zostanie osiągnięty przed rozmiar maksymalny transakcji.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Stan transakcji
Magazyn danych SQL funkcja XACT_STATE() raport transakcji nie powiodło się przy użyciu wartości -2. Ta wartość oznacza transakcji nie powiodło się i jest oznaczony do wycofania tylko.

> [!NOTE]
> Korzystanie z -2 przy użyciu funkcji XACT_STATE do oznaczania transakcji nie powiodło się reprezentuje inaczej do programu SQL Server. Program SQL Server używa wartość -1 do reprezentowania transakcję niemożliwą do zatwierdzenia. SQL Server może tolerować błędy wewnątrz transakcji bez konieczności można oznaczyć jako niemożliwą do zatwierdzenia. Na przykład `SELECT 1/0` może spowodować wystąpienie błędu, ale nie wymusić transakcji w stanie niemożliwą do zatwierdzenia. SQL Server umożliwia również odczytów w transakcję niemożliwą do zatwierdzenia. Jednak usługa SQL Data Warehouse nie zezwala na to zrobić. Jeśli wystąpi błąd wewnątrz transakcji SQL Data Warehouse automatycznie przejdzie w stan-2 i nie można wprowadzić dowolne dodatkowe wybierz instrukcji do momentu instrukcja została wycofana. Dlatego jest ważne sprawdzić, czy kod aplikacji, aby sprawdzić, czy używa XACT_STATE() jako użytkownik może zajść potrzeba wprowadzenia modyfikacji kodu.
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

Poprzedni kod zawiera następujący komunikat o błędzie:

Msg 111233, poziom 16, stan 1, wiersz 1 111233; Bieżąca transakcja została przerwana, a wszystkie oczekujące zmiany mają została wycofana. Przyczyna: W stanie tylko do wycofania transakcji nie zostało jawnie wycofane przed DDL, DML lub instrukcji SELECT.

Nie można uzyskać dane wyjściowe funkcji ERROR_ *.

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

Zmieniono jest WYCOFYWANIA transakcji musiała mieć miejsce przed odczytu informacji o błędach w bloku CATCH.

## <a name="errorline-function"></a>ERROR_LINE() — funkcja
Warto również zauważyć, że magazyn danych SQL wdrożenia lub nie obsługuje ERROR_LINE() — funkcja. Jeśli masz to w kodzie, musisz usuń go, aby było zgodne z usługą Magazyn danych SQL. W zamian użyj etykiet zapytania w kodzie do zaimplementowania równoważne funkcje. Aby uzyskać więcej informacji, zobacz [etykiety](sql-data-warehouse-develop-label.md) artykułu.

## <a name="using-throw-and-raiserror"></a>Przy użyciu THROW i RAISERROR
THROW jest bardziej nowoczesnych wykonania na występowanie wyjątków w usłudze SQL Data Warehouse, ale obsługiwana jest również RAISERROR. Istnieje kilka różnic, które warto jednak zwracając uwagę.

* Liczby wiadomości zdefiniowane przez użytkownika błędu nie może być w zakresie 150 000 100 000 THROW
* Komunikaty o błędach RAISERROR są ustalone na poziomie 50 000
* Korzystanie z widoku sys.messages nie jest obsługiwane.

## <a name="limitations"></a>Ograniczenia
Magazyn danych SQL ma kilka ograniczeń, które odnoszą się do transakcji.

Są one w następujący sposób:

* Nie transakcji rozproszonych
* Nie transakcji zagnieżdżonych dozwolone
* Bez zapisu punkty dozwolone
* Brak transakcji o nazwie
* Nie zaznaczonych transakcji
* Brak obsługi języka DDL, takie jak CREATE TABLE wewnątrz transakcji użytkownika

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat optymalizacji transakcji, zobacz [transakcji najlepsze rozwiązania](sql-data-warehouse-develop-best-practices-transactions.md). Aby dowiedzieć się więcej o najlepszych rozwiązaniach innych SQL Data Warehouse, zobacz [najlepsze rozwiązania w zakresie usługi SQL Data Warehouse](sql-data-warehouse-best-practices.md).

