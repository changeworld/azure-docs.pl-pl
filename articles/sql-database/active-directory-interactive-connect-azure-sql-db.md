---
title: ActiveDirectoryInteractive łączy do bazy danych SQL | Dokumentacja firmy Microsoft
description: Przykład kodu w języku C#, wraz z wyjaśnieniem, łączenie z usługą Azure SQL Database przy użyciu trybu SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi
ms.date: 03/12/2019
manager: craigg
ms.openlocfilehash: bc7274308b8a349d16866f107eac4a57e115be9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160836"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Nawiązać połączenie z usługi Azure SQL Database za pomocą usługi Azure Multi-Factor Authentication

Ten artykuł zawiera C# program, który nawiązuje połączenie z bazą danych SQL Azure. Program używa uwierzytelniania tryb interakcyjny, który obsługuje [usługi Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

Aby uzyskać więcej informacji na temat uwierzytelniania wieloskładnikowego obsługę narzędzia SQL zobacz [pomocy technicznej usługi Azure Active Directory w SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Uwierzytelnianie wieloskładnikowe dla usługi Azure SQL Database

Począwszy od .NET Framework w wersji 4.7.2, wyliczenia [ `SqlAuthenticationMethod` ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) ma nową wartość: `ActiveDirectoryInteractive`. W kliencie C# program, wartość wyliczenia instruuje system, aby użyć trybu interakcyjnego usługi Azure Active Directory (Azure AD), która obsługuje uwierzytelnianie wieloskładnikowe, aby nawiązać połączenie z bazą danych Azure SQL database. Użytkownik, który uruchamia program zobaczy następujących oknach dialogowych:

* Okno dialogowe wyświetla nazwę użytkownika usługi Azure AD, która poprosi o podanie hasła użytkownika.

   Jeśli domena użytkownika jest Sfederowane z usługą Azure AD, to okno dialogowe nie pojawia się, ponieważ hasło nie jest wymagane.

   Jeśli zasady usługi Azure AD narzuca użytkownika usługi Multi-Factor Authentication, okna dialogowe w dwóch następnych są wyświetlane.

* Gdy użytkownik przechodzi przez uwierzytelnianie wieloskładnikowe, po raz pierwszy system wyświetla okno dialogowe z pytaniem, czy są dostępne do wysyłania wiadomości SMS na numer telefonu komórkowego. Każdy komunikat zawiera *kod weryfikacyjny* , użytkownik musi wprowadzić w następnym oknie dialogowym.

* Okno dialogowe z pytaniem, czy kod weryfikacyjny uwierzytelnianie wieloskładnikowe, które system zostało wysłane na telefon komórkowy.

Aby uzyskać informacje o sposobie konfigurowania usługi Azure AD, aby wymagać uwierzytelniania wieloskładnikowego, zobacz [wprowadzenie do usługi Azure Multi-Factor Authentication w chmurze](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Zrzuty ekranu z tych okien dialogowych, zobacz [Konfiguruj uwierzytelnianie wieloskładnikowe dla programu SQL Server Management Studio i usługi Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Możesz wyszukiwać interfejsów API programu .NET Framework za pomocą [strony narzędzia przeglądarka interfejsu API .NET](https://docs.microsoft.com/dotnet/api/).
>
> Można także przeszukać bezpośrednio z [opcjonalne? termin =&lt;Wyszukaj wartość&gt; parametru](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Konfigurowanie usługi C# aplikacji w witrynie Azure portal

Przed rozpoczęciem należy [serwera Azure SQL Database](sql-database-get-started-portal.md) utworzona i dostępna.

### <a name="register-your-app-and-set-permissions"></a>Zarejestruj swoją aplikację i ustawianie uprawnień

Aby użyć uwierzytelniania usługi Azure AD Twojej C# program musi zarejestrować się jako aplikację usługi Azure AD. Aby zarejestrować aplikację, musisz być administratorem usługi Azure AD lub usługi Azure AD przypisać użytkownika *Deweloper aplikacji* roli. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [przypisywanie ról administratora i użytkowników niebędących administratorami do użytkowników z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

Kończenie rejestracji aplikacji zostanie utworzona i wyświetlona **identyfikator aplikacji**. Program musi zawierać ten identyfikator, aby połączyć.

Aby zarejestrować i ustawianie uprawnień niezbędnych dla aplikacji:

1. W witrynie Azure portal wybierz **usługi Azure Active Directory** > **rejestracje aplikacji** > **rejestrowanie nowej aplikacji**.

    ![Rejestracja aplikacji](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Po utworzeniu rejestracji aplikacji **identyfikator aplikacji** wartość jest wygenerowany i wyświetlony.

    ![Wyświetla identyfikator aplikacji](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Wybierz **zarejestrowana aplikacja** > **ustawienia** > **wymagane uprawnienia** > **Dodaj**.

    ![Ustawienia uprawnień dotyczących zarejestrowana aplikacja](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Wybierz **wymagane uprawnienia** > **Dodaj** > **wybierz interfejs API** > **usługi Azure SQL Database**.

    ![Dodaj dostęp do interfejsu API usługi Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Wybierz **dostęp do interfejsu API** > **wybierz uprawnienia** > **delegowane uprawnienia**.

    ![Deleguj uprawnienia do interfejsu API usługi Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Ustaw administratora usługi Azure AD dla serwera usługi SQL Database

Dla Twojej C# program do uruchomienia, administrator serwera Azure SQL musi przypisać administratora usługi Azure AD dla serwera usługi SQL Database. 

Na **programu SQL Server** wybierz opcję **administratora usługi Active Directory** > **Ustaw administratora**.

Aby dowiedzieć się więcej o usłudze Azure AD Administratorzy i użytkownicy usługi Azure SQL Database, zobacz zrzuty ekranu w [Konfigurowanie i zarządzanie uwierzytelnianiem usługi Azure Active Directory z usługą SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Dodaj użytkownika bez uprawnień administratora do konkretnej bazy danych (opcjonalnie)

Administrator usługi Azure AD dla serwera bazy danych SQL można uruchomić C# przykładowy program. Użytkownika usługi Azure AD można uruchomić program, jeśli są one w bazie danych. Administrator usługi Azure AD SQL lub użytkownika usługi Azure AD, która już istnieje w bazie danych i ma `ALTER ANY USER` uprawnień w bazie danych można dodać użytkownika.

Można dodać użytkownika do bazy danych przy użyciu języka SQL [ `Create User` ](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) polecenia. Może to być na przykład `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Aby uzyskać więcej informacji, zobacz [użyciu Azure uwierzytelnianie usługi Active Directory do uwierzytelniania przy użyciu bazy danych SQL Database, wystąpienia zarządzanego lub SQL Data Warehouse](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Nowa wartość wyliczenia uwierzytelniania

C# Przykład opiera się na [ `System.Data.SqlClient` ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) przestrzeni nazw. Specjalne znaczenie w odniesieniu do uwierzytelniania wieloskładnikowego jest wyliczenia `SqlAuthenticationMethod`, która zawiera następujące wartości:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Użyj tej wartości z nazwą użytkownika usługi Azure AD do zaimplementowania uwierzytelniania wieloskładnikowego. Ta wartość jest celem niniejszego artykułu. Tworzy środowisko interaktywne, wyświetlając okna dialogowe hasło użytkownika, a następnie do weryfikacji uwierzytelniania Multi-Factor Authentication, jeśli uwierzytelnianie wieloskładnikowe nakłada się na tego użytkownika. Ta wartość jest dostępnych w programie .NET Framework w wersji 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Użyj tej wartości dla *federacyjnych* konta. Dla kont federacyjnych nazwa użytkownika jest znany do domeny Windows. Ta metoda uwierzytelniania nie obsługuje uwierzytelniania wieloskładnikowego.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Ta wartość służy do uwierzytelniania, która wymaga usługi Azure AD, nazwę użytkownika i hasło. Usługa Azure SQL Database jest uwierzytelnianie. Ta metoda nie obsługuje uwierzytelniania wieloskładnikowego.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Ustaw C# wartości parametrów w witrynie Azure portal

Aby uzyskać C# program w celu pomyślnego uruchomienia, musisz przypisać odpowiednie wartości do pola statyczne. Podane tu są pola z wartościami w przykładzie. Także wyświetlane są lokalizacje portalu platformy Azure, gdzie można uzyskać odpowiednie wartości.

| Nazwa pola statycznego | Przykładowa wartość | Miejsce w witrynie Azure portal |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **Serwery SQL** > **Filtruj według nazwy** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **Usługa Azure Active Directory** > **użytkownika** > **nowy użytkownik-Gość** |
| Initial_DatabaseName | "myDatabase" | **Serwery SQL** > **baz danych SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Usługa Azure Active Directory** > **rejestracje aplikacji** > **wyszukiwanie według nazwy** > **Identyfikatora aplikacji** |
| RedirectUri | Nowy identyfikator Uri ("https://mywebserver.com/") | **Usługa Azure Active Directory** > **rejestracje aplikacji** > **wyszukiwanie według nazwy** > *[Your-App rejestracji]*  >  **Ustawienia** > **RedirectURIs**<br /><br />W tym artykule dowolną prawidłową wartość jest dobrym rozwiązaniem dla elementu RedirectUri, ponieważ nie jest używany w tym miejscu. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Weryfikuj za pomocą programu SQL Server Management Studio

Przed uruchomieniem C# programu, to dobry pomysł, aby sprawdzić, czy usługi instalacji i konfiguracji są poprawne w SQL Server Management Studio (SSMS). Wszelkie C# awarię programu następnie może zostać zawężony do kodu źródłowego.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Sprawdź adresy IP zapory bazy danych SQL

Uruchom program SSMS na tym samym komputerze, w tym samym budynku, w którym zamierzasz uruchomić C# program. Dla tego testu wszelkie **uwierzytelniania** tryb jest OK. Jeśli ma żadnego wskazania, że zapory serwera bazy danych nie akceptuje żadnych adresu IP, zobacz [reguły zapory na poziomie serwera i na poziomie bazy danych Azure SQL Database](sql-database-firewall-configure.md) Aby uzyskać pomoc.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Zweryfikowanie uwierzytelniania wieloskładnikowego w usłudze Azure Active Directory

Ponownie uruchom program SSMS, tym razem z **uwierzytelniania** równa **usługi Active Directory — uniwersalnego z obsługą uwierzytelniania Wieloskładnikowego**. Ta opcja wymaga programu SSMS wersji 17.5 lub nowszej.

Aby uzyskać więcej informacji, zobacz [skonfigurować uwierzytelnianie wieloskładnikowe SSMS i Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Jeśli jesteś użytkownikiem-gościem w bazie danych, należy podać nazwę domeny usługi Azure AD dla bazy danych: Wybierz **opcje** > **Identyfikatora nazwy lub dzierżawy domeny AD**. Aby znaleźć nazwę domeny w witrynie Azure portal, wybierz **usługi Azure Active Directory** > **niestandardowe nazwy domen**. W C# program przykładowy, podając nazwę domeny nie jest konieczne.

## <a name="c-code-example"></a>Przykładowy kod w języku C#

Przykład C# programu zależy od [ *Microsoft.IdentityModel.Clients.ActiveDirectory* ](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) zestawu biblioteki DLL.

Aby zainstalować ten pakiet w programie Visual Studio, wybierz opcję **projektu** > **Zarządzaj pakietami NuGet**. Wyszukiwanie i instalowanie **Microsoft.IdentityModel.Clients.ActiveDirectory**.

Jest to przykład C# kodu źródłowego.

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

Jest to przykład C# testowanie danych wyjściowych.

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
