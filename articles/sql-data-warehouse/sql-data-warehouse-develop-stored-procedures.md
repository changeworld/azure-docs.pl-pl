---
title: Korzystanie z procedur składowanych
description: Wskazówki dotyczące wdrażania procedur składowanych w Azure SQL Data Warehouse tworzenia rozwiązań.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e6e1144043cbbbc8124785351e1e56a776b84527
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692812"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Korzystanie z procedur składowanych w SQL Data Warehouse
Wskazówki dotyczące wdrażania procedur składowanych w Azure SQL Data Warehouse tworzenia rozwiązań.

## <a name="what-to-expect"></a>Czego oczekiwać

SQL Data Warehouse obsługuje wiele funkcji T-SQL, które są używane w programie SQL Server. Co ważniejsze, dostępne są funkcje skalowania w poziomie, których można użyć w celu zmaksymalizowania wydajności rozwiązania.

Jednak w celu utrzymania skali i wydajności SQL Data Warehouse istnieją także pewne funkcje i funkcje, które mają różnice behawioralne i inne, które nie są obsługiwane.


## <a name="introducing-stored-procedures"></a>Wprowadzenie procedur składowanych
Procedury składowane to świetny sposób hermetyzowania kodu SQL; przechowywanie go blisko danych w magazynie danych. Procedury składowane ułatwiają deweloperom modularyzacji swoich rozwiązań poprzez hermetyzację kodu w jednostkach do zarządzania; zwiększenie możliwości ponownego wykorzystania kodu. Każda procedura składowana może również akceptować parametry, aby zapewnić im jeszcze większą elastyczność.

SQL Data Warehouse zapewnia uproszczoną i udoskonaloną implementację procedury składowanej. Największą różnicą w porównaniu do SQL Server jest to, że procedura składowana nie jest wstępnie kompilowanym kodem. W przypadku magazynów danych czas kompilacji jest mały w porównaniu do czasu potrzebnego do uruchamiania zapytań dotyczących dużych ilości danych. Należy upewnić się, że kod procedury składowanej jest poprawnie zoptymalizowany pod kątem dużych zapytań. Celem jest oszczędność godzin, minut i sekund, a nie milisekund. W związku z tym bardziej pomocne jest, aby traktować procedury składowane jako kontenery dla logiki SQL.     

Gdy SQL Data Warehouse wykonuje procedurę składowaną, instrukcje SQL są analizowane, tłumaczone i optymalizowane w czasie wykonywania. W trakcie tego procesu każda instrukcja jest konwertowana na zapytania rozproszone. Kod SQL, który jest wykonywany względem danych, różni się od przesłanego zapytania.

## <a name="nesting-stored-procedures"></a>Zagnieżdżanie procedur składowanych
Gdy procedury składowane wywołują inne procedury składowane lub wykonują dynamiczną instrukcję SQL, wewnętrzna procedura składowana lub wywołanie kodu są określane jako zagnieżdżone.

SQL Data Warehouse obsługuje maksymalnie osiem poziomów zagnieżdżenia. Jest to nieco inne SQL Server. Poziom zagnieżdżenia w SQL Server to 32.

Wywołanie procedury składowanej najwyższego poziomu jest równe zagnieżdżeniu poziomu 1.

```sql
EXEC prc_nesting
```
Jeśli procedura składowana wykonuje również inne wywołanie programu EXEC, poziom zagnieżdżenia zwiększy się do dwóch.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Jeśli druga procedura wykonuje następnie dynamiczny SQL, poziom zagnieżdżenia rośnie do trzech.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Uwaga SQL Data Warehouse obecnie nie obsługuje [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Musisz śledzić poziom zagnieżdżenia. Jest mało prawdopodobne, aby przekroczyć limit ośmiu poziomów zagnieżdżenia, ale jeśli to zrobisz, musisz ponownie obsłużyć swój kod, aby dopasować poziomy zagnieżdżenia w ramach tego limitu.

## <a name="insertexecute"></a>Wstaw.. WYKONANA
SQL Data Warehouse nie zezwala na korzystanie z zestawu wyników procedury składowanej z instrukcją INSERT. Istnieje jednak alternatywna metoda, której można użyć. Aby zapoznać się z przykładem, zapoznaj się z artykułem dotyczącym [tabel tymczasowych](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Ograniczenia
Istnieją pewne aspekty procedur składowanych Transact-SQL, które nie są zaimplementowane w SQL Data Warehouse.

Oto one:

* tymczasowe procedury składowane
* numerowane procedury składowane
* rozszerzone procedury składowane
* Procedury składowane CLR
* Opcja szyfrowania
* Opcja replikacji
* parametry z wartościami przechowywanymi w tabeli
* parametry tylko do odczytu
* parametry domyślne
* konteksty wykonywania
* Return — Instrukcja

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](sql-data-warehouse-overview-develop.md).

