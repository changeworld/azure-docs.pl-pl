---
title: Używanie schematów zdefiniowanych przez użytkownika
description: Porady dotyczące korzystania ze schematów zdefiniowanych przez użytkownika w języku T-SQL w Azure SQL Data Warehouse na potrzeby tworzenia rozwiązań.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 697bffa36e9b208c1a027654df81fb356ddfc8ed
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685802"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Używanie schematów zdefiniowanych przez użytkownika w SQL Data Warehouse
Porady dotyczące korzystania ze schematów zdefiniowanych przez użytkownika w języku T-SQL w Azure SQL Data Warehouse na potrzeby tworzenia rozwiązań.

## <a name="schemas-for-application-boundaries"></a>Schematy granic aplikacji

Tradycyjne magazyny danych często używają osobnych baz danych do tworzenia granic aplikacji na podstawie obciążenia, domeny lub zabezpieczeń. Na przykład tradycyjny magazyn danych SQL Server może obejmować przemieszczanie bazy danych, bazy danych magazynu danych i niektórych baz danych składnicy danych. W tej topologii każda baza danych działa jako granica obciążenia i zabezpieczeń w architekturze.

Z kolei SQL Data Warehouse uruchamia całe obciążenie magazynu danych w jednej bazie danych. Sprzężenia między bazami danych są niedozwolone. W związku z tym SQL Data Warehouse oczekuje, że wszystkie tabele używane przez magazyn będą przechowywane w jednej bazie danych.

> [!NOTE]
> SQL Data Warehouse nie obsługuje zapytań między bazami danych dowolnego rodzaju. W związku z tym należy skorygować implementacje magazynu danych korzystające z tego wzorca.
> 
> 

## <a name="recommendations"></a>Polecane elementy
Są to zalecenia dotyczące konsolidowania obciążeń, zabezpieczeń, domeny i granic funkcjonalnych za pomocą schematów zdefiniowanych przez użytkownika

1. Używanie jednej SQL Data Warehouse Database do uruchamiania całego obciążenia magazynu danych
2. Konsolidowanie istniejącego środowiska magazynu danych w celu korzystania z jednej SQL Data Warehouse bazy danych
3. Korzystaj ze **schematów zdefiniowanych przez użytkownika** , aby zapewnić granicę zaimplementowaną wcześniej przy użyciu baz danych.

Jeśli schematy zdefiniowane przez użytkownika nie były wcześniej używane, masz czyste miejsce. Po prostu Użyj starej nazwy bazy danych jako podstawy dla schematów zdefiniowanych przez użytkownika w bazie danych SQL Data Warehouse.

Jeśli schematy zostały już użyte, możesz skorzystać z kilku opcji:

1. Usuń starsze nazwy schematów i zacznij od nowa
2. Zachowaj starsze nazwy schematów, przede wszystkim nazwami starszego schematu i nazwą tabeli
3. Zachowaj starsze nazwy schematów, implementując widoki w tabeli w dodatkowym schemacie, aby ponownie utworzyć starą strukturę schematu.

> [!NOTE]
> Przy pierwszej inspekcji Opcja 3 może wyglądać jak najbardziej atrakcyjny opcję. Devil jest jednak szczegółowy. Widoki są odczytywane tylko w SQL Data Warehouse. Wszelkie modyfikacje danych lub tabel należy wykonać w odniesieniu do tabeli podstawowej. Opcja 3 wprowadza również warstwę widoków w systemie. Jeśli używasz już widoków w danej architekturze, możesz zadawać to kilka dodatkowych myśli.
> 
> 

### <a name="examples"></a>Przykłady:
Implementowanie schematów zdefiniowanych przez użytkownika na podstawie nazw baz danych

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

Zachowaj starsze nazwy schematów przed zaczekaniem ich na nazwę tabeli. Użyj schematów dla granicy obciążenia.

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

Zachowaj starsze nazwy schematów przy użyciu widoków

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
> Wszelkie zmiany strategii schematu wymagają przeglądu modelu zabezpieczeń bazy danych. W wielu przypadkach może być możliwe uproszczenie modelu zabezpieczeń przez przypisanie uprawnień na poziomie schematu. Jeśli wymagane są bardziej szczegółowe uprawnienia, można użyć ról bazy danych.
> 
> 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](sql-data-warehouse-overview-develop.md).

