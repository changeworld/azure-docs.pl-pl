---
title: Migracja rozwiązania do usługi SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące migracji za rozwiązania do platformy Azure SQL Data Warehouse.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: 5a609fb2da1f9dba1247358f64b284fc3e3ef5bc
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="migrate-your-solution-to-azure-sql-data-warehouse"></a>Migracja rozwiązania do usługi Azure SQL Data Warehouse
Zobacz, na czym polega migracji istniejącego rozwiązania bazy danych do usługi Azure SQL Data Warehouse. 

## <a name="profile-your-workload"></a>Profilu obciążenia
Przed przeprowadzeniem migracji, należy się, że niektóre usługi SQL Data Warehouse to odpowiednie rozwiązanie dla obciążenia. Magazyn danych SQL to Rozproszony system przeznaczone do wykonywania analizy w dużej ilości danych.  Migracja do usługi SQL Data Warehouse wymaga pewnych zmian projektowych, które nie są zbyt niezbędne, aby dowiedzieć się, ale może potrwać pewien czas do wykonania. Firmy wymaga hurtowni danych klasy korporacyjnej, korzyści są warto działania. Jednak jeśli nie potrzebujesz możliwości usługi SQL Data Warehouse jest bardziej ekonomiczne rozwiązanie, aby użyć serwera SQL lub bazy danych SQL Azure.

Należy rozważyć użycie usługi SQL Data Warehouse przy możesz:
- Ma co najmniej jeden terabajtów danych
- Zaplanuj uruchamianie analityka w dużych ilości danych
- Będą potrzebowali możliwości skalowania zasobów obliczeniowych i magazynu 
- Czy chcesz zmniejszyć koszty wstrzymywanie zasoby obliczeniowe, gdy nie są potrzebne.

Magazyn danych SQL nie jest używany do operacyjnej obciążenia (OLTP), które mają:
- Wysoka częstotliwość odczyty i zapisy
- Wybiera dużą liczbę pojedynczych
- Dużej ilości wstawia pojedynczy wiersz
- Wiersz po wierszu przetwarzania potrzeb
- Niezgodnych formatach (JSON, XML)


## <a name="plan-the-migration"></a>Planowanie migracji

Po określeniu migrację istniejącego rozwiązania do usługi SQL Data Warehouse, koniecznie planowania migracji, przed rozpoczęciem pracy. 

Jeden celem planowania jest zapewnienie, że dane, z tabeli schematów i kodu są zgodne z usługi SQL Data Warehouse. Istnieją pewne różnice zgodności w celu obejścia między bieżącego systemu i SQL Data Warehouse. Ponadto migracji dużych ilości danych do platformy Azure wymaga czasu. Należy dokładnie zaplanować przyspiesza pobieranie danych do platformy Azure. 

Innym celem planowania jest zmiany projektu upewnij się, że rozwiązanie korzysta z wysoką wydajność zapytań przez usługi SQL Data Warehouse pozwala uzyskać. Projektowanie hurtowni danych do skalowania wprowadza różnych projektowy wzorców i dlatego tradycyjnych metod nie zawsze są najlepiej. Mimo że pewnych zmian projektu mogą być wykonywane po migracji, wcześniej wprowadzania zmian w procesie zapisze późniejszym czasie.

Do przeprowadzenia prawidłowej migracji, należy przeprowadzić migrację z tabeli schematów, kodu i danych. Aby uzyskać wskazówki dotyczące tych tematów migracji zobacz:

-  [Migracja z schematów](sql-data-warehouse-migrate-schema.md)
-  [Migrowanie kodu](sql-data-warehouse-migrate-code.md)
-  [Migrowanie danych](sql-data-warehouse-migrate-data.md). 

<!--
## Perform the migration


## Deploy the solution


## Validate the migration

-->

## <a name="next-steps"></a>Kolejne kroki
CAT (zespół doradczy klientów) ma także niektóre dużą wskazówek magazyn danych SQL, które publikują one za pośrednictwem blogów.  Spójrz na ich artykułu [migrowanie danych do usługi Azure SQL Data Warehouse w praktyce] [ Migrating data to Azure SQL Data Warehouse in practice] dodatkowe wskazówki dotyczące migracji.

<!--Image references-->

<!--Article references-->

<!--MSDN references-->

<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
