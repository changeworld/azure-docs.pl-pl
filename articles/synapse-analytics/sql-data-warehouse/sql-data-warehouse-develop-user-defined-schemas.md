---
title: Korzystanie ze schematów zdefiniowanych przez użytkownika
description: Porady dotyczące używania schematów zdefiniowanych przez użytkownika T-SQL do opracowywania rozwiązań w puli SQL Synapse.
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
ms.openlocfilehash: 7144fa75d156ca7aed9d8215592f89c167cfb221
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633466"
---
# <a name="user-defined-schemas-in-synapse-sql-pool"></a>Schematy zdefiniowane przez użytkownika w puli SQL Synapse
W tym artykule koncentruje się na dostarczaniu kilku wskazówek dotyczących korzystania z schematów zdefiniowanych przez użytkownika T-SQL do opracowywania rozwiązań w puli SQL Synapse.

## <a name="schemas-for-application-boundaries"></a>Schematy dla granic aplikacji

Tradycyjne magazyny danych często używają oddzielnych baz danych do tworzenia granic aplikacji na podstawie obciążenia, domeny lub zabezpieczeń. 

Na przykład tradycyjny magazyn danych programu SQL Server może zawierać tymczasową bazę danych, bazę danych magazynu danych i niektóre bazy danych składnicy danych. W tej topologii każda baza danych działa jako obwiednia obciążenia i zabezpieczeń w architekturze.

Z drugiej strony pula SQL uruchamia całe obciążenie magazynu danych w jednej bazie danych. Sprzężenia między bazami danych nie są dozwolone. Pula SQL oczekuje, że wszystkie tabele używane przez magazyn mają być przechowywane w jednej bazie danych.

> [!NOTE]
> Pula SQL nie obsługuje zapytań między bazami danych dowolnego rodzaju. W związku z tym implementacje magazynu danych, które wykorzystują ten wzorzec, będą musiały zostać zmienione.
> 
> 

## <a name="recommendations"></a>Zalecenia
Poniżej przedstawiono zalecenia dotyczące konsolidacji obciążeń, zabezpieczeń, domeny i granic funkcjonalnych przy użyciu schematów zdefiniowanych przez użytkownika:

- Użyj jednej bazy danych puli SQL, aby uruchomić całe obciążenie magazynu danych.
- Skonsoliduj istniejące środowisko magazynu danych, aby użyć jednej bazy danych puli SQL.
- Wykorzystaj **schematy zdefiniowane przez użytkownika,** aby zapewnić granicę wcześniej zaimplementowane przy użyciu baz danych.

Jeśli schematy zdefiniowane przez użytkownika nie były używane wcześniej, masz czystą planszę. Użyj starej nazwy bazy danych jako podstawy dla schematów zdefiniowanych przez użytkownika w bazie danych puli SQL.

Jeśli schematy zostały już użyte, masz kilka opcji:

- Usuń nazwy schematów starszych i zacznij od nowa.
- Zachowaj starsze nazwy schematu przez wstępnie oczekujące na nazwę starszego schematu do nazwy tabeli.
- Zachowaj starsze nazwy schematu, implementując widoki nad tabelą w dodatkowym schemacie, aby odtworzyć starą strukturę schematu.

> [!NOTE]
> Przy pierwszej inspekcji opcja 3 może wydawać się najbardziej atrakcyjną opcją. Jednak diabeł tkwi w szczegółach. Widoki są odczytywane tylko w puli SQL. Wszelkie dane lub modyfikacji tabeli należy wykonać względem tabeli bazowej. Opcja 3 wprowadza również warstwę widoków do systemu. Możesz chcieć nadać tej dodatkowej myśli, jeśli używasz już widoków w architekturze.
> 
> 

### <a name="examples"></a>Przykłady:
Zaimplementuj schematy zdefiniowane przez użytkownika na podstawie nazw baz danych:

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

Zachowaj starsze nazwy schematów przez wstępnie oczekujące je do nazwy tabeli. Użyj schematów dla granicy obciążenia:

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

Zachowaj starsze nazwy schematów przy użyciu widoków:

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

