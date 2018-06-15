---
title: Kopiowanie bazy danych Azure SQL | Dokumentacja firmy Microsoft
description: Utwórz spójna transakcyjnie kopię istniejącej bazy danych Azure SQL na tym samym serwerze lub inny serwer.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.date: 04/01/2018
ms.author: carlrab
ms.topic: conceptual
ms.openlocfilehash: 2217df046cf95ddcd12f6dcaa41b2c3f8b0090f6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646205"
---
# <a name="copy-an-azure-sql-database"></a>Kopiowanie bazy danych Azure SQL

Baza danych SQL Azure zapewnia kilka metod tworzenia spójna transakcyjnie kopię istniejącej bazy danych Azure SQL na tym samym serwerze lub inny serwer. Możesz skopiować bazę danych SQL za pomocą portalu Azure, programu PowerShell lub T-SQL. 

## <a name="overview"></a>Przegląd

Kopiowanie bazy danych jest migawką źródłowej bazy danych od chwili żądanie kopii. Możesz wybrać tego samego serwera lub inny serwer, jego warstwy usług i poziom wydajności lub poziomu wydajności różnych w ramach tej samej warstwie usług (wydanie). Po zakończeniu kopiowania staje się funkcjonalnej, niezależnie od bazy danych. W tym momencie możesz uaktualnić lub obniżyć go do dowolnej wersji. Nazwy logowania, użytkowników i uprawnień odbywa się niezależnie.  

## <a name="logins-in-the-database-copy"></a>Nazwy logowania w kopii bazy danych

Podczas kopiowania bazy danych na tym samym serwerze logicznym, można tej samej nazwy logowania w obu bazach danych. Służy do kopiowania bazy danych podmiot zabezpieczeń staje się właścicielem bazy danych na nowej bazy danych. Wszyscy użytkownicy bazy danych, ich uprawnienia i ich identyfikatorów zabezpieczeń (SID) są kopiowane do kopii bazy danych.  

Podczas kopiowania bazy danych na inny serwer logiczny, na nowym serwerze podmiot zabezpieczeń staje się właścicielem bazy danych na nowej bazy danych. Jeśli używasz [zawarte bazy danych użytkowników](sql-database-manage-logins.md) dla dostępu do danych, sprawdź, czy podstawowe i pomocnicze bazy danych zawsze mają tych samych poświadczeń użytkownika, tak aby po zakończeniu kopiowania należy bezpośrednio do niego dostęp z tych samych poświadczeń. 

Jeśli używasz [usługi Azure Active Directory](../active-directory/active-directory-whatis.md), można całkowicie wyeliminować potrzebę zarządzania poświadczeniami w kopii. Jednak podczas kopiowania bazy danych do nowego serwera, dostępu opartego na nazwie logowania mogą nie działać, ponieważ dane logowania nie istnieje na nowym serwerze. Aby dowiedzieć się więcej o zarządzaniu nazwy logowania podczas kopiowania bazy danych na inny serwer logiczny, zobacz [jak zarządzać zabezpieczeń bazy danych Azure SQL po awarii](sql-database-geo-replication-security-config.md). 

