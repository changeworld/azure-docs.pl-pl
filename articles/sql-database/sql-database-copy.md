---
title: Kopiuj bazę danych Azure SQL database | Dokumentacja firmy Microsoft
description: Na tym samym serwerze lub na innym serwerze, należy utworzyć spójnej transakcyjnie kopii istniejącej bazy danych Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sahsan
ms.reviewer: carlrab
manager: craigg
ms.date: 06/03/2019
ms.openlocfilehash: ba787ccd51bf5ea9ec8e06e341a0f698afb5a7e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66688075"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Skopiuj spójnej transakcyjnie kopii bazy danych Azure SQL

Usługa Azure SQL Database udostępnia kilka metod do tworzenia spójnej transakcyjnie kopii istniejącej bazy danych Azure SQL na tym samym serwerze lub na innym serwerze. Możesz skopiować bazę danych SQL za pomocą witryny Azure portal, programu PowerShell lub języka T-SQL. 

## <a name="overview"></a>Omówienie

Kopiowanie bazy danych jest migawką źródłowej bazy danych od czasu żądanie kopii. Można wybrać tego samego serwera lub innego serwera. Ponadto można zachować swojej warstwy usług i obliczenia rozmiaru lub użycie rozmiaru obliczeniowej w ramach tej samej warstwy usługi (wydanie). Po zakończeniu kopiowania staje się ona w pełni funkcjonalne, niezależnie od bazy danych. W tym momencie można uaktualnić lub obniżyć jej do dowolnej wersji. Identyfikatory logowania, użytkownikami i uprawnieniami mogą być zarządzane niezależnie.  

> [!NOTE]
> [Automatyczne kopie zapasowe bazy danych](sql-database-automated-backups.md) są używane podczas tworzenia kopii bazy danych.

## <a name="logins-in-the-database-copy"></a>Nazwy logowania w kopii bazy danych

Podczas kopiowania bazy danych do tego samego serwera bazy danych SQL, tego samego logowania może służyć w obu bazach danych. Podmiot, służy do kopiowania bazy danych zabezpieczeń staje się właścicielem bazy danych na nowej bazy danych. Wszyscy użytkownicy bazy danych, uprawnienia i ich identyfikatory zabezpieczeń (SID) są kopiowane do kopii bazy danych.  

Podczas kopiowania bazy danych na inny serwer bazy danych SQL, podmiot na nowym serwerze zabezpieczeń staje się właścicielem bazy danych na nowej bazy danych. Jeśli używasz [zawartych użytkowników bazy danych](sql-database-manage-logins.md) dostępu do danych, upewnij się, że podstawowe i pomocnicze bazy danych zawsze używały tych samych poświadczeń użytkownika, aby po zakończeniu kopiowania możesz od razu do niego dostęp przy użyciu tych samych poświadczeń . 

Jeśli używasz [usługi Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), możesz całkowicie wyeliminować potrzebę zarządzania poświadczeniami w kopii. Jednak podczas kopiowania bazy danych do nowego serwera, dostępu opartego na nazwie logowania mogą nie działać, ponieważ dane logowania nie istnieje na nowym serwerze. Aby dowiedzieć się więcej o zarządzaniu nazwy logowania podczas kopiowania bazy danych na inny serwer bazy danych SQL, zobacz [jak zarządzanie zabezpieczeniami bazy danych Azure SQL po odzyskaniu po awarii](sql-database-geo-replication-security-config.md). 

