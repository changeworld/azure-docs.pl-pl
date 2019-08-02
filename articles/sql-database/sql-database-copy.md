---
title: Kopiowanie bazy danych Azure SQL Database | Microsoft Docs
description: Utwórz transakcyjnie spójną kopię istniejącej bazy danych Azure SQL na tym samym serwerze lub na innym serwerze.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/03/2019
ms.openlocfilehash: e9cc5aaaf11a799b17cc87b40113e166fcd93afb
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568993"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Kopiowanie spójnej transakcyjnie kopii bazy danych Azure SQL Database

Azure SQL Database oferuje kilka metod tworzenia spójnej i niefunkcjonalnej kopii istniejącej bazy danych Azure SQL Database ([pojedyncza baza danych](sql-database-single-database.md)) na tym samym serwerze lub na innym serwerze. Bazę danych SQL można skopiować za pomocą Azure Portal, PowerShell lub T-SQL. 

## <a name="overview"></a>Omówienie

Kopia bazy danych jest migawką źródłowej bazy danych w czasie żądania kopiowania. Możesz wybrać ten sam serwer lub inny serwer. Można również wybrać opcję utrzymania warstwy usługi i rozmiaru obliczeń lub użyć innego rozmiaru obliczeniowego w ramach tej samej warstwy usług (Edition). Po zakończeniu kopiowania zostanie ona w pełni funkcjonalna, niezależna baza danych. W tym momencie można go uaktualnić lub zmienić na starszą wersję. Logowania, użytkownicy i uprawnienia mogą być zarządzane niezależnie.  

> [!NOTE]
> [Automatyczne kopie zapasowe bazy danych](sql-database-automated-backups.md) są używane podczas tworzenia kopii bazy danych.

## <a name="logins-in-the-database-copy"></a>Nazwy logowania w kopii bazy danych

Podczas kopiowania bazy danych na ten sam serwer SQL Database można używać tych samych identyfikatorów logowania w obu bazach danych. Podmiot zabezpieczeń, który jest używany do kopiowania bazy danych, zostaje właścicielem bazy danych w nowej bazie danych. Wszyscy użytkownicy bazy danych, ich uprawnienia i identyfikatory zabezpieczeń (SID) są kopiowane do kopii bazy danych.  

Podczas kopiowania bazy danych na inny serwer SQL Database, podmiot zabezpieczeń na nowym serwerze stał się właścicielem bazy danych w nowej bazie danych. W przypadku korzystania z [użytkowników zawartej bazy danych](sql-database-manage-logins.md) do uzyskiwania dostępu do danych upewnij się, że zarówno podstawowa, jak i pomocnicza baza danych zawsze mają te same poświadczenia użytkownika, więc po zakończeniu kopiowania można natychmiast uzyskać do niego dostęp przy użyciu tych samych poświadczeń. 

Jeśli używasz [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), możesz całkowicie wyeliminować konieczność zarządzania poświadczeniami w kopii. Jednak podczas kopiowania bazy danych na nowy serwer dostęp oparty na logowaniu może nie zadziałał, ponieważ nazwy logowania nie istnieją na nowym serwerze. Aby dowiedzieć się więcej o zarządzaniu nazwami logowania podczas kopiowania bazy danych na inny serwer SQL Database, zobacz [jak zarządzać zabezpieczeniami usługi Azure SQL Database po awarii](sql-database-geo-replication-security-config.md). 

