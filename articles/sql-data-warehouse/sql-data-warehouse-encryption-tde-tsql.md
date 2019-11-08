---
title: Transparent Data Encryption (T-SQL)
description: Transparent Data Encryption (TDE) w SQL Data Warehouse (T-SQL)
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 4e7f4f31cd8b899e3fcf79568ea62830313936b9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822604"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Wprowadzenie do Transparent Data Encryption (TDE)
> [!div class="op_single_selector"]
> * [Przegląd zabezpieczeń](sql-data-warehouse-overview-manage-security.md)
> * [Uwierzytelnianie](sql-data-warehouse-authentication.md)
> * [Szyfrowanie (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Szyfrowanie (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Wymagane uprawnienia
Aby włączyć Transparent Data Encryption (TDE), musisz być administratorem lub członkiem roli dbmanager.

## <a name="enabling-encryption"></a>Włączanie szyfrowania
Wykonaj następujące kroki, aby włączyć TDE dla SQL Data Warehouse:

1. Nawiąż połączenie z bazą danych *Master* na serwerze hostującym bazę danych przy użyciu nazwy logowania będącej administratorem lub członkiem roli **DBManager** w bazie danych Master.
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Wyłączanie szyfrowania
Wykonaj następujące kroki, aby wyłączyć TDE dla SQL Data Warehouse:

1. Łączenie się z bazą danych *Master* przy użyciu nazwy logowania będącej administratorem lub członkiem roli **DBManager** w bazie danych Master
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Wstrzymywanie SQL Data Warehouse musi zostać wznowione przed wprowadzeniem zmian w ustawieniach TDE.
> 
> 

## <a name="verifying-encryption"></a>Weryfikowanie szyfrowania
Aby sprawdzić stan szyfrowania SQL Data Warehouse, wykonaj następujące czynności:

1. Łączenie się z bazą danych *Master* lub wystąpieniem przy użyciu nazwy logowania będącej administratorem lub członkiem roli **DBManager** w bazie danych Master
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Wynik ```1``` oznacza zaszyfrowaną bazę danych, ```0``` wskazuje zaszyfrowaną bazę danych.

## <a name="encryption-dmvs"></a>Szyfrowanie widoków DMV
* [sys. databases][sys.databases] 
* [sys. dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
