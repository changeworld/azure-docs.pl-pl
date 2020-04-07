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
ms.openlocfilehash: ae751cc5b8e3ab67f3e65757724d0ebae1c45e02
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745247"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Wprowadzenie do szyfrowania danych przezroczystych (TDE)

> [!div class="op_single_selector"]
>
> * [Omówienie zabezpieczeń](sql-data-warehouse-overview-manage-security.md)
> * [Authentication](sql-data-warehouse-authentication.md)
> * [Szyfrowanie (portal)](sql-data-warehouse-encryption-tde.md)
> * [Szyfrowanie (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

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

* [Sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
