---
title: Korzystanie z procedur składowanych
description: Porady dotyczące wdrażania procedur przechowywanych w usłudze Azure SQL Data Warehouse w celu opracowywania rozwiązań.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 83c3187c580bda33df8780a0e36f0fb9f2a4f484
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351571"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Korzystanie z procedur przechowywanych w magazynie danych SQL
Porady dotyczące wdrażania procedur przechowywanych w usłudze Azure SQL Data Warehouse w celu opracowywania rozwiązań.

## <a name="what-to-expect"></a>Czego się spodziewać

Usługa SQL Data Warehouse obsługuje wiele funkcji T-SQL, które są używane w programie SQL Server. Co ważniejsze, istnieją funkcje specyficzne skalowaj w poziomie, których można użyć, aby zmaksymalizować wydajność rozwiązania.

Jednak aby zachować skalę i wydajność usługi SQL Data Warehouse istnieją również pewne funkcje i funkcje, które mają różnice w zachowaniu i inne, które nie są obsługiwane.


## <a name="introducing-stored-procedures"></a>Wprowadzenie procedur składowanych
Procedury przechowywane są doskonałym sposobem hermetyzacji kodu SQL; przechowywanie go w pobliżu danych w magazynie danych. Procedury przechowywane pomagają deweloperom modularyzacji swoich rozwiązań, hermetyzując kod w jednostkach, którymi można zarządzać; ułatwienie ponownego wykorzystania kodu. Każda procedura składowana może również akceptować parametry, aby uczynić je jeszcze bardziej elastycznymi.

Usługa SQL Data Warehouse zapewnia uproszczoną i usprawnione implementację procedury składowanej. Największą różnicą w porównaniu do programu SQL Server jest to, że procedura składowana nie jest wstępnie skompilowanym kodem. W magazynach danych czas kompilacji jest mały w porównaniu do czasu, jaki zajmuje uruchamianie zapytań względem dużych woluminów danych. Jest ważniejsze, aby upewnić się, że kod procedury składowanej jest poprawnie zoptymalizowany pod kątem dużych zapytań. Celem jest zapisanie godzin, minut i sekund, a nie milisekund. Dlatego bardziej pomocne jest myślenie o procedurach przechowywanych jako kontenerach dla logiki SQL.     

Gdy usługa SQL Data Warehouse wykonuje procedurę składowaną, instrukcje SQL są analizowane, tłumaczone i optymalizowane w czasie wykonywania. Podczas tego procesu każda instrukcja jest konwertowana na zapytania rozproszone. Kod SQL, który jest wykonywany względem danych jest inny niż przesłane zapytanie.

## <a name="nesting-stored-procedures"></a>Procedury przechowywane zagnieżdżania
Gdy procedury składowane wywołać inne procedury składowane lub wykonać dynamiczny SQL, a następnie wewnętrznej procedury składowanej lub wywołania kodu mówi się, że zagnieżdżone.

Usługa SQL Data Warehouse obsługuje maksymalnie osiem poziomów zagnieżdżenia. To jest nieco inny niż SQL Server. Poziom gniazda w programie SQL Server wynosi 32.

Wywołanie procedury składowanej najwyższego poziomu jest równoznaczne z poziomem zagnieżdżenia 1.

```sql
EXEC prc_nesting
```
Jeśli procedura składowana również wykonuje inne wywołanie EXEC, poziom gniazda zwiększa się do dwóch.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Jeśli druga procedura następnie wykonuje niektóre dynamiczne SQL, poziom gniazda zwiększa się do trzech.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Uwaga: usługa SQL Data Warehouse nie obsługuje obecnie [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Musisz śledzić poziom gniazda. Jest mało prawdopodobne, aby przekroczyć limit poziomu ośmiu gniazd, ale jeśli to zrobisz, należy przerobić kod, aby dopasować poziomy zagnieżdżenia w tym limicie.

## <a name="insertexecute"></a>Wstawić.. Wykonać
Usługa SQL Data Warehouse nie zezwala na korzystanie z zestawu wyników procedury składowanej z instrukcją INSERT. Istnieje jednak alternatywne podejście, którego można użyć. Na przykład zobacz artykuł dotyczący [tabel tymczasowych](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Ograniczenia
Istnieją pewne aspekty procedur przechowywanych transact-SQL, które nie są implementowane w magazynie danych SQL.

Oto one:

* tymczasowe procedury składowane
* policzone procedury składowane
* rozszerzone procedury składowane
* Procedury przechowywane w clr
* opcja szyfrowania
* opcja replikacji
* parametry wycenione w tabeli
* Parametry tylko do odczytu
* parametry domyślne
* konteksty wykonania
* return, instrukcja

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](sql-data-warehouse-overview-develop.md).

