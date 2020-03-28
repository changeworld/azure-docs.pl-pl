---
title: Migrowanie użytkowników i grup programu SQL ServerWindows do wystąpienia zarządzanego przy użyciu języka T-SQL
description: Dowiedz się, jak migrować lokalnych użytkowników i grupy systemu Windows z programem SQL Server do wystąpienia zarządzanego
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: 2c8d7252b4e4ca8caa465727c0d2328c4aafaefb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227921"
---
# <a name="tutorial-migrate-sql-server-on-premises-windows-users-and-groups-to-azure-sql-database-managed-instance-using-t-sql-ddl-syntax"></a>Samouczek: Migrowanie lokalnych użytkowników i grup systemu Windows z programem SQL Server do wystąpienia zarządzanego usługi Azure SQL Database przy użyciu składni DDL T-SQL

> [!NOTE]
> Składnia używana do migracji użytkowników i grup do wystąpienia zarządzanego w tym artykule znajduje się w **publicznej wersji zapoznawczej**.

W tym artykule przejdziesz przez proces migracji lokalnych użytkowników i grup systemu Windows w programie SQL Server do istniejącego wystąpienia zarządzanego usługi Azure SQL Database przy użyciu składni T-SQL.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Tworzenie loginów dla programu SQL Server
> - Tworzenie testowej bazy danych do migracji
> - Tworzenie loginów, użytkowników i ról
> - Tworzenie kopii zapasowych i przywracanie bazy danych do wystąpienia zarządzanego (MI)
> - Ręczna migracja użytkowników do mi przy użyciu składni ALTER USER
> - Testowanie uwierzytelniania z nowymi zamapowanymi użytkownikami

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, obowiązują następujące wymagania wstępne:

- Domena systemu Windows jest sfederowana z usługą Azure Active Directory (Azure AD).
- Dostęp do usługi Active Directory w celu tworzenia użytkowników/grup.
- Istniejący program SQL Server w środowisku lokalnym.
- Istniejące wystąpienie zarządzane. Zobacz [Szybki start: Tworzenie wystąpienia zarządzanego usługi Azure SQL Database](sql-database-managed-instance-get-started.md).
  - A `sysadmin` w wystąpieniu zarządzanym musi służyć do tworzenia logowania usługi Azure AD.
- [Utwórz administratora usługi Azure AD dla wystąpienia zarządzanego](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).
- Możesz połączyć się z wystąpieniem zarządzanym w sieci. Dodatkowe informacje można znaleźć w następujących artykułach: 
    - [Łączenie aplikacji z wystąpieniem zarządzanym usługi Azure SQL Database](sql-database-managed-instance-connect-app.md)
    - [Szybki start: konfigurowanie połączenia typu "punkt-lokacja" z wystąpieniem zarządzanym bazy danych SQL platformy Azure z lokalnego](sql-database-managed-instance-configure-p2s.md)
    - [Konfigurowanie publicznego punktu końcowego w wystąpieniu zarządzanym usługi Azure SQL Database](sql-database-managed-instance-public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>Składnia DDL T-SQL

Poniżej znajduje się składnia DDL T-SQL używana do obsługi lokalnych użytkowników i grup sql servera migracji do wystąpienia zarządzanego za pomocą uwierzytelniania usługi Azure AD.

```sql
-- For individual Windows users with logins 
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com]; 

--For individual groups with logins 
ALTER USER [domainName\groupName] WITH LOGIN=[groupName] 
```

## <a name="arguments"></a>Argumenty

_Nazwa_domeny_</br>
Określa nazwę domeny użytkownika.

_Nazwę użytkownika_</br>
Określa nazwę użytkownika zidentyfikowanego wewnątrz bazy danych.

_= domainName.com\@loginName_</br>
Ponowne mapowanie użytkownika do logowania usługi Azure AD

_Groupname_</br>
Określa nazwę grupy zidentyfikowanej wewnątrz bazy danych.

## <a name="part-1-create-logins-for-sql-server-on-premises-users-and-groups"></a>Część 1: Tworzenie loginów dla lokalnych użytkowników i grup programu SQL Server

> [!IMPORTANT]
> Poniższa składnia tworzy logowania użytkownika i grupy w programie SQL Server. Przed wykonaniem poniższej składni należy upewnić się, że użytkownik i grupa istnieją w usłudze Active Directory (AD). </br> </br>
> Użytkownicy: testUser1, testGroupUser </br>
> Grupa: migracja — testGroupUser musi należeć do grupy migracji w u.

Poniższy przykład tworzy login w programie SQL Server dla konta o nazwie _testUser1_ w domenie _aadsqlmi_. 

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases
 
use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;   
go; 

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser]. 
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;    
go; 


