---
title: Włącz przezroczystego szyfrowania danych dla baza danych Stretch TSQL - Azure | Dokumentacja firmy Microsoft
description: Włącz przezroczystego szyfrowania danych (funkcji TDE) dla danych programu SQL Server Stretch na Azure TSQL
services: sql-server-stretch-database
documentationcenter: ''
author: douglaslMS
manager: jhubbard
editor: ''
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: anvang
ms.openlocfilehash: ed26c2b386e08b76f78b4a05e12c46d2b97c20f2
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23872939"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Włącz przezroczystego szyfrowania danych (funkcji TDE) dla baza danych Stretch na platformie Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Azure portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Funkcji przezroczystego szyfrowania danych (TDE) pomaga w ochronie przed zagrożeniem złośliwych działań, wykonując w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian w aplikacji.

Funkcji TDE szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego o nazwie klucza szyfrowania bazy danych. Klucz szyfrowania bazy danych jest chroniony za pomocą certyfikatu wbudowanego serwera. Certyfikat serwera wbudowanych jest unikatowy dla każdego serwera usługi Azure. Microsoft automatycznie przełącza tych certyfikatów, co najmniej co 90 dni. Ogólny opis funkcji TDE, zobacz [funkcji przezroczystego szyfrowania danych (TDE)].

## <a name="enabling-encryption"></a>Włączenie szyfrowania
Aby włączyć funkcji TDE platformy Azure bazy danych, które są przechowywane dane migracji z bazy danych programu SQL Server z obsługą odcinek, wykonaj następujące czynności:

1. Połączyć się z *wzorca* bazy danych na serwer platformy Azure obsługującym bazę danych przy użyciu nazwy logowania, która jest administrator lub członek **dbmanager:** roli w bazie danych master
2. Wykonaj następującą instrukcję do szyfrowania bazy danych.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Wyłączenie szyfrowania
Wyłączenie funkcji TDE platformy Azure bazy danych, które są przechowywane dane migracji z bazy danych programu SQL Server z obsługą odcinek, wykonaj następujące czynności:

1. Połączyć się z *wzorca* bazy danych przy użyciu nazwy logowania, która jest administrator lub członek **dbmanager:** roli w bazie danych master
2. Wykonaj następującą instrukcję do szyfrowania bazy danych.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Weryfikowanie szyfrowania
Aby sprawdzić, czy stan szyfrowania Azure bazy danych, które są przechowywane dane migracji z bazy danych programu SQL Server z obsługą odcinek, wykonaj następujące czynności:

1. Połączyć się z *wzorca* lub wystąpienie bazy danych przy użyciu nazwy logowania, która jest administrator lub członek **dbmanager:** roli w bazie danych master
2. Wykonaj następującą instrukcję do szyfrowania bazy danych.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

W wyniku ```1``` wskazuje zaszyfrowanej bazy danych, ```0``` wskazuje niezaszyfrowane bazy danych.

<!--Anchors-->
[funkcji przezroczystego szyfrowania danych (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
