---
title: Przy użyciu schematów zdefiniowane przez użytkownika w usłudze SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące używania schematów użytkownika T-SQL w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: c18e6d34416390ae7e93b69b28d508a540f7b1ab
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>W usłudze SQL Data Warehouse przy użyciu schematów zdefiniowane przez użytkownika
Porady dotyczące używania schematów użytkownika T-SQL w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.

## <a name="schemas-for-application-boundaries"></a>Schematy dla granic aplikacji

Magazyny danych tradycyjnych często używają oddzielne bazy danych do tworzenia aplikacji granic na podstawie obciążenia, domeny lub zabezpieczeń. Na przykład tradycyjnego magazynu danych programu SQL Server może obejmować tymczasowej bazy danych, bazy danych magazynu danych i niektóre bazy danych składnicy danych. W tej topologii każda baza danych działa jako obciążenia i granicy zabezpieczeń w architekturze.

Z kolei SQL Data Warehouse uruchamia obciążeniu magazynu danych w ramach jednej bazy danych. Sprzężenia między bazy danych nie są dozwolone. W związku z tym usługa SQL Data Warehouse oczekuje, że wszystkie tabele używane w magazynie, aby były przechowywane w ramach jednej bazy danych.

> [!NOTE]
> Usługa SQL Data Warehouse nie obsługuje kwerend bazy danych dowolnego rodzaju. W rezultacie implementacji magazynu danych, korzystających z tego wzorca należy ocenić.
> 
> 

## <a name="recommendations"></a>Zalecenia
Są to zalecenia na konsolidację obciążeń, zabezpieczeń, domeny i funkcjonalności granice przy użyciu schematów zdefiniowane przez użytkownika

1. Użyj jednej bazy danych magazynu danych SQL, aby uruchomić obciążenie magazynu danych
2. Konsolidacja z istniejącym środowiskiem magazynu danych do użycia jednej bazy danych magazynu danych SQL
3. Wykorzystywanie **użytkownika schematów** ustanowienie granicę implementowana przy użyciu baz danych.

Jeśli schematy zdefiniowane przez użytkownika nie zostały użyte wcześniej użytkownik ma pustego. Po prostu użyć starej nazwy bazy danych jako podstawa dla Twojego schematów zdefiniowane przez użytkownika w bazie danych usługi SQL Data Warehouse.

Jeśli używasz już schematy masz kilka opcji:

1. Usuń nazwy starszej wersji schematu i rozpocząć od nowa pracę
2. Zachowanie nazwy schematu starszych wstępnie oczekujące nazwę starszej wersji schematu do nazwy tabeli
3. Zachowują nazwy starszej wersji schematu, implementując widoków na tabeli w schemacie dodatkowe ponownie utworzyć starego Struktura schematu.

> [!NOTE]
> Na pierwszej kontroli opcja 3 może się wydawać najbardziej atrakcyjnymi opcji. Jednak devil jest szczegółowo. Widoki są tylko do odczytu w magazynie danych SQL. Każda zmiana danych lub tabeli musi być wykonane względem tabeli podstawowej. Opcja 3 wprowadza również warstwy widoków do systemu. Możesz podać to rozwagą dodatkowe, jeśli korzystasz już z widoków w architektury.
> 
> 

### <a name="examples"></a>Przykłady:
Implementowanie schematy zdefiniowane przez użytkownika na podstawie nazw bazy danych

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

Zachowanie nazwy schematu starszych wstępnie oczekujące je do nazwy tabeli. Użyj schematów dla granicy obciążenia.

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

Zachowaj nazwy starszej wersji schematu przy użyciu widoków

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
> Wszelkie zmiany w schemacie strategii musi Przegląd modelu zabezpieczeń dla bazy danych. W wielu przypadkach można uprościć modelu zabezpieczeń, przypisując uprawnienia na poziomie schematu. Jeśli wymagane są uprawnienia bardziej szczegółowego można użyć ról bazy danych.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej porad programistycznych, zobacz [omówienie tworzenia](sql-data-warehouse-overview-develop.md).

