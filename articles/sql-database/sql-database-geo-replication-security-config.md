---
title: Konfigurowanie zabezpieczeń bazy danych SQL Azure dla odzyskiwania po awarii | Dokumentacja firmy Microsoft
description: Dowiedz się zagadnienia dotyczące konfigurowania i zarządzania nimi zabezpieczeń po Przywracanie bazy danych lub pracy awaryjnej na pomocniczym serwerze zabezpieczeń.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sashan
ms.openlocfilehash: 0796e5900bc67d93e51a0ce377ef5d1144346e2c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646868"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Konfigurowanie i zarządzanie nimi zabezpieczenia bazy danych SQL Azure geograficzne lub trybu failover 

W tym temacie opisano wymagania dotyczące uwierzytelniania można skonfigurować i sterować [aktywna replikacja geograficzna](sql-database-geo-replication-overview.md) i kroki wymagane do skonfigurowania użytkownikowi dostęp do pomocniczej bazy danych. Opisuje również sposób włączania dostępu do odzyskanej bazy danych po użyciu [geograficzne](sql-database-recovery-using-backups.md#geo-restore). Aby uzyskać więcej informacji na temat opcji odzyskiwania, zobacz [omówienie ciągłości działalności biznesowej](sql-database-business-continuity.md).

> [!NOTE]
> [Aktywna replikacja geograficzna](sql-database-geo-replication-overview.md) jest teraz dostępna dla wszystkich baz danych w warstwach wszystkich usług.
>  

## <a name="disaster-recovery-with-contained-users"></a>Odzyskiwanie po awarii z zawartych użytkowników
W przeciwieństwie do tradycyjnych użytkowników, którzy muszą być zamapowane na nazwy logowania w bazie danych master, zawartego użytkownika zarządza całkowicie sama baza danych. Ma to dwie korzyści. W przypadku odzyskiwania po awarii użytkownicy mogą w dalszym ciągu łączenia z bazą danych nowego podstawowego lub odzyskać bazy danych przy użyciu przywracaniem geograficznym bez przeprowadzania dodatkowej konfiguracji, ponieważ w bazie danych zarządza użytkownikami. Istnieją również potencjalne skalowalności i wydajności korzysta z tej konfiguracji z punktu widzenia logowania. Aby uzyskać więcej informacji, zobacz artykuł [Contained Database Users - Making Your Database Portable](https://msdn.microsoft.com/library/ff929188.aspx) (Użytkownicy zawartej bazy danych — tworzenie przenośnej bazy danych). 

Optymalne rozwiązania w zakresie głównym jest zarządzanie procesem odzyskiwania po awarii na dużą skalę trudniejsze. Jeśli masz wiele baz danych, które używają tej samej nazwy logowania, obsługa poświadczeń przy użyciu zawartych użytkowników w wielu baz danych może odwrócić zalet zawartych użytkowników. Na przykład zasady haseł obrotu wymaga czy zmian stale w wielu baz danych zamiast zmiany hasła podczas logowania jeden raz w bazie danych master. Z tego powodu jeśli masz wiele baz danych, które używają tej samej nazwy użytkownika i hasła, przy użyciu zawartych użytkowników nie jest zalecane. 

## <a name="how-to-configure-logins-and-users"></a>Konfigurowanie logowania i użytkowników
Jeśli używasz logowania i użytkowników (zamiast zawartych użytkowników), należy wykonać dodatkowe czynności, aby upewnić się, że tej samej nazwy logowania istnieje w bazie danych master. W poniższych sekcjach opisano zagadnienia związane i dodatkowe kroki.

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Konfigurowanie dostępu użytkowników do dodatkowej lub odzyskanej bazy danych
Aby dodatkowej bazy danych może być używany jako tylko do odczytu pomocniczej bazy danych i zapewnienie właściwy dostęp do nowych podstawowej bazy danych lub baza danych odzyskać, używając funkcji przywracania geograficznie głównej bazy danych serwera docelowego musi mieć odpowiednie zabezpieczenia Konfiguracja na miejscu przed odzyskiwania.

Określone uprawnienia dla każdego kroku są opisane w dalszej części tego tematu.

Przygotowywanie użytkownikowi dostęp do pomocniczego — replikacja geograficzna, należy wykonać w ramach konfigurowania — replikacja geograficzna. Przygotowywanie użytkownikowi dostęp do baz danych przywrócone z magazynu geograficznie, należy wykonać w dowolnej chwili, gdy oryginalny serwer jest w trybie online (np. w ramach wyszczególniania odzyskiwania po awarii).

> [!NOTE]
> Jeśli w trybie Failover lub przywracaniem geograficznym na serwerze, który nie ma poprawnego skonfigurowania logowania, dostęp do niego będzie ograniczony do konta administratora serwera.
> 
> 

Konfigurowanie logowania na serwerze docelowym obejmuje trzy kroki przedstawione poniżej:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Określ nazwy logowania z dostępem do podstawowej bazy danych:
Pierwszym krokiem procesu jest ustalenie, który logowania musi zostać skopiowane na serwer docelowy. Jest to realizowane przy użyciu pary instrukcji "SELECT", co w logicznej głównej bazy danych na serwerze źródłowym i w głównej bazy danych.

Tylko administrator serwera lub członek **LoginManager** roli serwera można określić nazwy logowania na serwerze źródłowym z następujących instrukcji SELECT. 

    SELECT [name], [sid] 
    FROM [sys].[sql_logins] 
    WHERE [type_desc] = 'SQL_Login'

Tylko członek roli bazy danych db_owner, użytkownika dbo lub administratorem serwera, można określić wszystkich podmiotów zabezpieczeń użytkownika bazy danych w podstawowej bazy danych.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Znajdź identyfikator SID dla logowania w kroku 1:
Porównanie dane wyjściowe zapytań z poprzedniej sekcji, a pasujące identyfikatory SID, możesz mapować logowanie do serwera bazy danych użytkownika. Nazwy logowania, w których użytkownik bazy danych ze zgodnym identyfikatorem SID ma dostęp użytkownika do tej bazy danych jako główny użytkownik bazy danych. 

Następujące zapytanie może służyć do wyświetlenia wszystkich podmiotów zabezpieczeń użytkowników i ich identyfikatorów SID w bazie danych. Tylko członek admin rola lub serwer bazy danych db_owner mogą wykonania tego zapytania.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> **INFORMATION_SCHEMA** i **sys** użytkownicy mają *NULL* identyfikatory SID i **gościa** identyfikator SID jest **0x00**. **Dbo** może rozpoczynać identyfikatora SID *0x01060000000001648000000000048454*, jeśli administrator serwera, a nie jest członkiem twórcy bazy danych **dbmanager:**.
> 
> 

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Utwórz dane logowania na serwerze docelowym:
Ostatnim krokiem jest przejdź do serwera docelowego lub serwerów i generować dane logowania z odpowiednich identyfikatorów SID. Podstawowa składnia jest następująca:

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Jeśli chcesz udzielić użytkownikowi dostępu do lokacji dodatkowej, ale nie do serwera podstawowego, możesz to zrobić, zmieniając dane logowania użytkownika na serwerze podstawowym za pomocą następującej składni.
> 
> ALTER LOGIN <login name> WYŁĄCZ
> 
> Wyłącz nie powoduje zmiany hasła, więc można zawsze włączyć ją w razie potrzeby.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać więcej informacji na temat zarządzania dostęp do bazy danych i logowania do, zobacz [zabezpieczeń bazy danych SQL: Zarządzanie zabezpieczeniami dostępu i nazwy logowania bazy danych](sql-database-manage-logins.md).
* Aby uzyskać więcej informacji dotyczących użytkowników zawartej bazy danych, zobacz [zawarte bazy danych użytkowników — co Twojej bazy danych przenośne](https://msdn.microsoft.com/library/ff929188.aspx).
* Aby uzyskać informacje o użyciu i skonfigurowaniu aktywna replikacja geograficzna, zobacz [aktywna replikacja geograficzna](sql-database-geo-replication-overview.md)
* Informacje o korzystaniu z przywracaniem geograficznym, zobacz [geograficzne](sql-database-recovery-using-backups.md#geo-restore)

