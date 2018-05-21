---
title: ActiveDirectoryInteractive łączy do bazy danych SQL | Dokumentacja firmy Microsoft
description: Przykład kodu C#, wraz z wyjaśnieniem, połączenie z bazą danych SQL Azure przy użyciu trybu SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: active directory
ms.topic: article
ms.date: 04/06/2018
ms.author: MirekS
ms.reviewer: GeneMi
ms.openlocfilehash: 6489fb5630e1990c942b461859650e2e469cda73
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="use-activedirectoryinteractive-mode-to-connect-to-azure-sql-database"></a>Użyj trybu ActiveDirectoryInteractive nawiązać połączenia z bazą danych SQL Azure

Ten artykuł zawiera do uruchomienia C# przykładowego kodu umożliwiająca nawiązanie połączenia bazy danych SQL Microsoft Azure. Program C# tryb interaktywny uwierzytelniania, która obsługuje usługę Azure AD uwierzytelnianie wieloskładnikowe (MFA). Na przykład próba połączenia mogą zawierać kod weryfikacyjny wysyłane na telefon komórkowy.

Aby uzyskać więcej informacji o obsłudze usługi MFA dla narzędzi SQL, zobacz [pomocy technicznej usługi Azure Active Directory w programu SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).




## <a name="sqlauthenticationmethod-activedirectoryinteractive-enum-value"></a>SqlAuthenticationMethod. Wartość wyliczenia ActiveDirectoryInteractive

W programie .NET Framework w wersji 4.7.2, wyliczenia [ **SqlAuthenticationMethod** ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) ma nową wartość **. ActiveDirectoryInteractive**. Gdy jest używana przez klienta programu C#, ta wartość enum kieruje używanie usługi Azure AD trybie interakcyjnym Obsługa uwierzytelniania Wieloskładnikowego do uwierzytelniania usługi Azure SQL Database w systemie. Użytkownik, który następnie uruchamia program widzi następujących okien dialogowych:

1. Okno dialogowe, który wyświetla nazwę użytkownika usługi Azure AD i która poprosi o podanie hasła użytkownika usługi Azure AD.
    - To okno dialogowe nie będzie wyświetlany, jeśli żadne hasło nie jest konieczne. Hasło nie jest niezbędny w przypadku domeny użytkownika jest Sfederowane przy użyciu usługi Azure AD.

    Jeśli uwierzytelnianie wieloskładnikowe nakłada się na użytkownika przez zasady ustawione w usłudze Azure AD, następujące okna dialogowe są wyświetlane obok.

2. Tylko podczas pierwszego ogólnego zmniejszania się scenariusz MFA system zostanie wyświetlone dodatkowe okno. Okno dialogowe pyta o numer telefonu komórkowego, do którego będą wysyłane wiadomości SMS. Każdy komunikat zawiera *kod weryfikacyjny* który użytkownik musi wprowadzić w następnym oknie dialogowym.

3. Okno dialogowe z pytaniem, czy kod weryfikacyjny MFA, które system został wysłany na telefon komórkowy.

