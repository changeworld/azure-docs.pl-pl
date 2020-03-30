---
title: Włącz przezroczyste szyfrowanie danych dla bazy danych rozciągania (T-SQL)
description: Włącz przezroczyste szyfrowanie danych (TDE) dla bazy danych rozciągania programu SQL Server na platformie TSQL platformy Azure
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 6f1f5f55348069dbfe11b4d5857d93f8ba8c9b19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033950"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Włącz przezroczyste szyfrowanie danych (TDE) dla bazy danych stretch na platformie Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Portal Azure](sql-server-stretch-database-encryption-tde.md)
> * [Tsql](sql-server-stretch-database-tde-tsql.md)
>
>

Transparent Data Encryption (TDE) pomaga chronić przed zagrożeniem złośliwej aktywności, wykonując szyfrowanie w czasie rzeczywistym i odszyfrowywanie bazy danych, skojarzone kopie zapasowe i pliki dziennika transakcji w spoczynku bez konieczności wprowadzania zmian w aplikacji.

Technologia TDE szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego nazywanego kluczem szyfrowania bazy danych. Klucz szyfrowania bazy danych jest chroniony przez wbudowany certyfikat serwera. Wbudowany certyfikat serwera jest unikatowy dla każdego serwera platformy Azure. Firma Microsoft automatycznie obraca te certyfikaty co najmniej co 90 dni. Aby uzyskać ogólny opis TDE, zobacz [Przezroczyste szyfrowanie danych (TDE)].

## <a name="enabling-encryption"></a>Włączanie szyfrowania
Aby włączyć TDE dla bazy danych platformy Azure, która przechowuje dane zmigrowane z bazy danych programu SQL Server z włączoną funkcją Stretch, wykonaj następujące czynności:

1. Łączenie się z *główną* bazą danych na serwerze platformy Azure obsługującym bazę danych przy użyciu logowania, który jest administratorem lub członkiem roli **menedżera dbmanagera** w głównej bazie danych
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Wyłączanie szyfrowania
Aby wyłączyć TDE dla bazy danych platformy Azure, która przechowuje dane zmigrowane z bazy danych programu SQL Server z włączoną funkcją Stretch, wykonaj następujące czynności:

1. Łączenie się z *główną* bazą danych przy użyciu logowania, który jest administratorem lub członkiem roli **dbmanager** w głównej bazie danych
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Weryfikowanie szyfrowania
Aby zweryfikować stan szyfrowania bazy danych platformy Azure, która przechowuje dane zmigrowane z bazy danych programu SQL Server z włączoną funkcją Stretch, wykonaj następujące czynności:

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

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
