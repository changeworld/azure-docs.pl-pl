---
title: Korzystanie ze schematów zdefiniowanych przez użytkownika
description: Porady dotyczące używania schematów zdefiniowanych przez użytkownika T-SQL w usłudze Azure SQL Data Warehouse do tworzenia rozwiązań.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a9ed4f01aae6ace1af6c1652fe3c5ecfe14dc6bf
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351544"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Używanie schematów zdefiniowanych przez użytkownika w magazynie danych SQL
Porady dotyczące używania schematów zdefiniowanych przez użytkownika T-SQL w usłudze Azure SQL Data Warehouse do tworzenia rozwiązań.

## <a name="schemas-for-application-boundaries"></a>Schematy dla granic aplikacji

Tradycyjne magazyny danych często używają oddzielnych baz danych do tworzenia granic aplikacji na podstawie obciążenia, domeny lub zabezpieczeń. Na przykład tradycyjny magazyn danych programu SQL Server może zawierać tymczasową bazę danych, bazę danych magazynu danych i niektóre bazy danych składnicy danych. W tej topologii każda baza danych działa jako obwiednia obciążenia i zabezpieczeń w architekturze.

Z drugiej strony usługa SQL Data Warehouse uruchamia całe obciążenie magazynu danych w jednej bazie danych. Sprzężenia między bazami danych nie są dozwolone. W związku z tym SQL Data Warehouse oczekuje, że wszystkie tabele używane przez magazyn mają być przechowywane w jednej bazie danych.

> [!NOTE]
> Usługa SQL Data Warehouse nie obsługuje zapytań między bazami danych dowolnego rodzaju. W związku z tym implementacje magazynu danych, które wykorzystują ten wzorzec, będą musiały zostać zmienione.
> 
> 

## <a name="recommendations"></a>Zalecenia
Są to zalecenia dotyczące konsolidacji obciążeń, zabezpieczeń, domeny i granic funkcjonalnych przy użyciu schematów zdefiniowanych przez użytkownika

1. Uruchamianie całego obciążenia magazynu danych za pomocą jednej bazy danych SQL Data Warehouse
2. Konsolidacja istniejącego środowiska magazynu danych w celu użycia jednej bazy danych usługi SQL Data Warehouse
3. Wykorzystaj **schematy zdefiniowane przez użytkownika,** aby zapewnić granicę wcześniej zaimplementowane przy użyciu baz danych.

Jeśli schematy zdefiniowane przez użytkownika nie były używane wcześniej, masz czystą planszę. Wystarczy użyć starej nazwy bazy danych jako podstawy dla schematów zdefiniowanych przez użytkownika w bazie danych usługi SQL Data Warehouse.

Jeśli schematy zostały już użyte, masz kilka opcji:

1. Usuwanie nazw starszych schematów i rozpoczynanie od nowa
2. Zachowaj starsze nazwy schematów przez wstępnie oczekujące na nazwę starszego schematu do nazwy tabeli
3. Zachowaj starsze nazwy schematu, implementując widoki nad tabelą w dodatkowym schemacie, aby odtworzyć starą strukturę schematu.

> [!NOTE]
> Przy pierwszej inspekcji opcja 3 może wydawać się najbardziej atrakcyjną opcją. Jednak diabeł tkwi w szczegółach. Widoki są odczytywane tylko w magazynie danych SQL. Wszelkie dane lub modyfikacji tabeli należy wykonać względem tabeli bazowej. Opcja 3 wprowadza również warstwę widoków do systemu. Możesz chcieć nadać tej dodatkowej myśli, jeśli używasz już widoków w architekturze.
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

Zachowaj starsze nazwy schematów przez wstępnie oczekujące je do nazwy tabeli. Użyj schematów dla granicy obciążenia.

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

Zachowywanie nazw starszych schematów przy użyciu widoków

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
> Wszelkie zmiany w strategii schematu wymaga przeglądu modelu zabezpieczeń dla bazy danych. W wielu przypadkach można uprościć model zabezpieczeń, przypisując uprawnienia na poziomie schematu. Jeśli wymagane są bardziej szczegółowe uprawnienia, można użyć ról bazy danych.
> 
> 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](sql-data-warehouse-overview-develop.md).

