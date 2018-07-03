---
title: ActiveDirectoryInteractive łączy do bazy danych SQL | Dokumentacja firmy Microsoft
description: Przykład kodu w języku C#, wraz z wyjaśnieniem, łączenie z usługą Azure SQL Database przy użyciu trybu SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: active directory
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: MirekS
ms.reviewer: GeneMi
ms.openlocfilehash: cbbaa789295a0e8fe602d7d90055f6d3af6bfc01
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "34643760"
---
# <a name="use-activedirectoryinteractive-mode-to-connect-to-azure-sql-database"></a>Użyj trybu ActiveDirectoryInteractive, aby nawiązać połączenie z bazą danych SQL Azure

Ten artykuł zawiera możliwy do uruchomienia C# przykładu kodu, który łączy z usługą Microsoft Azure SQL Database. Program C# używa tryb interakcyjny, uwierzytelnianie, które obsługuje usługę Azure AD uwierzytelnianie wieloskładnikowe (MFA). Na przykład próba połączenia może zawierać kod weryfikacyjny, są wysyłane z telefonem komórkowym.

Aby uzyskać więcej informacji na temat MFA obsługę narzędzia SQL zobacz [pomocy technicznej usługi Azure Active Directory w SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).




## <a name="sqlauthenticationmethod-activedirectoryinteractive-enum-value"></a>SqlAuthenticationMethod. Wartość wyliczenia ActiveDirectoryInteractive

Począwszy od .NET Framework w wersji 4.7.2, wyliczenia [ **SqlAuthenticationMethod** ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) ma nową wartość **. ActiveDirectoryInteractive**. W przypadku użycia przez klienta programu C#, ta wartość wyliczenia poleca systemowi użyć trybu interakcyjnego usługi Azure AD, Obsługa uwierzytelniania Wieloskładnikowego do uwierzytelniania w usłudze Azure SQL Database. Użytkownik, który następnie uruchamia program zobaczy następujących oknach dialogowych:

1. Okno dialogowe, który wyświetla nazwę użytkownika usługi Azure AD i która poprosi o podanie hasła użytkownika usługi Azure AD.
    - To okno dialogowe nie jest wyświetlana, jeśli hasło nie jest wymagana. Hasło nie jest potrzebny, jeśli domena użytkownika jest Sfederowane przy użyciu usługi Azure AD.

    Jeśli uwierzytelnianie wieloskładnikowe nakłada się na użytkownika przez zasady w usłudze Azure AD, następujące okna dialogowe są wyświetlane obok.

2. Tylko pierwszego uruchomienia podejrzewać scenariusza uwierzytelniania Wieloskładnikowego, system wyświetla dodatkowe okno. Okno dialogowe poprosi o podanie numeru telefonu komórkowego, do którego będą wysyłane wiadomości SMS. Każdy komunikat zawiera *kod weryfikacyjny* , użytkownik musi wprowadzić w następnym oknie dialogowym.

3. Okno dialogowe z pytaniem, czy kod weryfikacyjny MFA, który system zostało wysłane na telefon komórkowy.

