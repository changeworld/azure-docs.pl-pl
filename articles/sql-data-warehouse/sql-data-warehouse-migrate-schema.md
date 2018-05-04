---
title: Migrowanie schemat do usługi SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące migrowania schemat Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: fb1085450a16acb0f9a06a9dea9d91fc5ca23363
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="migrate-your-schemas-to-sql-data-warehouse"></a>Migracja z schematów SQL Data Warehouse
Wskazówki dotyczące migrowania schematy programu SQL do SQL Data Warehouse. 

## <a name="plan-your-schema-migration"></a>Planowanie migracji do schematu

Podczas planowania migracji, zobacz [omówienie tabeli] [ table overview] zapoznać się z tabeli projektowania, takich jak statystyka, dystrybucja, partycjonowanie i indeksowania.  Przedstawiono także niektóre [nieobsługiwane funkcje tabeli] [ unsupported table features] i ich obejścia.

## <a name="use-user-defined-schemas-to-consolidate-databases"></a>Schematy użytkownika umożliwia konsolidowanie baz danych

Obciążenie istniejących prawdopodobnie ma więcej niż jednej bazy danych. Na przykład magazynu danych programu SQL Server może obejmować tymczasowej bazy danych, bazy danych magazynu danych i niektóre bazy danych składnicy danych. W tej topologii każda baza danych jest uruchamiany jako oddzielny obciążenia przy użyciu zasad zabezpieczeń.

Z kolei SQL Data Warehouse uruchamia obciążeniu magazynu danych w ramach jednej bazy danych. Sprzężenia między bazy danych nie są dozwolone. W związku z tym usługa SQL Data Warehouse oczekuje, że wszystkie tabele używane przez Magazyn danych mają być przechowywane w ramach jednej bazy danych.

Zalecamy używanie schematów zdefiniowane przez użytkownika na konsolidację istniejące obciążenia na jedną bazę danych. Przykłady można znaleźć [schematy zdefiniowane przez użytkownika](sql-data-warehouse-develop-user-defined-schemas.md)

## <a name="use-compatible-data-types"></a>Użyj kompatybilne typy danych
Zmodyfikuj typów danych w celu zachowania zgodności z usługą Magazyn danych SQL. Dla listy typów obsługiwanych i nieobsługiwanych danych, zobacz [typy danych][data types]. Ten temat zawiera obejścia dla nieobsługiwane typy. Umożliwia także zapytania, aby zidentyfikować istniejące typy, które nie są obsługiwane w usłudze SQL Data Warehouse.

## <a name="minimize-row-size"></a>Minimalizuj rozmiar wiersza
Aby uzyskać najlepszą wydajność należy zminimalizować długość wiersza równą tabel. Ponieważ krótszą długości wiersza prowadzić do zapewnienia lepszej wydajności, należy użyć najmniejszą typy danych, które działają danych. 

Szerokość wiersza tabeli PolyBase ma limit 1 MB.  Jeśli planujesz ładowanie danych do usługi SQL Data Warehouse przy użyciu programu PolyBase, zaktualizuj tabele mieć szerokość maksymalna wiersza mniej niż 1 MB. 

<!--
- For example, this table uses variable length data but the largest possible size of the row is still less than 1 MB. PolyBase will load data into this table.

- This table uses variable length data and the defined row width is less than one MB. When loading rows, PolyBase allocates the full length of the variable-length data. The full length of this row is greater than one MB.  PolyBase will not load data into this table.  

-->

## <a name="specify-the-distribution-option"></a>Wybierz opcję dystrybucji
Usługa SQL Data Warehouse to system rozproszoną bazę danych. Każda tabela jest dystrybuowane lub replikowane w węzłach obliczeniowych. Brak opcji tabeli, która umożliwia określenie sposobu dystrybucji danych. Dostępne są okrężnego, replikowane, albo rozproszone wyznaczania wartości skrótu. Każdy ma zalet i wad. Jeśli nie określisz opcję dystrybucji SQL Data Warehouse użyje okrężnego jako domyślny.

- Działanie okrężne jest ustawieniem domyślnym. Najprościej do użycia i obciążeń dane tak szybko, jak to możliwe, ale sprzęga wymaga przenoszenia danych, co zmniejsza szybkość działania zapytania.
- Zreplikowane magazyny kopii tabeli w każdym węźle obliczeń. Zreplikowane tabele są wydajność, ponieważ nie wymaga przenoszenia danych do sprzęgania i agregacji. One wymagają dodatkowe miejsce do magazynowania, a w związku z tym najlepiej dla mniejszych tabel.
- Skrót rozproszonych dystrybuuje wiersze we wszystkich węzłach za pomocą funkcji skrótu. Tabele hash rozproszone są Puls usługi SQL Data Warehouse, ponieważ są one przeznaczone do zapewniają wysoką wydajność zapytań na dużych tabel. Ta opcja wymaga niektórych planowanie Wybierz najlepsze kolumny, na których chcesz rozesłać danych. Jednak jeśli nie wybierzesz najlepsze kolumny po raz pierwszy, można łatwo ponownie dystrybuować dane na inną kolumnę. 

Aby wybrać najlepszą opcją dystrybucji dla każdej tabeli, zobacz [rozproszonych tabel](sql-data-warehouse-tables-distribute.md).


## <a name="next-steps"></a>Kolejne kroki
Gdy schemat bazy danych zostały pomyślnie zmigrowane do magazynu danych SQL, przejdź do jednej z następujących artykułów:

* [Migrowanie danych][Migrate your data]
* [Migrowanie kodu][Migrate your code]

Aby uzyskać więcej informacji o najlepszych rozwiązaniach SQL Data Warehouse, zobacz [najlepsze rozwiązania] [ best practices] artykułu.

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types

<!--MSDN references-->


<!--Other Web references-->
