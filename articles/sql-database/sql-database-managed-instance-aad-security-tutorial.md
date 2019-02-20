---
title: Zabezpieczanie wystąpienia zarządzanego usługi Azure SQL Database przy użyciu identyfikatorów logowania usługi Azure AD | Microsoft Docs
description: Informacje na temat technik i funkcji umożliwiających zabezpieczanie wystąpienia zarządzanego w usłudze Azure SQL Database oraz korzystania z identyfikatorów logowania usługi Azure AD
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 402e10d9b99dbf0eeba8aac27071e4d78fdf0f01
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984515"
---
# <a name="tutorial-managed-instance-security-in-azure-sql-database-using-azure-ad-logins"></a>Samouczek: Zabezpieczanie wystąpienia zarządzanego usługi Azure SQL Database przy użyciu identyfikatorów logowania usługi Azure AD

Wystąpienie zarządzane zapewnia niemal wszystkie funkcje zabezpieczeń, które oferuje najnowszy lokalny aparat bazy danych programu SQL Server (Enterprise Edition):

- Ograniczanie dostępu w izolowanym środowisku
- Korzystanie z mechanizmów uwierzytelniania, które wymagają potwierdzenia tożsamości (uwierzytelnianie w usłudze Azure AD i uwierzytelnianie SQL)
- Stosowanie autoryzacji za pośrednictwem członkostwa i uprawnień opartych na rolach.
- Włączanie funkcji zabezpieczeń.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Tworzenie identyfikatora logowania usługi Azure Active Directory (AD) dla wystąpień zarządzanych
> - Przyznawanie uprawnień do identyfikatorów logowania usługi Azure AD w wystąpieniach zarządzanych
> - Tworzenie użytkowników usługi Azure AD na podstawie identyfikatorów logowania usługi Azure AD
> - Przypisywanie uprawnień do użytkowników usługi Azure AD i zarządzanie zabezpieczeniami bazy danych
> - Korzystanie z personifikacji użytkowników usługi Azure AD
> - Korzystanie z zapytań w wielu bazach danych dotyczących użytkowników usługi Azure AD
> - Informacje na temat funkcji zabezpieczeń takich jak ochrona przed zagrożeniami, inspekcja, maskowanie danych i szyfrowanie

> [!NOTE]
> Identyfikatory logowania usługi Azure AD dla wystąpienia zarządzanego są dostępne w **publicznej wersji zapoznawczej**.

