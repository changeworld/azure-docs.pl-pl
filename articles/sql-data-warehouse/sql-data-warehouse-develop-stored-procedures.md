---
title: Korzystanie z procedur składowanych w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące wykonywania procedur składowanych w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 200433d95d62edf2e878e58e5089a6baff290775
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65850583"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Korzystanie z procedur składowanych w usłudze SQL Data Warehouse
Porady dotyczące wykonywania procedur składowanych w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.

## <a name="what-to-expect"></a>Czego oczekiwać

Usługa SQL Data Warehouse obsługuje wiele funkcji języka T-SQL, które są używane w programie SQL Server. Co ważniejsze brak określonych funkcji skalowania w poziomie, które można użyć aby zmaksymalizować wydajność rozwiązania.

Aby zachować skalowalność i wydajność usługi SQL Data Warehouse są jednak także niektóre funkcje i możliwości, który ma różnice w zachowaniu i innym osobom, które nie są obsługiwane.


## <a name="introducing-stored-procedures"></a>Wprowadzenie do procedury składowane
Procedury składowane to doskonały sposób do enkapsulacji kodu SQL; zapisanie go w pobliżu danych w magazynie danych. Procedury składowane pomogą modularyzacji swoich rozwiązań poprzez hermetyzację kod w jednostki zarządzane; ułatwienie większa możliwość ponownego wykorzystania kodu. Każdej procedury składowanej mogą również akceptować parametry, aby były bardziej elastyczne.

Usługa SQL Data Warehouse zapewnia wykonanie procedury składowanej uproszczony i zoptymalizowany. Największa różnica w porównaniu do programu SQL Server jest, że procedura składowana nie jest wstępnie skompilowany kod. W hurtowni danych czas kompilacji jest mały w porównaniu z czasu potrzebnego do uruchamiania zapytań względem dużych ilości danych. Jest to niezwykle ważne upewnić się, że kod procedury składowanej poprawnie została zoptymalizowana pod kątem dużych kwerend. Celem jest zapisanie godzin, minut i sekund, nie milisekund. Dlatego jest bardziej użyteczne do procedur składowanych należy traktować jako kontenery logiki SQL.     

Usługa SQL Data Warehouse jest wykonywana Twoja procedura składowana, instrukcje SQL są przeanalizować, przetłumaczone i zoptymalizowane pod kątem w czasie wykonywania. W trakcie tego procesu każda instrukcja jest konwertowany na zapytań rozproszonych. Kod SQL, który jest wykonywane względem danych jest inny niż przesłano zapytanie.

## <a name="nesting-stored-procedures"></a>Zagnieżdżanie przechowywanych procedur
Procedury składowane wywołania innych procedur składowanych lub wykonać dynamiczny język SQL, następnie procedurę składowaną wewnętrzny lub wywołania kodu jest uznawany za zagnieżdżony.

Usługa SQL Data Warehouse obsługuje maksymalnie ośmiu poziomów zagnieżdżenia. To różni się nieco do programu SQL Server. Poziom zagnieżdżonych w programie SQL Server to 32.

Wywołanie procedury składowanej najwyższego poziomu jest równa zagnieździć poziomu 1.

```sql
EXEC prc_nesting
```
Jeśli procedura składowana sprawia, że inny EXEC wywołania, poziom zagnieżdżonych zwiększa się do dwóch.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Jeśli druga procedura wykonuje następnie niektóre dynamiczny język SQL, poziom zagnieżdżonych zwiększa się do trzech.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Należy pamiętać, że usługa SQL Data Warehouse nie obsługuje obecnie [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Należy śledzić poziom zagnieżdżonych. Jest mało prawdopodobne, przekroczenie limitu poziomu osiem zagnieżdżonych, ale jeśli to zrobisz, musisz przerabiać swój kod, aby dopasować poziomów zagnieżdżenia w ramach tego limitu.

## <a name="insertexecute"></a>WSTAW... WYKONYWANIE
Usługa SQL Data Warehouse pozwala na korzystanie z zestawu wyników procedury składowanej w instrukcji INSERT. Jednak to podejście alternatywne, których można użyć. Aby uzyskać przykład, zobacz artykuł [tabele tymczasowe](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Ograniczenia
Istnieją pewne aspekty procedury przechowywanej Transact-SQL, które nie są implementowane w usłudze SQL Data Warehouse.

Oto one:

* tymczasowych procedur składowanych
* numerowane procedury składowane
* Rozszerzone procedury składowane
* Procedury składowane CLR
* Opcja szyfrowania
* Opcja replikacji
* Parametry z wartościami przechowywanymi w tabeli
* Parametry tylko do odczytu
* domyślne parametry
* Kontekst wykonywania
* Return — instrukcja

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej porad programistycznych, zobacz [omówienie programowania w usłudze](sql-data-warehouse-overview-develop.md).