Aby uzyskać informacje o sposobie konfigurowania usługi Azure AD, aby wymagać uwierzytelniania MFA, zobacz [wprowadzenie do korzystania z usługi Azure Multi-Factor Authentication w chmurze](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Zrzuty ekranu z tych okien dialogowych, zobacz [skonfiguruj usługę Multi-Factor authentication dla programu SQL Server Management Studio i Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Naszą stronę wyszukiwania ogólnego dla wszystkich rodzajów interfejsów API architektury .NET Framework jest dostępna na poniższe łącze, aby nasze przydatną **przeglądarki interfejs API .NET** narzędzie:
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> Przez dodanie nazwy typu opcjonalny dołączany **? termin =** parametr strony wyszukiwania może mieć naszych wyników gotowy i czeka firmie Microsoft:
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)


## <a name="preparations-for-c-by-using-the-azure-portal"></a>Przygotowania C# za pomocą portalu Azure

Przyjęto założenie, że masz już [serwera bazy danych SQL Azure utworzony](sql-database-get-started-portal.md) i dostępne.


### <a name="a-create-an-app-registration"></a>A. Tworzenie rejestracji aplikacji

Aby używać uwierzytelniania usługi Azure AD, program kliencki C# należy podać identyfikator GUID jako *clientId* po programie podejmuje próbę nawiązania połączenia. Kończenie pracy rejestracji aplikacji generuje i wyświetla identyfikator GUID w portalu Azure, oznaczone jako **identyfikator aplikacji**. Kroki nawigacji są następujące:

1. Azure portal &gt; **usługi Azure Active Directory** &gt; **rejestracji aplikacji**

    ![Rejestracja aplikacji](media\active-directory-interactive-connect-azure-sql-db\sshot-create-app-registration-b20.png)

2. **Identyfikator aplikacji** wartość jest wygenerowany i wyświetlony.

    ![Wyświetla identyfikator aplikacji](media\active-directory-interactive-connect-azure-sql-db\sshot-application-id-app-regis-mk49.png)

3. **Aplikacja zarejestrowanych** &gt; **ustawienia** &gt; **wymagane uprawnienia** &gt; **Dodaj**

    ![Ustawienia uprawnień dla aplikacji w zarejestrowany](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

4. **Wymagane uprawnienia** &gt; **dostępu Dodaj interfejsu API** &gt; **wybierz interfejs API** &gt; **bazy danych SQL Azure**

    ![Dodaj dostęp do interfejsu API, bazy danych SQL Azure](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

5. **Dostępu do interfejsu API** &gt; **wybierz uprawnienia** &gt; **delegowane uprawnienia**

    ![Delegowanie uprawnień do interfejsu API, bazy danych SQL Azure](media\active-directory-interactive-connect-azure-sql-db\sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)


### <a name="b-set-azure-ad-admin-on-your-sql-database-server"></a>B. Ustawić administratora usługi Azure AD na serwerze bazy danych SQL

Każdy serwer bazy danych SQL Azure ma własną SQL serwera logicznego usługi Azure AD. W naszym C# scenariuszu administrator usługi Azure AD musi być ustawiona dla serwera Azure SQL.

1. **SQL Server** &gt; **administratora usługi Active Directory** &gt; **ustawić administratora**

    - Aby uzyskać więcej informacji na temat usługi Azure AD Administratorzy i użytkownicy bazy danych SQL Azure, zobacz zrzuty ekranu w [Konfigurowanie i zarządzanie nimi uwierzytelniania usługi Azure Active Directory z bazą danych SQL](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server), w sekcji dotyczącej **obsługi administracyjnej platformy Azure Active Directory administratorem serwera bazy danych SQL Azure**.


### <a name="c-prepare-an-azure-ad-user-to-connect-to-a-specific-database"></a>C. Przygotowanie użytkownika usługi Azure AD, aby połączyć z określoną bazą danych

W usłudze Azure AD, które są specyficzne dla serwera bazy danych SQL Azure można dodać użytkownika, który ma dostęp do określonej bazy danych.

Aby uzyskać więcej informacji, zobacz [Użyj Azure uwierzytelnianie usługi Active Directory do uwierzytelniania przy użyciu bazy danych SQL, zarządzane wystąpienia lub SQL Data Warehouse](sql-database-aad-authentication.md).


### <a name="d-add-a-non-admin-user-to-azure-ad"></a>D. Dodaj użytkownika bez uprawnień administratora do usługi Azure AD

Administrator usługi Azure AD z bazy danych programu SQL server może służyć do łączenia się z serwerem bazy danych SQL. Jednak bardziej ogólne przypadek jest dodanie użytkownika niebędącego administratorem do usługi Azure AD. W przypadku użytkownika niebędącego administratorem nawiązać sekwencji MFA jest wywoływana, jeśli uwierzytelnianie wieloskładnikowe nakłada się na tego użytkownika przez usługę Azure AD.




## <a name="azure-active-directory-authentication-library-adal"></a>Azure Active Directory Authentication Library (ADAL)

C# program opiera się na przestrzeń nazw **Microsoft.IdentityModel.Clients.ActiveDirectory**. Klasy dla tej przestrzeni nazw znajdują się w zestawie o tej samej nazwie.

- Użycie narzędzia NuGet, aby pobrać i zainstalować zestaw biblioteki ADAL.
    - [https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

- Dodaj odwołanie do zestawu, do obsługi kompilacji programu C#.




## <a name="sqlauthenticationmethod-enum"></a>Wyliczenia SqlAuthenticationMethod

Jeden przestrzeni nazw, która opiera się na przykład C# jest **System.Data.SqlClient**. Szczególnie ważne jest wyliczenia **SqlAuthenticationMethod**. To wyliczenie ma następujące wartości:

- **SqlAuthenticationMethod.ActiveDirectory*Interactive ***:&nbsp; za pomocą to nazwa użytkownika usługi Azure AD umożliwia uwierzytelnianie wieloskładnikowe MFA.
    - Ta wartość jest celem niniejszego artykułu. Tworzy interaktywna przez wyświetlanie okien dialogowych hasło użytkownika, a następnie do weryfikacji uwierzytelniania MFA, jeśli uwierzytelnianie wieloskładnikowe nakłada się na tego użytkownika.
    - Ta wartość jest dostępnych w programie .NET Framework wersja 4.7.2.

- **SqlAuthenticationMethod.ActiveDirectory*zintegrowany ***:&nbsp; go użyć do *federacyjnych* konta. Dla kontem federacyjnym nosi nazwę użytkownika do domeny systemu Windows. Ta metoda nie obsługuje uwierzytelnianie wieloskładnikowe.

- **SqlAuthenticationMethod.ActiveDirectory*hasło ***:&nbsp; to jest używany do uwierzytelniania, która wymaga usługi Azure AD użytkownika i hasło użytkownika. Baza danych SQL Azure przeprowadza uwierzytelnianie. Ta metoda nie obsługuje uwierzytelnianie wieloskładnikowe.




## <a name="prepare-c-parameter-values-from-the-azure-portal"></a>Przygotowanie C# wartości parametrów z portalu Azure

Dla pomyślnego uruchomienia programu C# należy przypisać odpowiednie wartości do pola statycznego. Te pola statyczne działać tak jak parametry do programu. Pola są wyświetlane tutaj pretend wartościami. Także wyświetlane są lokalizacje w portalu Azure, z której można uzyskać odpowiednie wartości:


| Nazwa pola statyczne | Podawać wartości | Jeśli w portalu Azure |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "Moje ulubionego sqldb-svr.database.windows.net" | **Serwery SQL** &gt; **filtrować według nazwy** |
| AzureAD_UserID | „user9@abc.onmicrosoft.com” | **Usługa Azure Active Directory** &gt; **użytkownika** &gt; **nowego użytkownika gościa** |
| Initial_DatabaseName | "główny" | **Serwery SQL** &gt; **bazy danych SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Usługa Azure Active Directory** &gt; **rejestracji aplikacji**<br /> &nbsp; &nbsp; &gt; **Wyszukaj według nazwy** &gt; **identyfikator aplikacji** |
| redirectUri | Nowy identyfikator Uri ("https://bing.com/") | **Usługa Azure Active Directory** &gt; **rejestracji aplikacji**<br /> &nbsp; &nbsp; &gt; **Wyszukaj według nazwy** &gt; *[Your App regis]* &gt;<br /> &nbsp; &nbsp; **Ustawienia** &gt; **RedirectURIs**<br /><br />W tym artykule wystarcza wszystkie prawidłowe wartości dla RedirectUri. Wartość nie jest w rzeczywistości używana w tym przypadku. |
| &nbsp; | &nbsp; | &nbsp; |


W zależności od konkretnego scenariusza nie może być konieczne wartości wszystkich parametrów w powyższej tabeli.




## <a name="run-ssms-to-verify"></a>Uruchom narzędzia SSMS, aby sprawdzić

Jest przydatne do uruchomienia programu SQL Server Management Studio (SSMS) przed uruchomieniem programu C#. SSMS Uruchom sprawdza, czy różnych konfiguracji są poprawne. Następnie jakiekolwiek niepowodzenie C# program może być narrows tylko jej kodu źródłowego.


#### <a name="verify-sql-database-firewall-ip-addresses"></a>Sprawdź adres IP zapory bazy danych SQL

Uruchom narzędzia SSMS na tym samym komputerze, w tym samym budynku później uruchomisz program C#. Można użyć zależności **uwierzytelniania** tryb uważasz, że jest najprostsza. W przypadku oznaczenie Zapora serwera bazy danych nie akceptuje adresu IP, możesz rozwiązać ten problem, jak pokazano w [reguły zapory poziomu serwera i bazy danych na poziomie bazy danych SQL Azure](sql-database-firewall-configure.md).


#### <a name="verify-multi-factor-authentication-mfa-for-azure-ad"></a>Zweryfikowanie uwierzytelniania wieloskładnikowego (MFA) dla usługi Azure AD

Ponownie uruchom SSMS, tym razem z **uwierzytelniania** ustawioną **usługi Active Directory - uniwersalnego z obsługą uwierzytelniania Wieloskładnikowego**. Dla tej opcji, musi mieć SSMS wersji 17.5 lub nowszej.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania wieloskładnikowego na SSMS i Azure AD](sql-database-ssms-mfa-authentication-configure.md).




## <a name="c-code-example"></a>Przykład kodu C#

Aby skompilować w tym przykładzie C#, należy dodać odwołanie do zestawu DLL o nazwie **Microsoft.IdentityModel.Clients.ActiveDirectory**.


#### <a name="reference-documentation"></a>Dokumentacja referencyjna

- **System.Data.SqlClient** przestrzeni nazw:
    - Wyszukiwania:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient](https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient)
    - Bezpośrednie:&nbsp; [System.Data.Client](https://docs.microsoft.com/dotnet/api/system.data.sqlclient)

- **Microsoft.IdentityModel.Clients.ActiveDirectory** przestrzeni nazw:
    - Wyszukiwania:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory)
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

#### <a name="second-half-of-the-c-program"></a>Drugiej połowie program C#

Lepiej wyświetlania visual C# program jest podzielony na dwa bloki kodu. Aby uruchomić program, Wklej bloki kodu dwóch razem.

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

#### <a name="actual-test-output-from-c"></a>Wyniki testu rzeczywiste w języku C#

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

