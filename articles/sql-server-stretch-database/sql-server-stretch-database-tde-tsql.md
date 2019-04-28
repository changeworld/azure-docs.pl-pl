---
title: Włączanie technologii Transparent Data Encryption for Stretch Database TSQL — Azure | Dokumentacja firmy Microsoft
description: Włącz przezroczyste szyfrowanie danych (TDE) dla programu SQL Server Stretch Database w TSQL platformy Azure
services: sql-server-stretch-database
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.author: douglasl
ms.openlocfilehash: 634e1b097f2ca16a8c52da02445b3f5f2af7ff3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708825"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Włączanie technologii Transparent Data Encryption (TDE) for Stretch Database na platformie Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Przezroczyste szyfrowanie danych (TDE) ułatwia ochronę przed złośliwymi działaniami, wykonując w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian do aplikacji.

Funkcja TDE szyfruje magazyn całą bazę danych przy użyciu klucza symetrycznego o nazwie klucza szyfrowania bazy danych. Klucz szyfrowania bazy danych jest chroniona za pomocą wbudowanego serwera certyfikatu. Certyfikat wbudowanego serwera jest unikatowy dla każdego serwera usługi Azure. Microsoft automatycznie przełącza tych certyfikatów, co 90 dni. Aby uzyskać ogólny opis funkcji TDE, zobacz [Transparent Data Encryption (TDE)].

## <a name="enabling-encryption"></a>Włączanie szyfrowania
Aby włączyć funkcję TDE dla platformy Azure bazy danych, która zapisuje dane zmigrowane z obsługą usługi Stretch program SQL Server, wykonaj następujące czynności:

1. Połączyć się z *wzorca* bazy danych na serwerze usługi Azure, bazę danych przy użyciu identyfikatora logowania, który jest administratorem lub członkiem **dbmanager** roli w bazie danych master
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Wyłączenie szyfrowania
Można wyłączyć funkcję TDE dla platformy Azure bazy danych, która zapisuje dane zmigrowane z obsługą usługi Stretch program SQL Server, wykonaj następujące czynności:

1. Połączyć się z *wzorca* bazy danych przy użyciu identyfikatora logowania, który jest administratorem lub członkiem **dbmanager** roli w bazie danych master
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Weryfikowanie szyfrowania
Aby sprawdzić, czy stanu szyfrowania dla platformy Azure bazy danych, która zapisuje dane zmigrowane z obsługą usługi Stretch program SQL Server, wykonaj następujące czynności:

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

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