Po pomyślnym zakończeniu kopiowania i wcześniejszym zamapowaniu użytkowników zostanie zalogowanie się do nowej bazy danych tylko za pomocą nazwy logowania, która zainicjowała kopiowanie. Aby rozwiązać nazwy logowania po zakończeniu operacji kopiowania, zobacz temat [Rozwiązywanie logowań](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Kopiowanie bazy danych przy użyciu Azure Portal

Aby skopiować bazę danych przy użyciu Azure Portal, Otwórz stronę dla bazy danych, a następnie kliknij przycisk **Kopiuj**. 

   ![Kopia bazy danych](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Kopiowanie bazy danych przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby skopiować bazę danych za pomocą programu PowerShell, użyj polecenia cmdlet [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) . 

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Aby zapoznać się z kompletnym przykładowym skryptem, zobacz [Kopiowanie bazy danych na nowy serwer](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Kopiowanie bazy danych przy użyciu języka Transact-SQL

Zaloguj się do bazy danych Master przy użyciu nazwy logowania podmiotu zabezpieczeń na poziomie serwera lub nazwy logowania, która utworzyła bazę danych, która ma zostać skopiowana. Aby Kopiowanie bazy danych powiodło się, nazwy logowania, które nie są podmiotem zabezpieczeń na poziomie serwera, muszą należeć do roli dbmanager. Aby uzyskać więcej informacji na temat nazw logowania i łączenia się z serwerem, zobacz Zarządzanie nazwami [logowania](sql-database-manage-logins.md).

Rozpocznij kopiowanie źródłowej bazy danych za pomocą instrukcji [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . Wykonanie tej instrukcji powoduje zainicjowanie procesu kopiowania bazy danych. Ponieważ Kopiowanie bazy danych jest procesem asynchronicznym, instrukcja CREATE DATABASE zwraca przed ukończeniem kopiowania bazy danych.

### <a name="copy-a-sql-database-to-the-same-server"></a>Kopiowanie bazy danych SQL na ten sam serwer

Zaloguj się do bazy danych Master przy użyciu nazwy logowania podmiotu zabezpieczeń na poziomie serwera lub nazwy logowania, która utworzyła bazę danych, która ma zostać skopiowana. Aby Kopiowanie bazy danych powiodło się, nazwy logowania, które nie są podmiotem zabezpieczeń na poziomie serwera, muszą należeć do roli dbmanager.

To polecenie kopiuje database1 do nowej bazy danych o nazwie Database2 na tym samym serwerze. W zależności od rozmiaru bazy danych operacja kopiowania może zająć trochę czasu.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Kopiowanie bazy danych SQL na inny serwer

Zaloguj się do bazy danych Master serwera docelowego, serwera SQL Database, na którym ma zostać utworzona nowa baza danych. Użyj nazwy logowania, która ma taką samą nazwę i hasło, jak właścicielem bazy danych źródłowej bazy danych na źródłowym serwerze SQL Database. Identyfikator logowania na serwerze docelowym musi być również członkiem roli DBManager lub być nazwą logowania podmiotu zabezpieczeń na poziomie serwera.

To polecenie kopiuje database1 na serwer1 do nowej bazy danych o nazwie Database2 na serwerze serwer2. W zależności od rozmiaru bazy danych operacja kopiowania może zająć trochę czasu.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;
    
> [!IMPORTANT]
> Zapory obu serwerów muszą być skonfigurowane tak, aby zezwalały na połączenia przychodzące z adresu IP klienta wystawiającego polecenie kopiowania T-SQL.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Kopiowanie bazy danych SQL do innej subskrypcji

Możesz użyć kroków opisanych w poprzedniej sekcji, aby skopiować bazę danych na serwer SQL Database w innej subskrypcji. Upewnij się, że używasz nazwy logowania, która ma taką samą nazwę i hasło, jak właścicielem bazy danych źródłowej bazy danych, i jest członkiem roli DBManager lub jest nazwą logowania podmiotu zabezpieczeń na poziomie serwera. 

> [!NOTE]
> [Azure Portal](https://portal.azure.com) nie obsługuje kopiowania do innej subskrypcji, ponieważ Portal wywołuje interfejs API ARM i używa certyfikatów subskrypcji w celu uzyskania dostępu do obu serwerów objętych replikacją geograficzną.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>Monitoruj postęp operacji kopiowania

Monitoruj proces kopiowania, wykonując zapytania dotyczące widoków sys. databases i sys. DM _database_copies. Gdy kopiowanie jest w toku, kolumna **state_desc** widoku sys. databases dla nowej bazy danych jest ustawiona do **kopiowania**.

* Jeśli kopiowanie nie powiedzie się, kolumna **state_desc** widoku sys. databases dla nowej bazy danych jest ustawionana podejrzane. Wykonaj instrukcję DROP w nowej bazie danych i spróbuj ponownie później.
* Jeśli kopiowanie powiedzie się, kolumna **state_desc** widoku sys. databases dla nowej bazy danych jest ustawiona na **online**. Kopiowanie zostało ukończone, a nowa baza danych jest zwykłą bazą danych, która może zostać zmieniona niezależnie od źródłowej bazy danych.

> [!NOTE]
> Jeśli zdecydujesz się anulować kopiowanie w trakcie wykonywania, wykonaj instrukcję [Drop Database](https://msdn.microsoft.com/library/ms178613.aspx) w nowej bazie danych. Alternatywnie wykonanie instrukcji DROP DATABASE w źródłowej bazie danych spowoduje również anulowanie procesu kopiowania.

## <a name="resolve-logins"></a>Rozwiązywanie logowań

Gdy nowa baza danych jest w trybie online na serwerze docelowym, należy użyć instrukcji [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) , aby ponownie zamapować użytkowników z nowej bazy danych na nazwy logowania na serwerze docelowym. Aby rozwiązać użytkowników oddzielonych, zobacz [Rozwiązywanie problemów z użytkownikami](https://msdn.microsoft.com/library/ms175475.aspx)oddzielonymi. Zobacz również [jak zarządzać zabezpieczeniami usługi Azure SQL Database po odzyskiwaniu po awarii](sql-database-geo-replication-security-config.md).

Wszyscy użytkownicy w nowej bazie danych zachowują uprawnienia, które miały w źródłowej bazie danych. Użytkownik, który zainicjował kopię bazy danych, zostaje właścicielem bazy danych nowej bazy danych i ma przypisany nowy identyfikator zabezpieczeń (SID). Po pomyślnym zakończeniu kopiowania i wcześniejszym zamapowaniu użytkowników zostanie zalogowanie się do nowej bazy danych tylko za pomocą nazwy logowania, która zainicjowała kopiowanie.

Aby dowiedzieć się więcej o zarządzaniu użytkownikami i logowaniami podczas kopiowania bazy danych na inny serwer SQL Database, zobacz [jak zarządzać zabezpieczeniami usługi Azure SQL Database po awarii](sql-database-geo-replication-security-config.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać informacje na temat logowań, zobacz Zarządzanie nazwami [logowania](sql-database-manage-logins.md) i [Zarządzanie zabezpieczeniami usługi Azure SQL Database po awarii](sql-database-geo-replication-security-config.md).
* Aby wyeksportować bazę danych, zobacz [Eksportowanie bazy danych do BACPAC](sql-database-export.md).
