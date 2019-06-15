---
title: Konfigurowanie zabezpieczeń usługi Azure SQL Database do odzyskiwania po awarii | Dokumentacja firmy Microsoft
description: Dowiedz się, zagadnienia dotyczące zabezpieczeń do konfigurowania i zarządzania zabezpieczeniami po przywróceniu bazy danych lub przejściu w tryb failover na serwer pomocniczy.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 8a2a2fffa9ed3a4dae3c0768291b7585be4bfc6d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64690833"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Konfigurowanie i zarządzanie nimi zabezpieczeń usługi Azure SQL Database funkcja przywracania geograficznego lub pracy awaryjnej

W tym artykule opisano wymagania dotyczące uwierzytelniania, konfigurować i kontrolować [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md) i [automatyczny tryb failover grupy](sql-database-auto-failover-group.md). Zapewnia również kroki wymagane do skonfigurowania dostępu użytkowników do pomocniczej bazy danych. Ponadto opisano również, jak włączyć dostęp do odzyskanej bazy danych po zakończeniu korzystania z [geoprzywracanie](sql-database-recovery-using-backups.md#geo-restore). Aby uzyskać więcej informacji na temat opcji odzyskiwania, zobacz [omówienie ciągłości działania](sql-database-business-continuity.md).

## <a name="disaster-recovery-with-contained-users"></a>Odzyskiwanie po awarii z zawartych użytkowników

W przeciwieństwie do tradycyjnych użytkowników, którzy muszą być zamapowane na nazwy logowania w bazie danych master, zawartego użytkownika jest całkowicie zarządzana przez sama baza danych. To ma dwie korzyści. W scenariuszu odzyskiwania po awarii użytkownicy mogą w dalszym ciągu połączenia z bazą danych nowego podstawowego lub odzyskać bazy danych przy użyciu przywracania geograficznego bez przeprowadzania dodatkowej konfiguracji, ponieważ bazy danych zarządza użytkownikami. Dostępne są także potencjalnych skalowalności i wydajności korzyści z tej konfiguracji z punktu widzenia logowania. Aby uzyskać więcej informacji, zobacz artykuł [Contained Database Users - Making Your Database Portable](https://msdn.microsoft.com/library/ff929188.aspx) (Użytkownicy zawartej bazy danych — tworzenie przenośnej bazy danych).

Jest głównym to kompromis Zarządzanie proces odzyskiwania po awarii w skali jest trudniejsze. W przypadku wielu baz danych, które używają tych samych danych logowania obsługi poświadczeń przy użyciu zawartych użytkowników w wielu bazach danych może negate — zalety zawartych użytkowników. Na przykład rotacji haseł wymaga, aby wprowadzone zmiany konsekwentnie w wielu bazach danych zamiast zmiany hasła podczas logowania jeden raz w bazie danych master. Z tego powodu w przypadku wielu baz danych, które używają tej samej nazwy użytkownika i hasła, za pomocą zawartych użytkowników nie jest zalecane.

## <a name="how-to-configure-logins-and-users"></a>Sposób konfigurowania kont logowania i użytkowników

Jeśli używasz kont logowania i użytkowników (zamiast zawartych użytkowników), należy wykonać dodatkowe kroki, aby upewnić się, że tej samej nazwy logowania istnieją w bazie danych master. W poniższych sekcjach opisano zagadnienia związane i dodatkowe kroki.

  >[!NOTE]
  > Istnieje również możliwość logowania do usługi Azure Active Directory (AAD) umożliwia zarządzanie bazami danych. Aby uzyskać więcej informacji, zobacz [kont logowania Azure SQL i użytkownicy](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Konfigurowanie dostępu użytkowników do dodatkowej lub odzyskanej bazy danych

Aby pomocniczej bazy danych może być używany jako tylko do odczytu pomocniczej bazy danych i zapewnienie odpowiedniego dostępu do nowej podstawowej bazy danych lub bazy danych, odzyskane przy użyciu przywracania geograficznego bazie danych master serwera docelowego musi mieć odpowiednie zabezpieczenia Konfiguracja na miejscu przed odzyskiwania.

Określone uprawnienia dla każdego kroku są opisane w dalszej części tego tematu.

Przygotowywanie dostęp użytkowników do dodatkowej replikacji geograficznej, należy wykonać w ramach konfigurowania replikacji geograficznej. Trwa przygotowywanie użytkownikom dostęp do baz danych przywrócona geograficznie, należy wykonać w dowolnej chwili, gdy oryginalny serwer jest w trybie online (np. podczas testowania odzyskiwania po awarii).

> [!NOTE]
> W przypadku przejścia w tryb failover lub funkcja przywracania geograficznego na serwerze, na którym nie ma nazwy logowania prawidłowo skonfigurowane, dostęp do niego będzie ograniczona do konta administratora serwera.

Konfigurowanie logowania na serwerze docelowym obejmuje trzy kroki opisane poniżej:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Określenia nazwy logowania z uprawnieniami do podstawowej bazy danych

Pierwszym krokiem procesu jest ustalenie, które z nich musi zostać zduplikowane na serwerze docelowym. Jest to realizowane przy użyciu pary instrukcji "SELECT", co w logicznej głównej bazy danych na serwerze źródłowym i w podstawowej bazie danych sam.

Tylko administrator serwera lub członek **LoginManager** roli serwera można określić nazwy logowania na serwerze źródłowym z następujących instrukcji SELECT.

    SELECT [name], [sid]
    FROM [sys].[sql_logins]
    WHERE [type_desc] = 'SQL_Login'

Tylko członek roli bazy danych db_owner, użytkownika dbo lub administratora serwera, można określić wszystkich podmiotów zabezpieczeń użytkownika bazy danych w podstawowej bazie danych.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Znajdowanie identyfikatora SID dla nazw logowania w kroku 1

Porównując dane wyjściowe zapytań z poprzedniej sekcji, a pasujące identyfikatory SID, można mapować nazwy logowania serwera bazy danych użytkownika. Identyfikatory logowania, które mają użytkownika bazy danych ze zgodnym identyfikatorem SID mieć dostęp użytkownika do tej bazy danych jako ten użytkownik bazy danych jednostki.

Następujące zapytanie, może służyć do wyświetlenia wszystkich podmiotami zabezpieczeń użytkownika i identyfikatory SID w bazie danych. Tylko członek administrator roli lub serwera bazy danych db_owner można uruchomić tego zapytania.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> **INFORMATION_SCHEMA** i **sys** użytkownicy mają *NULL* identyfikatory SID i **gościa** jest identyfikator SID **0x00**. **Dbo** identyfikator SID może się rozpoczynać *0x01060000000001648000000000048454*, jeśli Kreator bazy danych została administrator serwera, a nie jest członkiem **DbManager**.

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Utwórz dane logowania na serwerze docelowym

Ostatnim krokiem jest przejdź do serwera docelowego lub serwerów i generować dane logowania przy użyciu odpowiednich identyfikatorów SID. Podstawowa składnia jest następująca.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Jeśli chcesz udzielić użytkownikowi dostępu do regionu pomocniczego, ale nie do podstawowej, możesz zrobić to przez zmianę nazwy logowania użytkownika na serwerze podstawowym, przy użyciu następującej składni.
>
> ```sql
> ALTER LOGIN <login name> DISABLE
> ```
>
> Wyłącz nie powoduje zmiany hasła, dzięki czemu zawsze można włączyć je w razie potrzeby.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat zarządzania dostęp do bazy danych i nazwami logowania, zobacz [zabezpieczeń bazy danych SQL: Zarządzanie zabezpieczeniami dostępu i logowania w bazie danych](sql-database-manage-logins.md).
* Aby uzyskać więcej informacji na temat zawartych użytkowników bazy danych, zobacz [użytkownicy zawartej bazy danych — tworzenie przenośnej bazy danych](https://msdn.microsoft.com/library/ff929188.aspx).
* Aby dowiedzieć się więcej na temat aktywnej replikacji geograficznej, zobacz [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md).
* Aby dowiedzieć się więcej o grupach automatyczny tryb failover, zobacz [automatyczny tryb failover grupy](sql-database-auto-failover-group.md).
* Aby dowiedzieć się, jak przy użyciu przywracania geograficznego, zobacz [przywracania geograficznego](sql-database-recovery-using-backups.md#geo-restore)