Aby dowiedzieć się więcej, zobacz artykuły [Omówienie wystąpień zarządzanych usługi Azure SQL Database](sql-database-managed-instance-index.yml) i [Funkcje](sql-database-managed-instance.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, upewnij się, że dysponujesz następującymi elementami:

- Program [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Wystąpienie zarządzane usługi Azure SQL Database
  - Zapoznaj się z tym artykułem: [Szybki start: Tworzenie wystąpienia zarządzanego usługi Azure SQL Database](sql-database-managed-instance-get-started.md)
- Możliwość dostępu do wystąpienia zarządzanego oraz [aprowizowanego administratora usługi Azure AD dla wystąpienia zarządzanego](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Aby dowiedzieć się więcej, zobacz:
    - [Connect your application to a managed instance](sql-database-managed-instance-connect-app.md) (Łączenie aplikacji z wystąpieniem zarządzanym) 
    - [Managed instance connectivity architecture](sql-database-managed-instance-connectivity-architecture.md) (Architektura łączności wystąpienia zarządzanego)
    - [Configure and manage Azure Active Directory authentication with SQL (Konfigurowanie uwierzytelniania w usłudze Azure Active Directory i zarządzanie nim przy użyciu języka SQL)](sql-database-aad-authentication-configure.md)

## <a name="limiting-access-to-your-managed-instance"></a>Ograniczanie dostępu do wystąpienia zarządzanego

Dostęp do wystąpień zarządzanych jest możliwy tylko za pośrednictwem prywatnego adresu IP. Z wystąpieniem zarządzanym nie można łączyć się z poziomu żadnych punktów końcowych usługi poza siecią wystąpienia zarządzanego. Podobnie jak w przypadku wyizolowanego środowiska lokalnego programu SQL Server aplikacje lub użytkownicy muszą mieć dostęp do sieci wystąpienia zarządzanego (VNet) przed nawiązaniem połączenia. Aby uzyskać więcej informacji, zapoznaj się z artykułem [Connect your application to a managed instance](sql-database-managed-instance-connect-app.md) (Łączenie aplikacji z wystąpieniem zarządzanym).

> [!NOTE] 
> Ponieważ do wystąpień zarządzanych dostęp można uzyskać tylko w ich sieci wirtualnej, [reguły zapory usługi SQL Database](sql-database-firewall-configure.md) nie mają zastosowania. Wystąpienie zarządzane ma swoją własną [wbudowaną zaporę](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

## <a name="create-an-azure-ad-login-for-a-managed-instance-using-ssms"></a>Tworzenie identyfikatora logowania usługi Azure AD dla wystąpienia zarządzanego przy użyciu programu SSMS

Pierwszy identyfikator logowania usługi Azure AD musi zostać utworzony przez standardowe konto programu SQL Server (spoza usługi Azure AD) mające rolę `sysadmin`. Zobacz następujące artykuły, aby zapoznać się z przykładami nawiązywania połączenia z wystąpieniem zarządzanym:

- [Szybki start: Konfigurowanie maszyny wirtualnej platformy Azure w celu łączenia się z wystąpieniem zarządzanym](sql-database-managed-instance-configure-vm.md)
- [Szybki start: Konfigurowanie połączenia punkt-lokacja z wystąpieniem zarządzanym ze środowiska lokalnego](sql-database-managed-instance-configure-p2s.md)

> [!IMPORTANT]
> Konto administratora usługi Azure AD użyte w celu skonfigurowania wystąpienia zarządzanego nie może zostać użyte do utworzenia identyfikatora logowania usługi Azure AD w ramach wystąpienia zarządzanego. Pierwszy identyfikator logowania usługi Azure AD musi zostać utworzony przy pomocy konta programu SQL Server z rolą `sysadmin`. Jest to tymczasowe ograniczenie, które zostanie usunięte w momencie, kiedy identyfikatory logowania usługi Azure AD staną się ogólnie dostępne. Jeśli spróbujesz użyć konta administratora usługi Azure AD do utworzenia identyfikatora logowania, zostanie wyświetlony następujący komunikat o błędzie: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`

1. Zaloguj się do wystąpienia zarządzanego przy użyciu standardowego konta programu SQL Server (spoza usługi Azure AD) z rolą `sysadmin`, korzystając z programu [SQL Server Management Studio](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance).

2. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie**.

3. W oknie zapytania użyj następującej składni, aby utworzyć identyfikator logowania dla lokalnego konta usługi Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    W tym przykładzie utworzono identyfikator logowania dla konta nativeuser@aadsqlmi.onmicrosoft.com.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Na pasku narzędzi wybierz polecenie **Wykonaj**, aby utworzyć identyfikator logowania.

5. Sprawdź nowo dodany identyfikator logowania, wykonując następujące polecenie w języku T-SQL:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![native-login.png](media/sql-database-managed-instance-security-tutorial/native-login.png)

Aby uzyskać więcej informacji, zobacz temat [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="granting-permissions-to-allow-the-creation-of-managed-instance-logins"></a>Udzielanie uprawnień w celu umożliwienia tworzenia identyfikatorów logowania wystąpienia zarządzanego

Aby utworzyć inne identyfikatory logowania usługi Azure AD, należy przyznać uprawnienia lub role programu SQL Server podmiotowi zabezpieczeń (SQL lub Azure AD).

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

- Jeśli identyfikator logowania to podmiot zabezpieczeń SQL, tylko identyfikatory logowania, które są częścią roli `sysadmin` mogą użyć polecenia create do utworzenia identyfikatorów logowania dla konta usługi Azure AD.

### <a name="azure-ad-authentication"></a>Uwierzytelnianie w usłudze Azure AD

- Aby umożliwić nowo utworzonym identyfikatorom logowania usługi Azure AD tworzenie identyfikatorów logowania dla innych użytkowników, grup lub aplikacji usługi Azure AD, musisz przyznać identyfikatorowi logowania rolę serwera `sysadmin` lub `securityadmin`. 
- Należy przydzielić identyfikatorowi logowania usługi Azure AD przynajmniej uprawnienie **ALTER ANY LOGIN**, aby umożliwić mu tworzenie innych identyfikatorów logowania usługi Azure AD. 
- Domyślnie standardowe uprawnienia przyznawane nowo utworzonym identyfikatorom logowania usługi Azure AD w bazie danych master to: **CONNECT SQL** i **VIEW ANY DATABASE**.
- Rola serwera `sysadmin` może zostać przydzielona wielu identyfikatorom logowania usługi Azure AD w ramach wystąpienia zarządzanego.

Aby dodać identyfikator logowania do roli serwera `sysadmin`:

1. Zaloguj się ponownie w wystąpieniu zarządzanym lub użyj istniejącego połączenia z podmiotem zabezpieczeń SQL mającym rolę `sysadmin`.

1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie**.

1. Przyznaj identyfikatorowi logowania usługi Azure AD rolę serwera `sysadmin`, korzystając z następującej składni T-SQL:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    W poniższym przykładzie przydzielono rolę serwera `sysadmin` identyfikatorowi logowania nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-logins-using-ssms"></a>Tworzenie dodatkowych identyfikatorów logowania usługi Azure AD przy użyciu programu SSMS

Po utworzeniu identyfikatora logowania usługi Azure AD oraz dodaniu do niego uprawnień `sysadmin` można za jego pomocą tworzyć dodatkowe identyfikatory logowania, używając klauzuli **FROM EXTERNAL PROVIDER** z poleceniem **CREATE LOGIN**.

1. Połącz się z wystąpieniem zarządzanym za pomocą identyfikatora logowania usługi Azure AD, korzystając z programu SQL Server Management Studio. Wprowadź nazwę hosta wystąpienia zarządzanego. Są trzy opcje uwierzytelniania w programie SMSS podczas logowania za pomocą konta usługi Azure AD:

    - Active Directory — uniwersalne z obsługą uwierzytelniania wieloskładnikowego
    - Active Directory — hasło
    - Active Directory — zintegrowane </br>

    ![ssms-login-prompt.png](media/sql-database-managed-instance-security-tutorial/ssms-login-prompt.png)

    Aby uzyskać więcej informacji, zobacz następujące artykuły: [Uniwersalne uwierzytelnianie przy użyciu usługi SQL Database i SQL Data Warehouse (obsługa SSMS w przypadku uwierzytelniania wieloskładnikowego)](sql-database-ssms-mfa-authentication.md)

1. Wybierz opcję **Active Directory — uniwersalne z obsługą uwierzytelniania wieloskładnikowego**. To spowoduje wywołanie okna logowania uwierzytelniania wieloskładnikowego. Zaloguj się przy użyciu hasła usługi Azure AD.

    ![mfa-login-prompt.png](media/sql-database-managed-instance-security-tutorial/mfa-login-prompt.png)

1. W **Eksploratorze obiektów** programu SSMS kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie**.
1. W oknie zapytania użyj następującej składni, aby utworzyć identyfikator logowania dla kolejnego konta usługi Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    W tym przykładzie utworzono identyfikator logowania dla użytkownika usługi Azure AD bob@aadsqlmi.net, którego domena aadsqlmi.net jest sfederowana z domeną usługi Azure AD aadsqlmi.onmicrosoft.com.

    Wykonaj następujące polecenie w języku T-SQL. Federacyjne konta usługi Azure AD zastępują lokalne identyfikatory logowania i użytkowników systemu Windows na potrzeby wystąpień zarządzanych.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Utwórz bazę danych w wystąpieniu zarządzanym, korzystając ze składni [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current). Ta baza danych będzie używana do przetestowania identyfikatorów logowania użytkownika w kolejnej sekcji.
    1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie**.
    1. W oknie zapytania użyj następującej składni, aby utworzyć bazę danych o nazwie **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Utwórz identyfikator logowania wystąpienia zarządzanego dla grupy w usłudze Azure AD. Grupa musi istnieć w usłudze Azure AD przed dodaniem identyfikatora logowania do wystąpienia zarządzanego. Zobacz [Tworzenie grupy podstawowej i dodawanie członków przy użyciu usługi Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Utwórz grupę _mygroup_ i dodaj członków do tej grupy.

1. Otwórz nowe okno zapytania w programie SQL Server Management Studio.

    W tym przykładzie przyjęto założenie, że istnieje grupa o nazwie _mygroup_ w usłudze Azure AD. Wykonaj następujące polecenie:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. W ramach testu zaloguj się do wystąpienia zarządzanego za pomocą nowo utworzonego identyfikatora logowania lub grupy. Otwórz nowe połączenie z wystąpieniem zarządzanym i użyj nowego identyfikatora logowania podczas uwierzytelniania.
1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie** dla nowego połączenia.
1. Sprawdź uprawnienia serwera dla nowo utworzonego identyfikatora logowania usługi Azure AD, wykonując następujące polecenie:

    ```sql
    SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
    GO
    ```

> [!NOTE]
> Użytkownicy-goście usługi Azure AD mają możliwość obsługi identyfikatorów logowania wystąpienia zarządzanego tylko, jeśli są dodani jako członkowie grupy usługi Azure AD. Użytkownik-gość usługi Azure AD to konto zaproszone do usługi Azure AD, do której należy wystąpienie zarządzane, z innej usługi Azure AD. Na przykład joe@contoso.com (konto usługi Azure AD) lub steve@outlook.com (konto MSA) można dodać do grupy aadsqlmi usługi Azure AD. Po dodaniu użytkowników do grupy można utworzyć identyfikator logowania w bazie danych **master** wystąpienia zarządzanego dla grupy, korzystając ze składni **CREATE LOGIN**. Użytkownicy-goście, którzy są członkami tej grupy, mogą nawiązać połączenie z wystąpieniem zarządzanym, korzystając z aktualnego identyfikatora logowania (na przykład joe@contoso.com lub steve@outlook.com).

## <a name="create-an-azure-ad-user-from-the-azure-ad-login-and-give-permissions"></a>Tworzenie użytkownika usługi Azure AD na podstawie identyfikatora logowania usługi Azure AD oraz nadawanie uprawnień

Autoryzacja w poszczególnych bazach danych w wystąpieniu zarządzanym odbywa się podobnie jak w lokalnym programie SQL Server. Można utworzyć użytkownika na podstawie istniejącego identyfikatora logowania w bazie danych i nadać mu uprawienia w tej bazie danych lub dodać go do roli bazy danych.

Po utworzeniu bazy danych o nazwie **MyMITestDB** oraz utworzeniu identyfikatora logowania, który ma tylko uprawnienia domyślne, utworzymy teraz użytkownika na podstawie tego identyfikatora. Obecnie identyfikator logowania może nawiązać połączenie z wystąpieniem zarządzanym i wyświetlać wszystkie bazy danych, ale nie może korzystać z tych baz danych. Jeśli zalogujesz się za pomocą konta usługi Azure AD, które ma uprawnienia domyślne, i spróbujesz rozwinąć nowo utworzoną bazę danych, zostanie wyświetlony następujący komunikat o błędzie:

![ssms-db-not-accessible.png](media/sql-database-managed-instance-security-tutorial/ssms-db-not-accessible.png)

Aby uzyskać więcej informacji na temat udzielania uprawnień bazie danych, zobacz [Getting Started with Database Engine Permissions (Rozpoczynanie pracy z uprawnieniami aparatu bazy danych)](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Tworzenie użytkownika usługi Azure AD oraz tworzenie przykładowej tabeli

1. Zaloguj się do wystąpienia zarządzanego za pomocą konta `sysadmin`, korzystając z programu SQL Server Management Studio.
1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie**.
1. W oknie zapytania użyj następującej składni, aby utworzyć użytkownika usługi Azure AD na podstawie identyfikatora logowania usługi Azure AD:

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    W poniższym przykładzie utworzono użytkownika bob@aadsqlmi.net na podstawie identyfikatora logowania bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Możliwe jest również utworzenie użytkownika usługi Azure AD na podstawie identyfikatora logowania usługi Azure AD, który jest grupą.

    W następującym przykładzie utworzono identyfikator logowania dla grupy usługi Azure AD _mygroup_, która istnieje w usłudze Azure AD.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Wszyscy użytkownicy, którzy należą do grupy **mygroup**, mogą uzyskać dostęp do bazy danych **MyMITestDB**.

    > [!IMPORTANT]
    > Podczas tworzenia elementu **USER** na podstawie identyfikatora logowania usługi Azure AD parametr user_name powinien być taki sam jak parametr login_name elementu **LOGIN**.

    Aby uzyskać więcej informacji, zobacz temat [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current).

1. W nowym oknie zapytania utwórz tabelę testów, korzystając z następującego polecenia w języku T-SQL:

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. Utwórz połączenie w programie SSMS z utworzonym użytkownikiem. Zauważysz, że nie można wyświetlić tabeli **TestTable**, która została wcześniej utworzona przez konto `sysadmin`. Musimy nadać temu użytkownikowi uprawnienia odczytu danych z bazy danych.

1. Możesz sprawdzić bieżące uprawnienia użytkownika, wykonując następujące polecenie:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Dodawanie użytkowników do ról na poziomie bazy danych

Wyświetlanie danych przez użytkownika jest możliwe dopiero po przyznaniu mu odpowiednich [ról na poziomie bazy danych](/sql/relational-databases/security/authentication-access/database-level-roles).

1. Zaloguj się do wystąpienia zarządzanego za pomocą konta `sysadmin`, korzystając z programu SQL Server Management Studio.

1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie**.

1. Nadaj użytkownikowi usługi Azure AD rolę bazy danych `db_datareader`, korzystając z następującej składni w języku T-SQL:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    W poniższym przykładzie przyznano użytkownikowi bob@aadsqlmi.net oraz grupie _mygroup_ uprawnienia `db_datareader` w bazie danych **MyMITestDB**:

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Sprawdź, czy użytkownik usługi Azure AD utworzony w bazie danych istnieje, wykonując następujące polecenie:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Utwórz nowe połączenie z wystąpieniem zarządzanym z użytkownikiem, który został dodany do roli `db_datareader`.
1. Rozwiń bazę danych w **Eksploratorze obiektów**, aby wyświetlić tabelę.

    ![ssms-test-table.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table.png)

1. Otwórz nowe okno zapytania i wykonaj następującą instrukcję SELECT:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Czy jesteś w stanie wyświetlić dane w tabeli? Kolumny powinny zostać zwrócone.

    ![ssms-test-table-query.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table-query.png)

## <a name="impersonating-azure-ad-server-level-principals-logins"></a>Personifikacja podmiotów zabezpieczeń na poziomie serwera usługi Azure AD (identyfikatorów logowania)

Wystąpienie zarządzane obsługuje personifikację podmiotów zabezpieczeń na poziomie serwera usługi Azure AD (identyfikatorów logowania).

### <a name="test-impersonation"></a>Testowanie personifikacji

1. Zaloguj się do wystąpienia zarządzanego za pomocą konta `sysadmin`, korzystając z programu SQL Server Management Studio.

1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie**.

1. W oknie zapytania użyj następującego polecenia, aby utworzyć nową procedurę składowaną:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Użyj następującego polecenia, aby sprawdzić, czy użytkownik, którego personifikujesz podczas wykonywania procedury składowanej, to **bob@aadsqlmi.net**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Przetestuj personifikację, korzystając z instrukcji EXECUTE AS LOGIN:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Tylko podmioty zabezpieczeń na poziomie programu SQL Server (identyfikatory logowania), które są częścią roli `sysadmin`, mogą wykonać następujące operacje, których celem są podmioty zabezpieczeń usługi Azure AD: 
> - EXECUTE AS USER
> - EXECUTE AS LOGIN

## <a name="using-cross-database-queries-in-managed-instances"></a>Korzystanie z zapytań w wielu bazach danych w wystąpieniach zarządzanych

Funkcja wykonywania zapytań w wielu bazach danych jest obsługiwana dla kont usługi Azure AD z identyfikatorami logowania usługi Azure AD. Aby przetestować zapytania w wielu bazach danych w grupie usługi Azure AD, musimy utworzyć kolejną bazę danych i tabelę. Możesz pominąć krok tworzenia kolejnej bazy danych i tabeli, jeśli już istnieją.

1. Zaloguj się do wystąpienia zarządzanego za pomocą konta `sysadmin`, korzystając z programu SQL Server Management Studio.
1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie**.
1. W oknie zapytania użyj następującego polecenia, aby utworzyć bazę danych o nazwie **MyMITestDB2** oraz tabelę o nazwie **TestTable2**:

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. W nowym oknie zapytania wykonaj następujące polecenie, aby utworzyć użytkownika _mygroup_ w nowej bazie danych **MyMITestDB2** i udziel uprawnień SELECT w tej bazie danych użytkownikowi _mygroup_:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Zaloguj się do wystąpienia zarządzanego przy użyciu programu SQL Server Management Studio jako członek grupy usługi Azure AD _mygroup_. Otwórz nowe okno zapytania i wykonaj instrukcję SELECT w wielu bazach danych:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Powinny zostać wyświetlone wyniki z tabeli **TestTable2**.

## <a name="additional-scenarios-supported-for-azure-ad-logins-public-preview"></a>Dodatkowe scenariusze obsługiwane dla identyfikatorów logowania usługi Azure AD (publiczna wersja zapoznawcza) 

- Dla identyfikatorów logowania usługi Azure AD obsługiwane jest zarządzanie agentem SQL oraz wykonywanie zadań.
- Operacje tworzenia kopii zapasowej bazy danych oraz przywracania mogą być wykonywane przy użyciu identyfikatorów usługi Azure AD.
- [Inspekcja](sql-database-managed-instance-auditing.md) wszystkich instrukcji związanych z identyfikatorami logowania usługi Azure AD oraz zdarzenia uwierzytelniania.
- Dedykowane połączenie administratora dla identyfikatorów logowania usługi Azure AD, które są członkami roli serwera `sysadmin`.
- Identyfikatory logowania usługi Azure AD są obsługiwane przy użyciu narzędzi [sqlcmd](/sql/tools/sqlcmd-utility) oraz [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
- Wyzwalacze logowania są obsługiwane dla zdarzeń logowania przychodzących z identyfikatorów logowania usługi Azure AD.
- Przy użyciu identyfikatorów logowania usługi Azure AD można skonfigurować usługi Service Broker i DBMail.


## <a name="next-steps"></a>Następne kroki

### <a name="enable-security-features"></a>Włączanie funkcji zabezpieczeń.

Zapoznaj się z poniższym artykułem [Managed Instance capabilities security features](sql-database-managed-instance.md#azure-sql-database-security-features) (Funkcje zabezpieczeń wystąpienia zarządzanego), aby uzyskać pełną listę metod zabezpieczania bazy danych. Omówione są następujące funkcje zabezpieczeń:

- [Inspekcja wystąpienia zarządzanego](sql-database-managed-instance-auditing.md) 
- [Zawsze szyfrowane](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Wykrywanie zagrożeń](sql-database-managed-instance-threat-detection.md) 
- [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking)
- [Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security) 
- [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Funkcje wystąpienia zarządzanego

Aby zapoznać się z pełnym omówieniem funkcji wystąpienia zarządzanego, zobacz:

> [!div class="nextstepaction"]
> [Funkcje wystąpienia zarządzanego](sql-database-managed-instance.md)
