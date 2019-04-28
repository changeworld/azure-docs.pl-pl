---
title: Migrowanie schematu w usłudze SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące migracji schematu do usługi Azure SQL Data Warehouse do opracowywania rozwiązań.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
origin.date: 04/17/2018
ms.date: 10/15/2018
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 4139ea776f6947eeacf4620c3676606d6535dd2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748156"
---
# <a name="migrate-your-schemas-to-sql-data-warehouse"></a>Migrowanie schematy usługi SQL Data Warehouse
Wskazówki dotyczące migracji swoje schematy SQL w usłudze SQL Data Warehouse. 

## <a name="plan-your-schema-migration"></a>Planowanie migracji schematu

Podczas planowania migracji, zobacz [Omówienie tabel] [ table overview] zapoznać się z tabeli projektowania, takich jak statystyki, dystrybucja, partycjonowanie i indeksowania.  Wyświetla także niektóre [nieobsługiwane funkcje tabeli] [ unsupported table features] i ich obejścia.

## <a name="use-user-defined-schemas-to-consolidate-databases"></a>Schematy definiowane przez użytkownika umożliwia konsolidowanie baz danych

Obciążenie istniejących prawdopodobnie ma więcej niż jednej bazy danych. Na przykład bazę danych programu SQL Server data warehouse może obejmować tymczasowej bazy danych, bazy danych magazynu danych i niektóre bazy danych składnicy danych. W tej topologii każda baza danych działa jako oddzielne obciążenia za pomocą zasad zabezpieczeń osobno.

Z drugiej strony usługa SQL Data Warehouse działa obciążenie magazynu danych w obrębie jednej bazy danych. Sprzężenia krzyżowego bazy danych nie są dozwolone. W związku z tym usługa SQL Data Warehouse oczekuje, że wszystkie tabele używane w magazynie danych mają być przechowywane w jednej bazie danych.

Zalecamy używanie schematy definiowane przez użytkownika do konsolidowania istniejące obciążenia w jednej bazie danych. Aby uzyskać przykłady, zobacz [schematy definiowane przez użytkownika](sql-data-warehouse-develop-user-defined-schemas.md)

## <a name="use-compatible-data-types"></a>Użyj kompatybilne typy danych
Modyfikowanie typów danych, aby były zgodne z usługą SQL Data Warehouse. Aby uzyskać listę typów danych obsługiwanych i nieobsługiwanych zobacz [typy danych][data types]. Ten temat zawiera rozwiązania dla nieobsługiwane typy. Umożliwia także zapytanie, aby zidentyfikować istniejące typy, które nie są obsługiwane w usłudze SQL Data Warehouse.

## <a name="minimize-row-size"></a>Minimalizuj rozmiar wiersza
Aby uzyskać najlepszą wydajność należy zminimalizować długość wiersza tabel. Ponieważ krótszy długości wiersza prowadzić do zapewnienia lepszej wydajności, należy użyć najmniejszy typy danych, które działają dla swoich danych. 

Szerokość wiersza tabeli programu PolyBase obowiązuje limit 1 MB.  Jeśli planujesz ładowanie danych do usługi SQL Data Warehouse przy użyciu technologii PolyBase, zaktualizować tabele mieć szerokość maksimum, mniej niż 1 MB. 

## <a name="specify-the-distribution-option"></a>Wybierz opcję dystrybucji
Usługa SQL Data Warehouse to Rozproszony system baz danych. Każda tabela jest rozproszone lub replikowane w węzłach obliczeniowych. Brak opcji tabeli, która pozwala określić sposób dystrybucji danych. Dostępne są następujące mechanizmy działania okrężnego replikowane, lub rozproszonego wyznaczania wartości skrótu. Każdy ma zalety i wady. Jeśli nie określisz opcji dystrybucji, usługa SQL Data Warehouse użyje działanie okrężne jako domyślny.

- Działanie okrężne jest ustawieniem domyślnym. Jest najprostsze w użyciu i ładowania danych tak szybko, jak to możliwe, ale sprzęga wymaga przenoszenia danych, która zmniejsza wydajność zapytań.
- Zreplikowane zapisuje kopię tabeli w każdym węźle obliczeniowym. Zreplikowane tabele są wydajne, ponieważ nie wymagają przenoszenia danych do sprzęgania i agregacji. One wymagają dodatkowego magazynu, a w związku z tym najlepiej działać dla mniejszych tabel.
- Skrót rozproszonych dystrybuuje wiersze we wszystkich węzłach za pomocą funkcji skrótu. Tabele zbędnych danych rozproszonych są serce usługi SQL Data Warehouse, ponieważ są one przeznaczone do świadczenia wysoką wydajność zapytań w dużych tabel. Ta opcja wymaga pewnego planowania wybierz najlepsze kolumnę, w którym można rozpowszechnić danych. Jednak jeśli nie wybierzesz najlepsze kolumny po raz pierwszy, można łatwo ponownie dystrybuować dane na inną kolumnę. 

Aby wybrać najlepszą opcją dystrybucji dla każdej tabeli, zobacz [tabelach rozproszonych](sql-data-warehouse-tables-distribute.md).

## <a name="next-steps"></a>Kolejne kroki
Gdy zostały pomyślnie zmigrowane schemat bazy danych w usłudze SQL Data Warehouse, przejdź do jednego z następujących artykułów:

* [Migrowanie danych][Migrate your data]
* [Migrowanie kodu][Migrate your code]

Aby uzyskać więcej informacji o najlepszych praktykach SQL Data Warehouse, zobacz [najlepsze praktyki] [ best practices] artykułu.

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

<!--Update_Description: update meta properties, add new content about Migrate schemas to SQL Data Warehouse -->