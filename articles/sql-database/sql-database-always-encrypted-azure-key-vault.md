---
title: 'Zawsze zaszyfrowane: Baza danych SQL — usługi Azure Key Vault | Dokumentacja firmy Microsoft'
description: W tym artykule przedstawiono sposób zabezpieczyć poufne dane z bazy danych SQL za pomocą szyfrowania danych przy użyciu zawsze zaszyfrowane kreatora w programie SQL Server Management Studio.
keywords: szyfrowanie klucza szyfrowania danych w chmurze szyfrowania
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: ee03ae8edf4402e64f6b671a478a357369e8780a
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-azure-key-vault"></a>Zawsze zaszyfrowane: Ochrona poufnych danych w bazie danych SQL i przechowywania kluczy szyfrowania w usłudze Azure Key Vault

W tym artykule przedstawiono sposób zabezpieczyć poufne dane z bazy danych SQL z szyfrowania danych przy użyciu [zawsze szyfrowany Kreator](https://msdn.microsoft.com/library/mt459280.aspx) w [programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Zawiera również instrukcje, które będą widoczne sposobu przechowywania każdego klucza szyfrowania w usłudze Azure Key Vault.

Zawsze zaszyfrowane jest nową technologią szyfrowania danych w bazie danych SQL Azure i SQL Server, która pomaga w ochronie poufnych danych przechowywanych na serwerze podczas przepływu między klientem a serwerem, gdy dane są używane. Zawsze zaszyfrowane gwarantuje, że poufne dane nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po skonfigurowaniu szyfrowanie danych tylko aplikacje klienckie lub serwery aplikacji, które mają dostęp do kluczy można uzyskać dostępu do danych w postaci zwykłego tekstu. Aby uzyskać szczegółowe informacje, zobacz [zawsze zaszyfrowane (aparat bazy danych)](https://msdn.microsoft.com/library/mt163865.aspx).

Po skonfigurowaniu bazy danych zawsze zaszyfrowane spowoduje utworzenie aplikacji klienckiej w języku C# z programem Visual Studio do pracy z zaszyfrowanych danych.

Wykonaj kroki opisane w tym artykule i Dowiedz się, jak skonfigurować zawsze zaszyfrowane dla bazy danych Azure SQL. W tym artykule dowiesz się, jak wykonać następujące zadania:

* Użyj Kreatora zawsze zaszyfrowane w programie SSMS, aby utworzyć [zawsze zaszyfrowane klucze](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Utwórz [klucza głównego kolumny (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Utwórz [klucza szyfrowania kolumny (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Tworzenie tabeli bazy danych i szyfrowania kolumn.
* Utwórz aplikację, która wstawia, wybiera i wyświetla dane z zaszyfrowanych kolumn.

## <a name="prerequisites"></a>Wymagania wstępne
W tym samouczku potrzebne są:

* Konto i subskrypcja platformy Azure. Jeśli nie masz, zaloguj się do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) wersji 13.0.700.242 lub nowszej.
* [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) lub nowszej (na komputerze klienckim).
* Program [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* [Program Azure PowerShell](/powershell/azure/overview), w wersji 1.0 lub nowszej. Typ **(Get-Module azure flagą-ListAvailable). Wersja** jakiej wersji programu PowerShell są uruchomione.

## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Włączanie aplikacji klienckiej dostęp do usługi baza danych SQL
Należy włączyć dostęp do usługi SQL Database, konfigurowania aplikacji usługi Azure Active Directory (AAD) i kopiowanie przez aplikację klienta *identyfikator aplikacji* i *klucza* potrzebne do uwierzytelniania aplikacji.

Aby uzyskać *identyfikator aplikacji* i *klucza*, postępuj zgodnie z instrukcjami [utworzyć podmiot zabezpieczeń aplikacji i usług, który ma dostęp do zasobów usługi Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md).

## <a name="create-a-key-vault-to-store-your-keys"></a>Tworzenie klucza magazynu do przechowywania kluczy
Skonfigurowano aplikację klienta, a ma swój identyfikator aplikacji, jest czas na utworzenie magazynu kluczy i skonfigurować jego zasady dostępu, możesz i aplikacji wymaga dostępu do kluczy tajnych w magazynie (zawsze zaszyfrowane kluczy). *Utworzyć*, *uzyskać*, *listy*, *znak*, *Sprawdź*, *wrapKey*, i *unwrapKey* uprawnienia są wymagane do utworzenia nowego klucza głównego kolumny i konfigurowania szyfrowania za pomocą programu SQL Server Management Studio.

Może szybko utworzyć magazyn kluczy, uruchamiając poniższy skrypt. Aby uzyskać dokładniejsze objaśnienie tych poleceń cmdlet i więcej informacji na temat tworzenia i konfigurowania magazynu kluczy, zobacz [wprowadzenie do usługi Azure Key Vault](../key-vault/key-vault-get-started.md).

    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $applicationId = '<application ID from your AAD application>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'


    Login-AzureRmAccount
    $subscriptionId = (Get-AzureRmSubscription -SubscriptionName $subscriptionName).Id
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzureRmKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list




## <a name="create-a-blank-sql-database"></a>Tworzenie pustej bazy danych SQL
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do **Utwórz zasób** > **baz danych** > **bazy danych SQL**.
3. Utwórz **puste** bazy danych o nazwie **Clinic** na nowym lub istniejącym serwerze. Aby uzyskać szczegółowe informacje o sposobie tworzenia bazy danych w portalu Azure, zobacz [pierwszą bazę danych Azure SQL](sql-database-get-started-portal.md).
   
    ![Tworzenie pustej bazy danych](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Konieczne będzie połączenie string później w samouczku, dlatego po utworzeniu bazy danych, przejdź do nowej bazy danych Clinic i skopiuj parametry połączenia. Parametry połączenia można uzyskać w dowolnym momencie, ale łatwo skopiować go w portalu Azure.

1. Przejdź do **baz danych SQL** > **Clinic** > **Pokaż parametry połączenia bazy danych**.
2. Skopiuj parametry połączenia dla **ADO.NET**.
   
    ![Skopiuj parametry połączenia](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Nawiązywanie połączenia z bazą danych za pomocą programu SSMS
Otwórz SSMS i nawiąż połączenie z serwerem z bazą danych Clinic.

1. Otwórz program SSMS. (Przejdź do **Connect** > **aparatu bazy danych** otworzyć **Połącz z serwerem** okna, jeśli nie jest otwarty.)
2. Wprowadź nazwę serwera i poświadczenia. Nazwa serwera można znaleźć w bloku bazy danych SQL i w parametrach połączenia skopiowane wcześniej. Wpisz pełną nazwę serwera, łącznie z *database.windows.net*.
   
    ![Skopiuj parametry połączenia](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Jeśli **nowej reguły zapory** okno, zaloguj się na platformie Azure oraz umożliwiają SSMS Utwórz nową regułę zapory dla Ciebie.

## <a name="create-a-table"></a>Tworzenie tabeli
W tej sekcji utworzysz tabelę do przechowywania danych pacjenta. Nie jest początkowo szyfrowane — skonfiguruj szyfrowania w następnej sekcji.

1. Rozwiń węzeł **baz danych**.
2. Kliknij prawym przyciskiem myszy **Clinic** bazy danych, a następnie kliknij przycisk **nowe zapytanie**.
3. Wklej następujący języka Transact-SQL (T-SQL) do okna nowej kwerendy i **Execute** go.

        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## <a name="encrypt-columns-configure-always-encrypted"></a>Szyfrowania kolumn (Konfigurowanie zawsze szyfrowane)
SSMS udostępnia kreatora, który pomoże Ci łatwe konfigurowanie zawsze zaszyfrowane poprzez ustawienie klucza głównego kolumny klucza szyfrowania kolumny i zaszyfrowanych kolumn dla Ciebie.

1. Rozwiń węzeł **baz danych** > **Clinic** > **tabel**.
2. Kliknij prawym przyciskiem myszy **pacjentów** tabeli i wybierz **szyfrowania kolumn** aby otworzyć Kreatora zawsze szyfrowane:
   
    ![Szyfrowania kolumn](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

Kreator zawsze zaszyfrowane zawiera następujące sekcje: **kolumn wybór**, **konfiguracji klucza głównego**, **weryfikacji**, i **Podsumowanie**.

### <a name="column-selection"></a>Wybór kolumny
Kliknij przycisk **dalej** na **wprowadzenie** strony, aby otworzyć **kolumn wybór** strony. Na tej stronie możesz wybrać kolumny, które mają być szyfrowane, [typ szyfrowania i jakie klucza szyfrowania kolumny (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) do użycia.

Szyfrowanie **SSN** i **Data urodzenia** informacje dotyczące każdego pacjenta. Kolumna SSN użyje deterministyczne szyfrowania, który obsługuje równości wyszukiwań, sprzężeń i Grupuj według. Data urodzenia kolumny użyje losowego szyfrowania, który nie obsługuje operacji.

Ustaw **typ szyfrowania** dla kolumny SSN **Deterministic** i kolumnę Data urodzenia **Randomized**. Kliknij przycisk **Dalej**.

![Szyfrowania kolumn](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Konfiguracja klucza głównego
**Konfiguracji klucza głównego** strona jest gdzie konfigurowanie Twojego CMK i wybierz dostawcę magazynu kluczy przechowywania CMK. Obecnie można przechowywać CMK w magazynie certyfikatów systemu Windows, usługi Azure Key Vault lub sprzętowego modułu zabezpieczeń (HSM).

W tym samouczku przedstawiono sposób przechowywania kluczy w magazynie kluczy Azure.

1. Wybierz **usługi Azure Key Vault**.
2. Wybierz żądaną magazynu kluczy z listy rozwijanej.
3. Kliknij przycisk **Dalej**.

![Konfiguracja klucza głównego](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)

### <a name="validation"></a>Walidacja
Można teraz szyfrowanie kolumny lub zapisać skrypt programu PowerShell, aby uruchomić później. W tym samouczku, wybierz **przystąpić do zakończenia teraz** i kliknij przycisk **dalej**.

### <a name="summary"></a>Podsumowanie
Sprawdź, czy ustawienia są wszystkie prawidłowe i kliknij przycisk **Zakończ** aby ukończyć instalację na zawsze szyfrowane.

![Podsumowanie](./media/sql-database-always-encrypted-azure-key-vault/summary.png)

### <a name="verify-the-wizards-actions"></a>Sprawdź akcje Kreatora
Po zakończeniu pracy Kreatora bazy danych jest skonfigurowane dla zawsze szyfrowane. Kreator wykonywane następujące akcje:

* Utworzony klucza głównego kolumny i zapisana w usłudze Azure Key Vault.
* Utworzony klucz szyfrowania kolumny i zapisana w usłudze Azure Key Vault.
* Skonfigurowane zaznaczonych kolumnach do szyfrowania. W tabeli pacjentów obecnie nie ma danych, ale wszystkie istniejące dane w zaznaczonych kolumnach jest teraz zaszyfrowana.

Sprawdź tworzenia kluczy w programie SSMS, rozwijając **Clinic** > **zabezpieczeń** > **zawsze zaszyfrowane klucze**.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Tworzenie aplikacji klienckiej, która współdziała z zaszyfrowanych danych
Teraz, gdy skonfigurowano zawsze zaszyfrowane, można utworzyć aplikację, która wykonuje *wstawia* i *wybiera* w przypadku kolumn zaszyfrowanych.  

> [!IMPORTANT]
> Aplikacja musi używać [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) obiekty podczas przekazywania do serwera z kolumnami zawsze zaszyfrowane dane w postaci zwykłego tekstu. Przekazywanie wartości literałów bez przy użyciu obiektów SqlParameter spowoduje Wystąpił wyjątek.
> 
> 

1. Otwórz program Visual Studio i utworzyć nowe C# **aplikacji konsoli** (Visual Studio 2015 lub starszym) lub **aplikacji konsoli (.NET Framework)** (Visual Studio 2017 i nowsze). Upewnij się, że projekt ma ustawioną wartość **.NET Framework 4.6** lub nowszym.
2. Nazwij projekt **AlwaysEncryptedConsoleAKVApp** i kliknij przycisk **OK**.
3. Zainstaluj następujące pakiety NuGet, przechodząc do **narzędzia** > **Menedżera pakietów NuGet** > **Konsola Menedżera pakietów**.

Uruchom te dwa wiersze kodu w konsoli Menedżera pakietów.

    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory



## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modyfikowanie parametrów połączenia, aby włączyć zawsze zaszyfrowane
W tej sekcji wyjaśniono, jak włączyć zawsze zaszyfrowane w ciągu połączenia bazy danych.

Aby włączyć zawsze zaszyfrowane, musisz dodać **ustawienie szyfrowania kolumny** — słowo kluczowe połączenia ciąg i ustaw ją na **włączone**.

Możesz ustawić bezpośrednio w parametrach połączenia lub można ją ustawić za pomocą [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Przykładową aplikację w następnej sekcji przedstawiono sposób użycia **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Włącz zawsze zaszyfrowane w parametrach połączenia
Dodaj następujące słowa kluczowego do parametrów połączenia.

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Włącz zawsze zaszyfrowane przy SqlConnectionStringBuilder
Poniższy kod przedstawia sposób włączania zawsze szyfrowane przez ustawienie [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) do [włączone](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="register-the-azure-key-vault-provider"></a>Zarejestruj dostawcę usługi Azure Key Vault
Poniższy kod przedstawia sposób rejestrowania dostawcy usługi Azure Key Vault ze sterownikiem programu ADO.NET.

    private static ClientCredential _clientCredential;

    static void InitializeAzureKeyVaultProvider()
    {
       _clientCredential = new ClientCredential(applicationId, clientKey);

       SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
          new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

       Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
          new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

       providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
       SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
    }



## <a name="always-encrypted-sample-console-application"></a>Zawsze zaszyfrowane Przykładowa aplikacja konsolowa
W przykładzie pokazano, jak:

* Zmodyfikuj parametry połączenia, aby włączyć zawsze szyfrowane.
* Rejestrowanie usługi Azure Key Vault jako dostawcy magazynu kluczy aplikacji.  
* Wstawianie danych w zaszyfrowanych kolumn.
* Wybierz rekord filtrując określonej wartości w zaszyfrowanej kolumny.

Zastąp zawartość **Program.cs** następującym kodem. Zastąp ciąg połączenia dla zmiennej globalnej connectionString w wiersz bezpośrednio poprzedzający metody Main z Twojej prawidłowe parametry połączenia z portalu Azure. Jest to tylko zmiany, które należy wprowadzić ten kod.

Uruchom aplikację, aby wyświetlić zawsze zaszyfrowane w akcji.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

    namespace AlwaysEncryptedConsoleAKVApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";


        static void Main(string[] args)
        {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider()
        {

            _clientCredential = new ClientCredential(applicationId, clientKey);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope)
        {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }



## <a name="verify-that-the-data-is-encrypted"></a>Sprawdź, czy dane są szyfrowane.
Można szybko sprawdzić, czy rzeczywistymi danymi na serwerze są szyfrowane, badając pacjentów danych przy użyciu narzędzia SSMS (przy użyciu tego połączenia gdzie **ustawienie szyfrowania kolumny** nie jest jeszcze włączona).

Uruchom następujące zapytanie w bazie danych Clinic.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Widać, że zaszyfrowanych kolumn nie zawierają żadnych danych w postaci zwykłego tekstu.

   ![Nową aplikację konsoli](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)

Aby uzyskać dostęp do danych w postaci zwykłego tekstu przy użyciu narzędzia SSMS, można dodać *ustawienie szyfrowania kolumny = włączone* parametru do połączenia.

1. W programie SSMS, kliknij prawym przyciskiem myszy serwer w **Eksplorator obiektów** i wybierz polecenie **rozłączenia**.
2. Kliknij przycisk **Connect** > **aparatu bazy danych** otworzyć **Połącz z serwerem** i kliknij **opcje**.
3. Kliknij przycisk **dodatkowe parametry połączenia** i typ **ustawienie szyfrowania kolumny = włączone**.
   
    ![Nową aplikację konsoli](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)
4. Uruchom następujące zapytanie w bazie danych Clinic.
   
        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   
     Można przeglądać dane w postaci zwykłego tekstu w zaszyfrowanych kolumn.

    ![Nową aplikację konsoli](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu bazy danych, która używa zawsze zaszyfrowane, można wykonać następujące czynności:

* [Obracanie i wyczyścić klucze](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migracja danych, które jest już zaszyfrowane za pomocą zawsze zaszyfrowane](https://msdn.microsoft.com/library/mt621539.aspx).

## <a name="related-information"></a>Informacje pokrewne
* [Zawsze zaszyfrowane (Programowanie klienta)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Niewidoczne szyfrowanie danych](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server szyfrowania](https://msdn.microsoft.com/library/bb510663.aspx)
* [Zawsze zaszyfrowane Kreatora](https://msdn.microsoft.com/library/mt459280.aspx)
* [Blog zawsze zaszyfrowane](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

