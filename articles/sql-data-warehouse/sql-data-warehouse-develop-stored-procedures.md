---
title: Korzystanie z procedur składowanych w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące implementowania procedur przechowywanych w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 74e943548ff97a04231e2affb645daa1e4b826a2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Korzystanie z procedur składowanych w magazynie danych SQL
Wskazówki dotyczące implementowania procedur przechowywanych w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.

## <a name="what-to-expect"></a>Czego oczekiwać

Magazyn danych SQL obsługuje wiele funkcji T-SQL, które są używane w programie SQL Server. Istnieją co ważniejsze, skalowalnych w poziomie określonych funkcji, które mogą używać w celu zwiększenia wydajności rozwiązania.

Aby zachować skalowalność i wydajność usługi SQL Data Warehouse są jednak także niektóre funkcje i funkcje, które mają różnice funkcjonalne i inne osoby, które nie są obsługiwane.


## <a name="introducing-stored-procedures"></a>Wprowadzenie do procedury składowane
Procedury składowane są to dobry sposób na hermetyzując kodu SQL; zapisanie jej pobliżu danych w magazynie danych. Procedury składowane pomóc deweloperom modularyzacji ich rozwiązania hermetyzując kod w jednostki zarządzane; ułatwianie większa możliwość ponownego wykorzystania kodu. Każdej procedury składowanej również może przyjmować parametry, aby były bardziej elastyczne.

Magazyn danych SQL zawiera implementację uproszczony i zoptymalizowany procedury składowanej. Największych różnicę w porównaniu z programem SQL Server jest, że procedura składowana nie jest wstępnie skompilowanego kodu. W magazynie danych czas kompilacji jest mała, w odróżnieniu od czasu, jaki zajmuje do wykonywania kwerend do dużych ilości danych. Więcej koniecznie upewnij się, że kod procedury składowanej poprawnie jest zoptymalizowana pod kątem duże kwerendy. Celem jest zapisać godzin, minut i sekund, nie milisekund. Dlatego jest bardziej użyteczne do procedury składowane można traktować jako kontenery logiki SQL.     

Magazyn danych SQL wykonuje procedury przechowywanej, instrukcje SQL są przeanalizować, translacji i zoptymalizowany w czasie wykonywania. W trakcie tego procesu każda instrukcja jest konwertowany na zapytań rozproszonych. Kod SQL y wykonywane względem danych różni się od zapytania przesłane.

## <a name="nesting-stored-procedures"></a>Procedury przechowywane zagnieżdżenia
Procedury składowane wywołania innych procedur składowanych lub wykonać dynamicznej SQL, następnie wewnętrznej procedury składowanej lub wywołanie kodu jest uznawany za zagnieżdżony.

Magazyn danych SQL obsługuje maksymalnie osiem poziomów zagnieżdżenia. Różni się nieco do programu SQL Server. Poziom zagnieździć w programie SQL Server to 32.

Wywołanie procedury składowanej najwyższego poziomu jest równa zagnieździć poziomu 1.

```sql
EXEC prc_nesting
```
Jeśli procedura składowana powoduje również EXEC innego wywołania, poziom zagnieżdżania zwiększa się do dwóch.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Jeśli drugiej procedury wykonywania niektórych dynamiczne SQL, poziom zagnieżdżania zwiększa się do trzech.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Należy pamiętać, że SQL Data Warehouse nie obsługuje obecnie [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Należy śledzić poziom zagnieżdżania. Prawdopodobnie przekracza limit poziomu zagnieżdżania osiem, ale jeśli to zrobisz, należy przeanalizować swój kod, aby dopasować poziomów zagnieżdżenia w ramach tego limitu.

## <a name="insertexecute"></a>INSERT... WYKONANIE
Magazyn danych SQL nie pozwala na korzystanie z zestawu wyników procedury składowanej w instrukcji INSERT. Istnieje jednak alternatywne podejście, których można użyć. Na przykład, zobacz artykuł na [tabel tymczasowych](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Ograniczenia
Brak niektórych aspektów procedury przechowywanej Transact-SQL, które nie są zaimplementowane w usłudze SQL Data Warehouse.

Oto one:

* tymczasowych procedur składowanych
* numerowane procedury składowane
* rozszerzone procedury składowane
* Procedur składowanych CLR
* 
* Opcja szyfrowania
* opcji replikacji
* Parametry przechowywanymi w tabeli
* Parametry tylko do odczytu
* domyślne parametry
* Kontekst wykonywania
* Return — instrukcja

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej porad programistycznych, zobacz [omówienie tworzenia](sql-data-warehouse-overview-develop.md).