-- Check logins were created
select * from sys.server_principals; 
go; 
```

Utwórz bazę danych dla tego testu.

```sql
-- Create a database called [migration]
create database migration 
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>Część 2: Tworzenie użytkowników i grup systemu Windows, a następnie dodawanie ról i uprawnień

Aby utworzyć użytkownika testowego, użyj następującej składni.

```sql
use migration;  
go 
 
-- Create Windows user [aadsqlmi\testUser1] with login 
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1]; 
go 
```

Sprawdź uprawnienia użytkownika:

```sql
-- Check the user in the Metadata 
select * from sys.database_principals; 
go 
 
-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions; 
go
```

Utwórz rolę i przypisz testowego użytkownika do tej roli:

```sql 
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole; 
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole; 
go 

alter role UserMigrationRole add member [aadsqlmi\testUser1]; 
go 
``` 

Użyj następującej kwerendy, aby wyświetlić nazwy użytkowników przypisane do określonej roli:

```sql
-- Display user name assigned to a specific role 
SELECT DP1.name AS DatabaseRoleName,    
   isnull (DP2.name, 'No members') AS DatabaseUserName    
 FROM sys.database_role_members AS DRM   
 RIGHT OUTER JOIN sys.database_principals AS DP1   
   ON DRM.role_principal_id = DP1.principal_id   
 LEFT OUTER JOIN sys.database_principals AS DP2   
   ON DRM.member_principal_id = DP2.principal_id   
WHERE DP1.type = 'R' 
ORDER BY DP1.name; 
```

Aby utworzyć grupę, użyj następującej składni. Następnie dodaj grupę `db_owner`do roli .

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration]; 
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration'; 
go 

--Check the db_owner role for 'aadsqlmi\migration' group 
select is_rolemember('db_owner', 'aadsqlmi\migration')   
go 
-- Output  ( 1 means YES) 
```

Utwórz tabelę testową i dodaj kilka danych przy użyciu następującej składni:

```sql
-- Create a table and add data 
create table test ( a int, b int); 
go 

insert into test values (1,10) 
go 

-- Check the table values
select * from test; 
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-managed-instance"></a>Część 3: Tworzenie kopii zapasowych i przywracanie bazy danych poszczególnych użytkowników do wystąpienia zarządzanego

Utwórz kopię zapasową bazy danych migracji przy użyciu artykułu [Kopiuj bazy danych za pomocą kopii zapasowej i przywracania](/sql/relational-databases/databases/copy-databases-with-backup-and-restore)lub użyj następującej składni:

```sql
use master; 
go 
backup database migration to disk = 'C:\Migration\migration.bak'; 
go
```

Postępuj zgodnie z naszym [przewodnikiem Szybki start: Przywracanie bazy danych do wystąpienia zarządzanego.](sql-database-managed-instance-get-started-restore.md)

## <a name="part-4-migrate-users-to-managed-instance"></a>Część 4: Migrowanie użytkowników do wystąpienia zarządzanego

