---
title: ActiveDirectoryInteractive łączy się z sql
description: Przykład kodu języka C# z objaśnieniami, aby połączyć się z usługą Azure SQL Database przy użyciu sqlauthenticationMethod.ActiveDirectoryInteractive mode.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi, vanto
ms.date: 10/11/2019
ms.openlocfilehash: 5e7d58e5e0fc79e407e77ae9d73314a1d5d22666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73692302"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Łączenie się z usługą Azure SQL Database za pomocą uwierzytelniania wieloskładnikowego platformy Azure

Ten artykuł zawiera program języka C#, który łączy się z usługą Azure SQL Database. Program korzysta z uwierzytelniania w trybie interaktywnym, które obsługuje [uwierzytelnianie wieloskładnikowe platformy Azure.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)

Aby uzyskać więcej informacji na temat obsługi uwierzytelniania wieloskładnikowego dla narzędzi SQL, zobacz [Obsługa usługi Azure Active Directory w narzędziach SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Uwierzytelnianie wieloskładnikowe dla bazy danych SQL usługi Azure

Począwszy od programu .NET Framework w wersji 4.7.2, wyliczenie [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) ma nową wartość: `ActiveDirectoryInteractive`. W programie języka C# klienta wartość wyliczenia kieruje system do korzystania z trybu interaktywnego usługi Azure Active Directory (Azure AD), który obsługuje uwierzytelnianie wieloskładnikowe, aby połączyć się z bazą danych SQL platformy Azure. Użytkownik uruchamiany program widzi następujące okna dialogowe:

* Okno dialogowe, w które wyświetla nazwę użytkownika usługi Azure AD i prosi o hasło użytkownika.

   Jeśli domena użytkownika jest sfederowana z usługą Azure AD, to okno dialogowe nie jest wyświetlane, ponieważ hasło nie jest potrzebne.

   Jeśli zasady usługi Azure AD nakładają uwierzytelnianie wieloskładnikowe na użytkownika, zostaną wyświetlone następne dwa okna dialogowe.

* Gdy użytkownik po raz pierwszy przechodzi przez uwierzytelnianie wieloskładnikowe, system wyświetla okno dialogowe z prośbą o numer telefonu komórkowego do wysyłania wiadomości tekstowych. Każda wiadomość zawiera *kod weryfikacyjny,* który użytkownik musi wprowadzić w następnym oknie dialogowym.

* Okno dialogowe z prośbą o kod weryfikacyjny uwierzytelniania wieloskładnikowego, który system wysłał na telefon komórkowy.

Aby uzyskać informacje dotyczące konfigurowania usługi Azure AD do uwierzytelniania wieloskładnikowego, zobacz [Wprowadzenie do uwierzytelniania wieloskładnikowego platformy Azure w chmurze](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Aby uzyskać zrzuty ekranu tych okien dialogowych, zobacz [Konfigurowanie uwierzytelniania wieloskładnikowego dla programu SQL Server Management Studio i usługi Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Interfejsy API programu .NET Framework można przeszukiwać za pomocą [strony narzędzia przeglądarka interfejsu API .NET](https://docs.microsoft.com/dotnet/api/).
>
> Można również wyszukiwać bezpośrednio za pomocą [opcjonalnego&lt;parametru wartości&gt; wyszukiwania ? term= wyszukiwania](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Konfigurowanie aplikacji języka C# w witrynie Azure portal

Przed rozpoczęciem należy utworzyć i udostępnić [serwer bazy danych SQL Azure.](sql-database-get-started-portal.md)

### <a name="register-your-app-and-set-permissions"></a>Rejestrowanie aplikacji i ustawianie uprawnień

Aby użyć uwierzytelniania usługi Azure AD, program języka C# musi zarejestrować się jako aplikacja usługi Azure AD. Aby zarejestrować aplikację, musisz być administratorem usługi Azure AD lub użytkownikem przypisanym roli *dewelopera aplikacji* usługi Azure AD. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [Przypisywanie ról administratora i osób niebędących administratorami użytkownikom za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

Ukończenie rejestracji aplikacji generuje i wyświetla **identyfikator aplikacji**. Program musi zawierać ten identyfikator, aby się połączyć.

Aby zarejestrować i ustawić niezbędne uprawnienia dla aplikacji:

1. W witrynie Azure portal wybierz pozycję**Rejestracje** > aplikacji **usługi Azure Active Directory** > Nowa**rejestracja**.

    ![Rejestracja aplikacji](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Po utworzeniu rejestracji aplikacji wartość **identyfikatora aplikacji** jest generowana i wyświetlana.

    ![Wyświetlany identyfikator aplikacji](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Wybierz **uprawnienia** > interfejsu API**Dodaj uprawnienie**.

    ![Ustawienia uprawnień dla zarejestrowanej aplikacji](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Wybierz **interfejsy API, których moja organizacja używa** > wpisz usługę Azure SQL **Database** w > wyszukiwania i wybierz usługę Azure **SQL Database**.

    ![Dodawanie dostępu do interfejsu API dla bazy danych SQL usługi Azure](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Wybierz **pozycję Uprawnienia delegowane** > **user_impersonation** > **Dodaj uprawnienia**.

    ![Delegowanie uprawnień do interfejsu API dla bazy danych SQL usługi Azure](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Ustawianie administratora usługi Azure AD dla serwera bazy danych SQL

Aby program języka C# był uruchamiany, administrator serwera SQL platformy Azure musi przypisać administratora usługi Azure AD dla serwera bazy danych SQL. 

Na stronie **SQL Server** wybierz pozycję **Administrator** > usługi Active Directory**Ustaw administratora**.

Aby uzyskać więcej informacji na temat administratorów i użytkowników usługi Azure AD dla usługi Azure SQL Database, zobacz zrzuty ekranu w [obszarze Konfigurowanie uwierzytelniania usługi Azure Active Directory i zarządzanie nim za pomocą bazy danych SQL](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Dodawanie użytkownika niebędącego administratorem do określonej bazy danych (opcjonalnie)

Administrator usługi Azure AD dla serwera bazy danych SQL może uruchomić przykładowy program języka C#. Użytkownik usługi Azure AD można uruchomić program, jeśli znajdują się w bazie danych. Administrator SQL usługi Azure AD lub użytkownik usługi Azure AD, `ALTER ANY USER` który istnieje już w bazie danych i ma uprawnienia do bazy danych można dodać użytkownika.

Można dodać użytkownika do bazy danych [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) za pomocą polecenia SQL. Może to być na przykład `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Aby uzyskać więcej informacji, zobacz [Używanie uwierzytelniania usługi Azure Active Directory do uwierzytelniania przy użyciu bazy danych SQL, wystąpienia zarządzanego lub magazynu danych SQL](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Nowa wartość wyliczenia uwierzytelniania

Przykład języka C# [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) opiera się na obszarze nazw. Szczególne zainteresowanie dla uwierzytelniania wieloskładnikowego jest wyliczenie, `SqlAuthenticationMethod`który ma następujące wartości:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Ta wartość służy do implementowania uwierzytelniania wieloskładnikowego za pomocą nazwy użytkownika usługi Azure AD. Ta wartość jest przedmiotem niniejszego artykułu. Tworzy interaktywne środowisko, wyświetlając okna dialogowe dla hasła użytkownika, a następnie dla sprawdzania poprawności uwierzytelniania wieloskładnikowego, jeśli uwierzytelnianie wieloskładnikowe jest narzucone na tego użytkownika. Ta wartość jest dostępna począwszy od programu .NET Framework w wersji 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Użyj tej wartości dla konta *federacyjnego.* W przypadku konta federacyjnego nazwa użytkownika jest znana domenie systemu Windows. Ta metoda uwierzytelniania nie obsługuje uwierzytelniania wieloskładnikowego.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Użyj tej wartości do uwierzytelniania, który wymaga nazwy użytkownika usługi Azure AD i hasła. Usługa Azure SQL Database wykonuje uwierzytelnianie. Ta metoda nie obsługuje uwierzytelniania wieloskładnikowego.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Ustawianie wartości parametrów języka C# z witryny Azure portal

Aby program C# został pomyślnie uruchomiony, należy przypisać odpowiednie wartości do pól statycznych. Pokazane tutaj są pola z przykładowymi wartościami. Pokazano również lokalizacje witryny azure portal, gdzie można uzyskać potrzebne wartości.

| Nazwa pola statycznego | Przykładowa wartość | Gdzie w witrynie Azure portal |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **Serwery** > SQL**Filtruj według nazwy** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **Nowy** > **użytkownik** > usługi Azure Active Directory**użytkownik-gość** |
| Initial_DatabaseName | "myDatabase" | **Bazy** > **danych SQL** serwerów SQL |
| Identyfikator aplikacji klienta | "a94f9c62-97fe-4d19-b06d-1111111111111111111" | **Rejestracje** >  > **aplikacji**usługi Azure Active Directory**Wyszukiwanie według nazwy** > **identyfikator aplikacji** |
| RedirectUri (RedirectUri) | nowy Uri("https://mywebserver.com/") | **Rejestracje** >  > **aplikacji**usługi Azure Active Directory**Wyszukiwanie według nazwy** > [Ustawienia rejestracji*aplikacji]* > **RedirectURIs** **Settings** > <br /><br />W tym artykule wszelkie prawidłowe wartości jest w porządku dla RedirectUri, ponieważ nie jest używany w tym miejscu. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Weryfikowanie za pomocą programu SQL Server Management Studio

Przed uruchomieniem programu C# warto sprawdzić, czy konfiguracje i konfiguracje są poprawne w programie SQL Server Management Studio (SSMS). Każdy błąd programu C# można następnie zawęzić do kodu źródłowego.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Weryfikowanie adresów IP zapory bazy danych SQL

Uruchom system SSMS z tego samego komputera, w tym samym budynku, w którym planujesz uruchomić program C#. W tym teście każdy tryb **uwierzytelniania** jest OK. Jeśli istnieją jakiekolwiek wskazania, że zapora serwera bazy danych nie akceptuje adresu IP, zobacz [Reguły zapory na poziomie serwera i bazy danych na poziomie bazy danych azure,](sql-database-firewall-configure.md) aby uzyskać pomoc.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Weryfikowanie uwierzytelniania wieloskładnikowego usługi Azure Active Directory

Uruchom ponownie SSMS, tym razem z **uwierzytelnianiem** ustawionym na **Active Directory - Uniwersalny z obsługą usługi MFA**. Ta opcja wymaga systemu SSMS w wersji 17.5 lub nowszej.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania wieloskładnikowego dla usług SSMS i usługi Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Jeśli jesteś użytkownikiem-gościem w bazie danych, musisz również podać nazwę domeny usługi Azure AD dla bazy danych: Wybierz **opcje** > **nazwa domeny usługi AD lub identyfikator dzierżawy**. Aby znaleźć nazwę domeny w witrynie Azure portal, wybierz pozycję**Niestandardowe nazwy domen**usługi Azure Active **Directory** > . W przykładowym programie języka C# podanie nazwy domeny nie jest konieczne.

## <a name="c-code-example"></a>Przykład kodu języka C#

Przykładowy program Języka C# opiera się na zestawie bibliotek dll [*Microsoft.IdentityModel.Clients.ActiveDirectory.*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory)

Aby zainstalować ten pakiet, w programie Visual Studio wybierz pozycję **Project** > **Manage NuGet Packages**. Wyszukaj i zainstaluj **program Microsoft.IdentityModel.Clients.ActiveDirectory**.

Jest to przykład kodu źródłowego języka C#.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

Jest to przykład danych wyjściowych testu języka C#.

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne.

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
