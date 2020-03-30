---
title: Przezroczyste szyfrowanie danych (T-SQL)
description: Przezroczyste szyfrowanie danych (TDE) w usłudze Azure Synapse Analytics (T-SQL)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 14058a91ee21bd792f9be1a64d554092c4f5aedd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351503"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Wprowadzenie do szyfrowania danych przezroczystych (TDE)
> [!div class="op_single_selector"]
> * [Omówienie zabezpieczeń](sql-data-warehouse-overview-manage-security.md)
> * [Uwierzytelnianie](sql-data-warehouse-authentication.md)
> * [Szyfrowanie (portal)](sql-data-warehouse-encryption-tde.md)
> * [Szyfrowanie (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Wymagane uprawnienia
Aby włączyć przezroczyste szyfrowanie danych (TDE), musisz być administratorem lub członkiem roli dbmanager.

## <a name="enabling-encryption"></a>Włączanie szyfrowania
Wykonaj następujące kroki, aby włączyć TDE:

1. Łączenie się z *główną* bazą danych na serwerze obsługującym bazę danych przy użyciu logowania, który jest administratorem lub członkiem roli **dbmanager** w głównej bazie danych
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Wyłączanie szyfrowania
Wykonaj następujące kroki, aby wyłączyć TDE:

1. Łączenie się z *główną* bazą danych przy użyciu logowania, który jest administratorem lub członkiem roli **dbmanager** w głównej bazie danych
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Wstrzymana pula SQL musi zostać wznowiona przed wprowadzeniem zmian w ustawieniach TDE.
> 
> 

## <a name="verifying-encryption"></a>Weryfikowanie szyfrowania
Aby zweryfikować stan szyfrowania, wykonaj poniższe czynności:

1. Łączenie się z bazą danych *głównego* lub wystąpienia przy użyciu logowania, który jest administratorem lub członkiem roli **dbmanager** w głównej bazie danych
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Wynik ```1``` wskazuje zaszyfrowaną bazę ```0``` danych, wskazuje niezaszyfrowaną bazę danych.

## <a name="encryption-dmvs"></a>Szyfrowanie dmv
* [Sys.databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