> [!NOTE]
> Administrator usługi Azure AD dla funkcji wystąpienia zarządzanego po zmianie utworzenia. Aby uzyskać więcej informacji, zobacz [Nowa funkcja administratora usługi Azure AD dla MI](sql-database-aad-authentication-configure.md#new-azure-ad-admin-functionality-for-mi).

Wykonaj polecenie ALTER USER, aby zakończyć proces migracji w wystąpieniu zarządzanym.

1. Zaloguj się do wystąpienia zarządzanego przy użyciu konta administratora usługi Azure AD dla wystąpienia zarządzanego. Następnie utwórz swój login usługi Azure AD w wystąpieniu zarządzanym przy użyciu następującej składni. Aby uzyskać więcej informacji, zobacz [Samouczek: Zabezpieczenia wystąpienia zarządzanego w bazie danych SQL usługi Azure przy użyciu podmiotów serwera usługi Azure AD (logowania)](sql-database-managed-instance-aad-security-tutorial.md).

    ```sql
    use master 
    go 
    
    -- Create login for AAD user [testUser1@aadsqlmi.net] 
    create login [testUser1@aadsqlmi.net] from external provider 
    go
    
    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net] 
    create login [migration] from external provider 
    go 
     
    --Check the two new logins 
    select * from sys.server_principals 
    go
    ```

1. Sprawdź migrację pod kątem poprawnej bazy danych, tabeli i podmiotów.

    ```sql
    -- Switch to the database migration that is already restored for MI 
    use migration;  
    go 
     
    --Check if the restored table test exist and contain a row 
    select * from test; 
    go 
     
    -- Check that the SQL on-premises Windows user/group exists  
    select * from sys.database_principals; 
    go 
    -- the old user aadsqlmi\testUser1 should be there 
    -- the old group aadsqlmi\migration should be there
    ```

1. Użyj składni ALTER USER, aby zamapować użytkownika lokalnego na identyfikator logowania usługi Azure AD.

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net]; 
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
    -- New user testUser1@aadsqlmi.net should be there instead 
     
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go

    -- Check a specific role   
    -- Display Db user name assigned to a specific role 
    SELECT DP1.name AS DatabaseRoleName,    
    isnull (DP2.name, 'No members') AS DatabaseUserName    
    FROM sys.database_role_members AS DRM   
    RIGHT OUTER JOIN sys.database_principals AS DP1   
    ON DRM.role_principal_id = DP1.principal_id   
    LEFT OUTER JOIN sys.database_principals AS DP2   
    ON DRM.member_principal_id = DP2.principal_id   
    WHERE DP1.type = 'R' 
    ORDER BY DP1.name;
    ```

1. Użyj składni ALTER USER, aby zamapować lokalną grupę na identyfikator logowania usługi Azure AD.

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration]; 
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
     
    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go 
     
    --Check the db_owner role for 'aadsqlmi\migration' user 
    select is_rolemember('db_owner', 'migration')   
    go 
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>Część 5: Testowanie uwierzytelniania użytkowników lub grup usługi Azure AD

Przetestuj uwierzytelnianie do wystąpienia zarządzanego przy użyciu użytkownika wcześniej mapowane do logowania usługi Azure AD przy użyciu składni ALTER USER.
 
1. Zaloguj się do sfederowanej maszyny Wirtualnej przy użyciu subskrypcji MI jako`aadsqlmi\testUser1`
1. Korzystając z programu SQL Server Management Studio (SSMS), zaloguj się do `migration`wystąpienia zarządzanego przy użyciu zintegrowanego uwierzytelniania usługi Active **Directory,** łącząc się z bazą danych .
    1. Można również zalogować testUser1@aadsqlmi.net się przy użyciu poświadczeń za pomocą opcji SSMS **Active Directory – Universal z obsługą usługi MFA.** Jednak w takim przypadku nie można użyć mechanizmu logowania jednokrotnego i należy wpisać hasło. Nie trzeba używać sfederowanej maszyny Wirtualnej, aby zalogować się do wystąpienia zarządzanego.
1. Jako część elementu członkowskiego roli **SELECT**można `test` wybrać z tabeli

    ```sql
    Select * from test  --  and see one row (1,10)
    ```


Przetestuj uwierzytelnianie w wystąpieniu zarządzanym przy użyciu członka grupy `migration`systemu Windows . Użytkownik `aadsqlmi\testGroupUser` powinien zostać dodany do `migration` grupy przed migracją.

1. Zaloguj się do sfederowanej maszyny Wirtualnej przy użyciu subskrypcji MI jako`aadsqlmi\testGroupUser` 
1. Korzystanie z usługi SSMS ze zintegrowanym uwierzytelnianiem **usługi Active Directory** umożliwia łączenie się z serwerem MI i bazą danych`migration`
    1. Można również zalogować testGroupUser@aadsqlmi.net się przy użyciu poświadczeń za pomocą opcji SSMS **Active Directory – Universal z obsługą usługi MFA.** Jednak w takim przypadku nie można użyć mechanizmu logowania jednokrotnego i należy wpisać hasło. Nie trzeba używać sfederowanej maszyny Wirtualnej, aby zalogować się do wystąpienia zarządzanego. 
1. W ramach `db_owner` roli można utworzyć nową tabelę.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```
                             
> [!NOTE] 
> Ze względu na znany problem z projektem dla usługi Azure SQL DB instrukcja tworzenia tabeli wykonywane jako członek grupy zakończy się niepowodzeniem z następującym błędem: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4 
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> Bieżące obejście jest utworzenie tabeli z istniejącym schematem w przypadku powyżej <dbo.new>

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Migrowanie programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database w trybie offline przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)