Po pomyślnym kopiowanie i przed innymi użytkownikami są mapowane ponownie, logowania, która zainicjowała, kopiowanie, właściciel bazy danych może zalogować się do nowej bazy danych. Aby rozwiązać nazwy logowania, po zakończeniu operacji kopiowania, zobacz [rozwiązać logowania](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Kopiowanie bazy danych przy użyciu witryny Azure portal

Aby skopiować bazę danych przy użyciu witryny Azure portal, otwórz stronę bazy danych, a następnie kliknij przycisk **kopiowania**. 

   ![Kopiowanie bazy danych](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Kopiowanie bazy danych przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby skopiować bazę danych przy użyciu programu PowerShell, należy użyć [New AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) polecenia cmdlet. 

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Aby uzyskać kompletny przykładowy skrypt, zobacz [kopiowanie bazy danych na nowy serwer](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Kopiowanie bazy danych przy użyciu języka Transact-SQL

Zaloguj się w bazie danych master przy użyciu głównego identyfikatora logowania poziomu serwera lub logowania, który utworzył bazę danych, które mają zostać skopiowane. Do kopiowania bazy danych zakończyło się sukcesem, logowania, które nie są jednostki poziomu serwera muszą być elementami członkowskimi roli dbmanager. Aby uzyskać więcej informacji dotyczących logowania i łączenia z serwerem, zobacz [zarządzanie danymi logowania](sql-database-manage-logins.md).

Rozpocznij kopiowanie źródłowa baza danych o [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) instrukcji. Wykonywanie tej instrukcji inicjuje proces kopiowania bazy danych. Kopiowanie bazy danych jest proces asynchroniczny, instrukcji CREATE DATABASE zwraca przed ukończeniem kopiowania bazy danych.

### <a name="copy-a-sql-database-to-the-same-server"></a>Kopiowanie bazy danych SQL na tym samym serwerze

Zaloguj się w bazie danych master przy użyciu głównego identyfikatora logowania poziomu serwera lub logowania, który utworzył bazę danych, które mają zostać skopiowane. Do kopiowania bazy danych zakończyło się sukcesem, logowania, które nie są jednostki poziomu serwera muszą być elementami członkowskimi roli dbmanager.

To polecenie kopiuje bazadanych1 do nowej bazy danych o nazwie bazy danych 2 na tym samym serwerze. W zależności od rozmiaru bazy danych operacji kopiowania może zająć trochę czasu.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Kopiowanie bazy danych SQL na innym serwerze

Zaloguj się z główną bazą danych serwera docelowego, serwera bazy danych SQL, gdzie nowa baza danych ma zostać utworzony. Użyj nazwy logowania, która ma taką samą nazwę i hasło jako właściciel bazy danych źródłowej bazy danych na źródłowym serwerze bazy danych SQL. Zaloguj się na serwerze docelowym musi również należeć do roli dbmanager lub być głównego identyfikatora logowania poziomu serwera.

To polecenie kopiuje bazadanych1 na serwerze1 do nowej bazy danych o nazwie bazy danych 2 na serwerze Serwer2. W zależności od rozmiaru bazy danych operacji kopiowania może zająć trochę czasu.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;
    
> [!IMPORTANT]
> Oba serwery zapory muszą skonfigurowane w taki sposób, aby zezwolić na połączenia przychodzące z adresu IP klienta, wydanie polecenia języka T-SQL kopii.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Kopiowanie bazy danych SQL do innej subskrypcji

Descrbed kroki opisane w poprzedniej sekcji służy do kopiowania bazy danych na serwerze bazy danych SQL w innej subskrypcji. Upewnij się, użyj nazwy logowania, która ma taką samą nazwę i hasło jako właściciel bazy danych źródłowej bazy danych i jest członkiem roli dbmanager lub jest głównego identyfikatora logowania poziomu serwera. 

> [!NOTE]
> [Witryny Azure portal](https://portal.azure.com) nie obsługuje kopiowania do innej subskrypcji, ponieważ Portal wywołuje interfejs API ARM i używa certyfikatów subskrypcji dostępu do obu serwerów, które są związane z replikacją geograficzną.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>Monitoruj postęp operacji kopiowania

Monitorowanie procesu kopiowania, badając sys.databases i sys.dm_database_copies widoki. Podczas kopiowania jest w toku, **state_desc** kolumny widoku sys.databases dla nowej bazy danych ustawiono **kopiowanie**.

* Jeśli kopiowanie nie powiedzie się, **state_desc** kolumny widoku sys.databases dla nowej bazy danych ustawiono **PODEJRZENIE**. Wykonaj instrukcję listy na nową bazę danych, a następnie spróbuj ponownie później.
* Jeśli kopiowanie zakończy się powodzeniem, **state_desc** kolumny widoku sys.databases dla nowej bazy danych ustawiono **ONLINE**. Kopiowanie zostało ukończone, a nowa baza danych jest zwykłej bazy danych, które można zmienić niezależne od źródłowej bazy danych.

> [!NOTE]
> Jeśli zechcesz anulować, kopiując je w trakcie wykonywania [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) instrukcji na nową bazę danych. Alternatywnie wykonywania instrukcji DROP DATABASE źródłowej bazy danych również przerywa proces kopiowania.

## <a name="resolve-logins"></a>Rozwiąż logowania

Po Nowa baza danych jest w trybie online, na serwerze docelowym, użyj [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) instrukcję, aby ponownie zamapować użytkowników z nową bazę danych do nazwy logowania na serwerze docelowym. Aby rozwiązać problem użytkowników osieroconych, zobacz [Rozwiązywanie problemów użytkowników oddzielonych](https://msdn.microsoft.com/library/ms175475.aspx). Zobacz też [jak zarządzanie zabezpieczeniami bazy danych Azure SQL po odzyskaniu po awarii](sql-database-geo-replication-security-config.md).

Wszyscy użytkownicy w nowej bazy danych zachowuje uprawnienia, które miały do źródłowej bazy danych. Użytkownik, który zainicjował kopii bazy danych staje się właścicielem bazy danych w nowej bazy danych i ma przypisany nowy identyfikator zabezpieczeń (SID). Po pomyślnym kopiowanie i przed innymi użytkownikami są mapowane ponownie, logowania, która zainicjowała, kopiowanie, właściciel bazy danych może zalogować się do nowej bazy danych.

Aby dowiedzieć się więcej o zarządzaniu użytkownicy i logowania podczas kopiowania bazy danych na inny serwer bazy danych SQL, zobacz [jak zarządzanie zabezpieczeniami bazy danych Azure SQL po odzyskaniu po awarii](sql-database-geo-replication-security-config.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać informacji na temat nazw logowania, zobacz [zarządzanie danymi logowania](sql-database-manage-logins.md) i [jak zarządzanie zabezpieczeniami bazy danych Azure SQL po odzyskaniu po awarii](sql-database-geo-replication-security-config.md).
* Aby wyeksportować bazę danych, zobacz [eksportowania bazy danych do pliku BACPAC](sql-database-export.md).
