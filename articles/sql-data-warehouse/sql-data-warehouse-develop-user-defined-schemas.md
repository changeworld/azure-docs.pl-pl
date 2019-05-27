---
title: Za pomocą schematy definiowane przez użytkownika w usłudze SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące związane z opracowywaniem rozwiązań przy użyciu języka T-SQL schematy definiowane przez użytkownika w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 7e22dc69a9da1d9b5a8c0ff13f73769b1ed4514a
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861713"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Za pomocą schematy definiowane przez użytkownika w usłudze SQL Data Warehouse
Porady dotyczące związane z opracowywaniem rozwiązań przy użyciu języka T-SQL schematy definiowane przez użytkownika w usłudze Azure SQL Data Warehouse.

## <a name="schemas-for-application-boundaries"></a>Schematów dla granic aplikacji

Magazyny danych tradycyjnych często używają oddzielnych baz danych do tworzenia granic aplikacji, w oparciu o obciążenie, domeny lub zabezpieczeń. Na przykład tradycyjnego magazynu danych programu SQL Server może obejmować tymczasowej bazy danych, bazy danych magazynu danych i niektóre bazy danych składnicy danych. W tej topologii, każda baza danych działa jako obciążenie i granicy zabezpieczeń w architekturze.

Z drugiej strony usługa SQL Data Warehouse działa obciążenie magazynu danych w obrębie jednej bazy danych. Sprzężenia krzyżowego bazy danych nie są dozwolone. W związku z tym usługa SQL Data Warehouse oczekuje, że wszystkie tabele używane w magazynie mają być przechowywane w jednej bazie danych.

> [!NOTE]
> Usługa SQL Data Warehouse nie obsługuje zapytania obejmujące wiele baz danych dowolnego rodzaju. W związku z tym implementacje magazynu danych, które korzystają z tego wzorca należy skorygować.
> 
> 

## <a name="recommendations"></a>Rekomendacje
Poniżej przedstawiono zalecenia dotyczące konsolidację obciążeń, zabezpieczeń, domeny i funkcjonalności granice przy użyciu schematów zdefiniowane przez użytkownika

1. Używać jedna baza danych SQL Data Warehouse do uruchamiania obciążenia magazynu danych
2. Konsolidacja z istniejącym środowiskiem magazynu danych, aby używać jedna baza danych SQL Data Warehouse
3. Wykorzystaj **schematy definiowane przez użytkownika** do zapewnienia granicy ustawienie było wcześniej zaimplementowane przy użyciu bazy danych.

Jeśli schematy definiowane przez użytkownika nie zostały użyte wcześniej, można skorzystać z pustego. Po prostu użyć starej nazwy bazy danych jako podstawy dla swoje schematy definiowane przez użytkownika w bazie danych SQL Data Warehouse.

Jeśli używano już schematów masz kilka opcji:

1. Usuń nazwy starszej wersji schematu i zacznij od zera
2. Zachowanie nazwy schematu starszych wstępnie oczekujące nazwy starszej wersji schematu, do nazwy tabeli
3. Zachowują nazwy starszej wersji schematu, implementując widoków za pośrednictwem tabeli w schemacie dodatkowe ponownie utworzyć starej struktury schematu.

> [!NOTE]
> Na pierwszej kontroli opcja 3 może się wydawać najbardziej atrakcyjnymi opcji. Jednak devil jest w szczegółach. Widoki są tylko do odczytu w magazynie danych SQL. Wszelkie zmiany danych lub tabeli, należałoby były wykonywane wbrew tabeli podstawowej. Opcja 3 wprowadza również warstwy widoków do systemu. Warto nadać to rozwagą dodatkowe, jeśli używane są widoki w ramach architektury już.
> 
> 

### <a name="examples"></a>Przykłady:
Implementowanie schematy definiowane przez użytkownika na podstawie nazw bazy danych

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Zachowanie nazwy schematu starszych wstępnie oczekujące je do nazwy tabeli. Schematy na użytek granicę obciążenia.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Zachowaj nazw starszej wersji schematu, przy użyciu widoków

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Wszelkie zmiany w schemacie strategii wymaga przeglądu model zabezpieczeń dla bazy danych. W wielu przypadkach można uprościć model zabezpieczeń, przypisywania uprawnień na poziomie schematu. Bardziej szczegółowe uprawnienia są wymagane, można użyć ról bazy danych.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej porad programistycznych, zobacz [omówienie programowania w usłudze](sql-data-warehouse-overview-develop.md).

