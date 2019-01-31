---
title: Przezroczyste szyfrowanie danych w magazynie danych SQL (T-SQL) | Dokumentacja firmy Microsoft
description: Transparent Data Encryption (TDE) w magazynie danych SQL (T-SQL)
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 50b6077687da2ed24a3a90b227d129c43f70bfc9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465813"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Rozpoczynanie pracy z przezroczystego szyfrowania danych (TDE)
> [!div class="op_single_selector"]
> * [Omówienie zabezpieczeń](sql-data-warehouse-overview-manage-security.md)
> * [Uwierzytelnianie](sql-data-warehouse-authentication.md)
> * [Szyfrowanie (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Encryption (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permssions"></a>Wymagane uprawnienia
Aby włączyć przezroczystego szyfrowania danych (TDE), musi być administratorem lub członkiem roli dbmanager.

## <a name="enabling-encryption"></a>Włączanie szyfrowania
Wykonaj następujące kroki, aby włączyć funkcję TDE dla usługi SQL Data Warehouse:

1. Połączyć się z *wzorca* bazy danych na serwerze hostującym bazę danych przy użyciu identyfikatora logowania, który jest administratorem lub członkiem **dbmanager** roli w bazie danych master
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Wyłączenie szyfrowania
Wykonaj następujące kroki, aby wyłączyć funkcję TDE dla usługi SQL Data Warehouse:

1. Połączyć się z *wzorca* bazy danych przy użyciu identyfikatora logowania, który jest administratorem lub członkiem **dbmanager** roli w bazie danych master
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Wstrzymanej usłudze SQL Data Warehouse musi zostać wznowiony przed wprowadzeniem zmian w ustawieniach funkcji TDE.
> 
> 

## <a name="verifying-encryption"></a>Weryfikowanie szyfrowania
Aby sprawdzić, czy stan szyfrowania usłudze SQL Data Warehouse, wykonaj następujące czynności:

1. Połączyć się z *wzorca* lub wystąpienie bazy danych przy użyciu identyfikatora logowania, który jest administratorem lub członkiem **dbmanager** roli w bazie danych master
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Wynikiem ```1``` wskazuje szyfrowanej bazy danych, ```0``` wskazuje niezaszyfrowane bazy danych.

## <a name="encryption-dmvs"></a>Szyfrowanie dynamiczne widoki zarządzania
* [sys.databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