Aby uzyskać informacje o sposobie konfigurowania usługi Azure AD, aby wymagać uwierzytelniania Wieloskładnikowego, zobacz [wprowadzenie do usługi Azure Multi-Factor Authentication w chmurze](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Zrzuty ekranu z tych okien dialogowych, zobacz [Konfiguruj uwierzytelnianie wieloskładnikowe dla programu SQL Server Management Studio i usługi Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Naszą stronę wyszukiwania ogólnego dla wszystkich rodzajów interfejsów API programu .NET Framework jest dostępna na Poniższy link, aby nasz wygodny **przeglądarka interfejsu API .NET** narzędzie:
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> Dodając nazwę typu opcjonalny dołączany **? termin =** parametru strony wyszukiwania może mieć naszych wyników gotowy i czeka dla nas:
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)


## <a name="preparations-for-c-by-using-the-azure-portal"></a>Przygotowania do języka C# za pomocą witryny Azure portal

Przyjęto założenie, że masz już [utworzony serwer Azure SQL Database](sql-database-get-started-portal.md) i dostępne.


### <a name="a-create-an-app-registration"></a>A. Tworzenie rejestracji aplikacji

Aby użyć uwierzytelniania usługi Azure AD, program kliencki C# należy podać identyfikator GUID jako *clientId* gdy program próbuje nawiązać połączenie. Kończenie rejestracji aplikacji generuje i wyświetla identyfikator GUID w witrynie Azure portal, oznaczone jako **identyfikator aplikacji**. Kroki nawigacji są następujące:

1. Witryna Azure portal &gt; **usługi Azure Active Directory** &gt; **rejestracji aplikacji**

    ![Rejestracja aplikacji](media\active-directory-interactive-connect-azure-sql-db\sshot-create-app-registration-b20.png)

2. **Identyfikator aplikacji** wartość jest wygenerowany i wyświetlony.

    ![Wyświetla identyfikator aplikacji](media\active-directory-interactive-connect-azure-sql-db\sshot-application-id-app-regis-mk49.png)

3. **Zarejestrowana aplikacja** &gt; **ustawienia** &gt; **wymagane uprawnienia** &gt; **Dodaj**

    ![Ustawienia uprawnień dotyczących zarejestrowana aplikacja](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

4. **Wymagane uprawnienia** &gt; **dostępu Dodaj interfejs API** &gt; **wybierz interfejs API** &gt; **usługi Azure SQL Database**

    ![Dodaj dostęp do interfejsu API usługi Azure SQL Database](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

5. **Dostęp do interfejsu API** &gt; **wybierz uprawnienia** &gt; **delegowane uprawnienia**

    ![Deleguj uprawnienia do interfejsu API usługi Azure SQL Database](media\active-directory-interactive-connect-azure-sql-db\sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)


### <a name="b-set-azure-ad-admin-on-your-sql-database-server"></a>B. Ustaw administratora usługi Azure AD na serwerze bazy danych SQL

Każdy serwer usługi Azure SQL Database ma swój własny serwerem logicznym SQL usługi Azure AD. W naszym C# w scenariuszu należy ustawić administrator usługi Azure AD dla serwera Azure SQL.

1. **Program SQL Server** &gt; **administratora usługi Active Directory** &gt; **Ustaw administratora**

    - Aby dowiedzieć się więcej o usłudze Azure AD Administratorzy i użytkownicy usługi Azure SQL Database, zobacz zrzuty ekranu w [Konfigurowanie i zarządzanie uwierzytelnianiem usługi Azure Active Directory z usługą SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server), w sekcji **obsługi administracyjnej platformy Azure Administratora usługi Active Directory dla serwera usługi Azure SQL Database**.


### <a name="c-prepare-an-azure-ad-user-to-connect-to-a-specific-database"></a>C. Przygotowanie użytkownika usługi Azure AD, aby nawiązać połączenie konkretnej bazy danych

W usłudze Azure AD, które są specyficzne dla serwera usługi Azure SQL Database możesz dodać użytkownika, który ma dostęp do określonej bazy danych.

Aby uzyskać więcej informacji, zobacz [użyciu Azure uwierzytelnianie usługi Active Directory do uwierzytelniania przy użyciu bazy danych SQL Database, wystąpienia zarządzanego lub SQL Data Warehouse](sql-database-aad-authentication.md).


### <a name="d-add-a-non-admin-user-to-azure-ad"></a>D. Dodaj użytkownika bez uprawnień administratora do usługi Azure AD

Administrator usługi Azure AD, serwera bazy danych SQL, można połączyć się z serwerem usługi SQL Database. Jednak bardziej ogólnych jest można dodać użytkownika bez uprawnień administratora do usługi Azure AD. Po użytkownik niebędący administratorem służy do łączenia, sekwencji MFA jest wywoływana, jeśli uwierzytelnianie wieloskładnikowe nakłada się na tego użytkownika przez usługę Azure AD.




## <a name="azure-active-directory-authentication-library-adal"></a>Usługa Azure Active Directory Authentication Library (ADAL)

Program C# opiera się na przestrzeń nazw **Microsoft.IdentityModel.Clients.ActiveDirectory**. Klasy dla tej przestrzeni nazw znajdują się w zestawie o tej samej nazwie.

- Aby pobrać i zainstalować biblioteki ADAL zestawu, należy użyć NuGet.
    - [https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

- Dodaj odwołanie do zestawu, do obsługi kompilacji programu C#.




## <a name="sqlauthenticationmethod-enum"></a>Wyliczenie SqlAuthenticationMethod

Jednej przestrzeni nazw, które opiera się na przykład C# jest **System.Data.SqlClient**. Szczególnie ważne jest wyliczenia **SqlAuthenticationMethod**. To wyliczenie ma następujące wartości:

- **SqlAuthenticationMethod.ActiveDirectory*Interactive ***:&nbsp; za pomocą to nazwa użytkownika usługi Azure AD, do osiągnięcia uwierzytelnianie wieloskładnikowe MFA.
    - Ta wartość jest celem niniejszego artykułu. Tworzy środowisko interaktywne, wyświetlając okien dialogowych dla hasła użytkownika, a następnie do weryfikacji uwierzytelniania Wieloskładnikowego, jeśli uwierzytelnianie wieloskładnikowe nakłada się na tego użytkownika.
    - Ta wartość jest dostępnych w programie .NET Framework w wersji 4.7.2.

- **SqlAuthenticationMethod.ActiveDirectory*zintegrowany ***:&nbsp; użyjemy dla *federacyjnych* konta. Dla kont federacyjnych nazwa użytkownika jest znany do domeny Windows. Ta metoda nie obsługuje uwierzytelniania Wieloskładnikowego.

- **SqlAuthenticationMethod.ActiveDirectory*hasło ***:&nbsp; używane dla uwierzytelniania, który wymaga użytkownika usługi Azure AD i hasło użytkownika. Usługa Azure SQL Database przeprowadza uwierzytelnianie. Ta metoda nie obsługuje uwierzytelniania Wieloskładnikowego.




## <a name="prepare-c-parameter-values-from-the-azure-portal"></a>Przygotowanie wartości parametrów języka C# w witrynie Azure portal

Dla pomyślny przebieg programu C# należy przypisać odpowiednie wartości do pola statyczne. Te pola statyczne zachowywać się jak parametry do programu. Pola są wyświetlane w tym miejscu pretend wartościami. Także wyświetlane są lokalizacje w witrynie Azure portal, z której można uzyskać z odpowiednimi wartościami:


| Nazwa pola statycznego | Poudawać wartość | Miejsce w witrynie Azure portal |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "Moja-ulubionego elementu sqldb-svr.database.windows.net" | **Serwery SQL** &gt; **Filtruj według nazwy** |
| AzureAD_UserID | „user9@abc.onmicrosoft.com” | **Usługa Azure Active Directory** &gt; **użytkownika** &gt; **nowy użytkownik-Gość** |
| Initial_DatabaseName | "główną" | **Serwery SQL** &gt; **baz danych SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Usługa Azure Active Directory** &gt; **rejestracje aplikacji**<br /> &nbsp; &nbsp; &gt; **Wyszukaj według nazwy** &gt; **Identyfikatora aplikacji** |
| Element RedirectUri | Nowy identyfikator Uri ("https://bing.com/") | **Usługa Azure Active Directory** &gt; **rejestracje aplikacji**<br /> &nbsp; &nbsp; &gt; **Wyszukaj według nazwy** &gt; *[Your App regis]* &gt;<br /> &nbsp; &nbsp; **Ustawienia** &gt; **RedirectURIs**<br /><br />W tym artykule dowolną prawidłową wartość jest dobrym rozwiązaniem dla elementu RedirectUri. Wartość nie jest tak naprawdę używana w tym przypadku. |
| &nbsp; | &nbsp; | &nbsp; |


Zależnie od konkretnego scenariusza możesz potrzebować nie wartości wszystkich parametrów w powyższej tabeli.




## <a name="run-ssms-to-verify"></a>Uruchom program SSMS, aby sprawdzić

Jest przydatne do uruchomienia programu SQL Server Management Studio (SSMS) przed uruchomieniem programu C#. SSMS uruchomienia sprawdza, czy różne konfiguracje są poprawne. Jakiekolwiek niepowodzenie program C# można następnie zwężenie można po prostu jego kod źródłowy.


#### <a name="verify-sql-database-firewall-ip-addresses"></a>Sprawdź adresy IP zapory bazy danych SQL

Uruchom program SSMS na tym samym komputerze, w tym samym budynku, później uruchomisz program C#. Możesz użyć zależności **uwierzytelniania** tryb uważasz, że jest najprostsza. Jeśli ma żadnego wskazania, że zapory serwera bazy danych nie akceptuje adresu IP, można naprawić, jak pokazano na [reguły zapory na poziomie serwera i na poziomie bazy danych Azure SQL Database](sql-database-firewall-configure.md).


#### <a name="verify-multi-factor-authentication-mfa-for-azure-ad"></a>Zweryfikowanie uwierzytelniania wieloskładnikowego (MFA) dla usługi Azure AD

Ponownie uruchom program SSMS, tym razem z **uwierzytelniania** równa **usługi Active Directory — uniwersalnego z obsługą uwierzytelniania Wieloskładnikowego**. Ta opcja musi mieć SSMS wersji 17.5 lub nowszej.

Aby uzyskać więcej informacji, zobacz [Konfiguruj uwierzytelnianie wieloskładnikowe dla SSMS i Azure AD](sql-database-ssms-mfa-authentication-configure.md).




## <a name="c-code-example"></a>Przykładowy kod w języku C#

Aby skompilować ten przykład C#, należy dodać odwołanie do zestawu biblioteki DLL o nazwie **Microsoft.IdentityModel.Clients.ActiveDirectory**.


#### <a name="reference-documentation"></a>Dokumentacja referencyjna

- **System.Data.SqlClient** przestrzeni nazw:
    - Wyszukiwanie:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient](https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient)
    - Bezpośrednie:&nbsp; [System.Data.Client](https://docs.microsoft.com/dotnet/api/system.data.sqlclient)

- **Microsoft.IdentityModel.Clients.ActiveDirectory** przestrzeni nazw:
    - Wyszukiwanie:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory)
    - Bezpośrednie:&nbsp; [Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory)


#### <a name="c-source-code-in-two-parts"></a>Kod źródłowy C#, w dwóch częściach

&nbsp;

```csharp

using System;    // C# ,  part 1 of 2.

// Add a reference to assembly:  Microsoft.IdentityModel.Clients.ActiveDirectory.DLL
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
        static public string Az_SQLDB_svrName = "<YOUR VALUE HERE>";
        static public string AzureAD_UserID = "<YOUR VALUE HERE>";
        static public string Initial_DatabaseName = "master";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<YOUR VALUE HERE>";
        static public readonly Uri RedirectUri = new Uri("<YOUR VALUE HERE>");

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
    } // EOClass Program .

```

&nbsp;

#### <a name="second-half-of-the-c-program"></a>Drugą połowę programu C#

Dla lepszej wizualizacji do wyświetlenia program C# jest dzielony na dwóch bloków kodu. Aby uruchomić program, Wklej razem bloki kodu dwa.

&nbsp;

```csharp

    // C# ,  part 2 of 2 ,  to concatenate below part 1.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2 .
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
    } // EOClass ActiveDirectoryAuthProvider .
} // EONamespace.  End of entire program source code.

```

&nbsp;

#### <a name="actual-test-output-from-c"></a>Dane wyjściowe testu rzeczywistych za pomocą języka C#

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

&nbsp;


## <a name="next-steps"></a>Kolejne kroki

- [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator)