Po kopiowanie powiedzie się i przed są mapowane ponownie innych użytkowników, logowania, który zainicjował kopiowania, właściciel bazy danych może zalogować się do nowej bazy danych. Aby rozwiązać nazwy logowania, po zakończeniu operacji kopiowania, zobacz [rozwiązać logowania](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Kopiowanie bazy danych przy użyciu portalu Azure

Aby skopiować bazę danych przy użyciu portalu Azure, otwórz stronę bazy danych, a następnie kliknij przycisk **kopiowania**. 

   ![Kopiowanie bazy danych](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Kopiowanie bazy danych przy użyciu programu PowerShell

Aby skopiować bazę danych przy użyciu programu PowerShell, użyj [AzureRmSqlDatabaseCopy nowy](/powershell/module/azurerm.sql/new-azurermsqldatabasecopy) polecenia cmdlet. 

```PowerShell
New-AzureRmSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Kompletnego przykładowego skryptu, zobacz [skopiować bazę danych do nowego serwera](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Kopiowanie bazy danych przy użyciu języka Transact-SQL

Zaloguj się bazie danych master z głównego identyfikatora logowania poziomu serwera lub nazwy logowania, który utworzył bazę danych, którą chcesz skopiować. Kopiowania bazy danych powiodło się, nazwy logowania nie będących principal poziomu serwera muszą być członkami roli dbmanager:. Aby uzyskać więcej informacji na temat logowania i łączenia się z serwerem, zobacz [Zarządzanie logowania](sql-database-manage-logins.md).

Uruchom kopiowanie źródłowa baza danych o [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) instrukcji. Wykonywanie tej instrukcji inicjuje proces kopiowania bazy danych. Wykonywanie instrukcji CREATE DATABASE kopiowania bazy danych jest proces asynchroniczny, zwraca przed ukończeniem kopiowania bazy danych.

### <a name="copy-a-sql-database-to-the-same-server"></a>Skopiuj do tego samego serwera bazy danych SQL
Zaloguj się bazie danych master z głównego identyfikatora logowania poziomu serwera lub nazwy logowania, który utworzył bazę danych, którą chcesz skopiować. Kopiowania bazy danych powiodło się, nazwy logowania nie będących principal poziomu serwera muszą być członkami roli dbmanager:.

To polecenie powoduje skopiowanie Database1 nową bazę danych o nazwie Database2 na tym samym serwerze. W zależności od rozmiaru bazy danych operacji kopiowania może zająć trochę czasu.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Kopiowanie bazy danych SQL do innego serwera

Zaloguj się bazie danych master serwera docelowego, serwer bazy danych SQL, na którym ma być tworzony nowej bazy danych. Użyj nazwy logowania, która ma taką samą nazwę i hasło jako właściciel bazy danych na serwerze bazy danych SQL źródła źródłowej bazy danych. Logowanie na serwerze docelowym również musi być członkiem roli dbmanager: lub być głównego identyfikatora logowania poziomu serwera.

To polecenie powoduje skopiowanie Database1 na serwerze1 na nową bazę danych o nazwie Database2 na serwer2. W zależności od rozmiaru bazy danych operacji kopiowania może zająć trochę czasu.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;


### <a name="monitor-the-progress-of-the-copying-operation"></a>Monitorowanie postępu operacji kopiowania

Sys.databases i sys.dm_database_copies widoki zapytań, aby monitorować proces kopiowania. Podczas kopiowania jest w toku, **state_desc** kolumny widoku sys.databases nowej bazy danych ustawiono **kopiowanie**.

* Jeśli kopiowanie nie powiedzie się, **state_desc** kolumny widoku sys.databases nowej bazy danych ustawiono **PODEJRZEWAĆ**. Wykonaj instrukcja DROP na nową bazę danych i spróbuj ponownie później.
* Jeśli kopiowanie zakończy się powodzeniem, **state_desc** kolumny widoku sys.databases nowej bazy danych ustawiono **ONLINE**. Kopiowanie została zakończona, a nowa baza danych jest zwykłej bazy danych, które można zmienić niezależnie od źródłowej bazy danych.

> [!NOTE]
> Jeśli zdecydujesz się anulować kopiowanie, gdy jest w toku, wykonaj [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) instrukcją w nowej bazy danych. Alternatywnie wykonywania instrukcji DROP DATABASE na źródłowej bazy danych również przerywa proces kopiowania.
> 

## <a name="resolve-logins"></a>Rozwiąż logowania

Po nowej bazy danych jest w trybie online na serwerze docelowym, za pomocą [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) instrukcji, aby ponownie zamapować użytkownikom z nową bazę danych do nazwy logowania na serwerze docelowym. Aby rozwiązać problem użytkowników osieroconych, zobacz [Rozwiązywanie problemów z osieroconych użytkowników](https://msdn.microsoft.com/library/ms175475.aspx). Zobacz też [jak zarządzać zabezpieczeń bazy danych Azure SQL po awarii](sql-database-geo-replication-security-config.md).

Wszyscy użytkownicy w nowej bazy danych zachować uprawnienia, które miały źródłowej bazy danych. Użytkownik, który zainicjował kopii bazy danych, staje się właścicielem bazy danych nowej bazy danych i ma przypisany nowy identyfikator zabezpieczeń (SID). Po kopiowanie powiedzie się i przed są mapowane ponownie innych użytkowników, logowania, który zainicjował kopiowania, właściciel bazy danych może zalogować się do nowej bazy danych.

Informacje na temat zarządzania użytkownikami i logowania do kopiowania bazy danych na inny serwer logiczny, zobacz [jak zarządzać zabezpieczeń bazy danych Azure SQL po awarii](sql-database-geo-replication-security-config.md).

## <a name="next-steps"></a>Kolejne kroki

* Informacje logowania, zobacz [Zarządzanie logowania](sql-database-manage-logins.md) i [jak zarządzać zabezpieczeń bazy danych Azure SQL po awarii](sql-database-geo-replication-security-config.md).
* Aby wyeksportować bazę danych, zobacz [wyeksportować do pliku BACPAC bazy danych](sql-database-export.md).
