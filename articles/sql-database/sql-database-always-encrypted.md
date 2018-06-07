---
title: 'Zawsze zaszyfrowane: Azure SQL Database — magazynu certyfikatów systemu Windows | Dokumentacja firmy Microsoft'
description: W tym artykule przedstawiono sposób zabezpieczyć poufne dane z bazy danych SQL przy użyciu szyfrowania bazy danych przy użyciu zawsze zaszyfrowane kreatora w programu SQL Server Management Studio (SSMS). On również przedstawiono sposób przechowywania kluczy szyfrowania w magazynie certyfikatów systemu Windows.
keywords: szyfrowanie danych, szyfrowania sql, szyfrowania bazy danych poufnych danych, zawsze zaszyfrowane
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 25565f2369bc684525a53889b6cd069af5ce32f0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647038"
---
# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-the-windows-certificate-store"></a>Zawsze zaszyfrowane: Ochrona poufnych danych w bazie danych SQL i przechowywania kluczy szyfrowania w magazynie certyfikatów systemu Windows

W tym artykule przedstawiono sposób zabezpieczyć poufne dane z bazy danych SQL przy użyciu szyfrowania bazy danych przy użyciu [zawsze szyfrowany Kreator](https://msdn.microsoft.com/library/mt459280.aspx) w [programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). On również przedstawiono sposób przechowywania kluczy szyfrowania w magazynie certyfikatów systemu Windows.

Zawsze zaszyfrowane jest nową technologią szyfrowania danych w bazie danych SQL Azure i programu SQL Server, która pomaga chronić poufnych danych przechowywanych na serwerze podczas przenoszenia między klientem a serwerem i danych w trakcie używania, zapewnienie poufnych danych, nigdy nie jest wyświetlany jako zwykłego tekstu w systemie bazy danych. Po dane są szyfrowane, tylko aplikacje klienckie lub serwery aplikacji, które mają dostęp do kluczy można uzyskać dostępu do danych w postaci zwykłego tekstu. Aby uzyskać szczegółowe informacje, zobacz [zawsze zaszyfrowane (aparat bazy danych)](https://msdn.microsoft.com/library/mt163865.aspx).

Po skonfigurowaniu bazy danych zawsze zaszyfrowane, spowoduje utworzenie aplikacji klienckiej w języku C# z programem Visual Studio do pracy z zaszyfrowanych danych.

Wykonaj kroki opisane w tym artykule opisano konfigurowanie zawsze zaszyfrowane dla bazy danych Azure SQL. W tym artykule dowiesz się, jak wykonać następujące zadania:

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

## <a name="create-a-blank-sql-database"></a>Tworzenie pustej bazy danych SQL
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Kliknij przycisk **Utwórz zasób** > **dane i magazyn** > **bazy danych SQL**.
3. Utwórz **puste** bazy danych o nazwie **Clinic** na nowym lub istniejącym serwerze. Aby uzyskać szczegółowe instrukcje dotyczące tworzenia bazy danych w portalu Azure, zobacz [pierwszą bazę danych Azure SQL](sql-database-get-started-portal.md).
   
    ![Tworzenie pustej bazy danych](./media/sql-database-always-encrypted/create-database.png)

Parametry połączenia należy później w samouczku. Po utworzeniu bazy danych, przejdź do nowej bazy danych Clinic i skopiuj parametry połączenia. Parametry połączenia można uzyskać w dowolnym momencie, ale łatwo skopiować go w portalu Azure.

1. Kliknij przycisk **baz danych SQL** > **Clinic** > **Pokaż parametry połączenia bazy danych**.
2. Skopiuj parametry połączenia dla **ADO.NET**.
   
    ![Skopiuj parametry połączenia](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Nawiązywanie połączenia z bazą danych za pomocą programu SSMS
Otwórz SSMS i nawiąż połączenie z serwerem z bazą danych Clinic.

1. Otwórz program SSMS. (Kliknij **Connect** > **aparatu bazy danych** otworzyć **Połącz z serwerem** okna, jeśli nie jest otwarty).
2. Wprowadź nazwę serwera i poświadczenia. Nazwa serwera można znaleźć w bloku bazy danych SQL i w parametrach połączenia skopiowane wcześniej. Wpisz nazwy serwerów pełną, w tym *database.windows.net*.
   
    ![Skopiuj parametry połączenia](./media/sql-database-always-encrypted/ssms-connect.png)

Jeśli **nowej reguły zapory** okno, zaloguj się na platformie Azure oraz umożliwiają SSMS Utwórz nową regułę zapory dla Ciebie.

## <a name="create-a-table"></a>Tworzenie tabeli
W tej sekcji utworzysz tabelę do przechowywania danych pacjenta. Będzie to normalna tabeli początkowo — skonfiguruje szyfrowania w następnej sekcji.

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
SSMS udostępnia kreatora, aby łatwo skonfigurować zawsze zaszyfrowane poprzez ustawienie CMK CEK i zaszyfrowanych kolumn dla Ciebie.

1. Rozwiń węzeł **baz danych** > **Clinic** > **tabel**.
2. Kliknij prawym przyciskiem myszy **pacjentów** tabeli i wybierz **szyfrowania kolumn** aby otworzyć Kreatora zawsze szyfrowane:
   
    ![Szyfrowania kolumn](./media/sql-database-always-encrypted/encrypt-columns.png)

Kreator zawsze zaszyfrowane zawiera następujące sekcje: **kolumn wybór**, **konfiguracji klucza głównego** (CMK), **weryfikacji**, i **Podsumowanie**.

### <a name="column-selection"></a>Wybór kolumny
Kliknij przycisk **dalej** na **wprowadzenie** strony, aby otworzyć **kolumn wybór** strony. Na tej stronie możesz wybrać kolumny, które mają być szyfrowane, [typ szyfrowania i jakie klucza szyfrowania kolumny (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) do użycia.

Szyfrowanie **SSN** i **Data urodzenia** informacje dotyczące każdego pacjenta. **SSN** kolumny użyje deterministyczne szyfrowania, który obsługuje równości wyszukiwań, sprzężeń i Grupuj według. **Data urodzenia** kolumny użyje losowego szyfrowania, który nie obsługuje operacji.

Ustaw **typ szyfrowania** dla **SSN** kolumny **Deterministic** i **Data urodzenia** kolumny **Randomized**. Kliknij przycisk **Dalej**.

![Szyfrowania kolumn](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Konfiguracja klucza głównego
**Konfiguracji klucza głównego** strona jest gdzie konfigurowanie Twojego CMK i wybierz dostawcę magazynu kluczy przechowywania CMK. Obecnie można przechowywać CMK w magazynie certyfikatów systemu Windows, usługi Azure Key Vault lub sprzętowego modułu zabezpieczeń (HSM). W tym samouczku przedstawiono sposób przechowywania kluczy w magazynie certyfikatów systemu Windows.

Sprawdź, czy **magazynu certyfikatów systemu Windows** jest zaznaczone, a następnie kliknij przycisk **dalej**.

![Konfiguracja klucza głównego](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Walidacja
Można teraz szyfrowanie kolumny lub zapisać skrypt programu PowerShell, aby uruchomić później. W tym samouczku, wybierz **przystąpić do zakończenia teraz** i kliknij przycisk **dalej**.

### <a name="summary"></a>Podsumowanie
Sprawdź, czy ustawienia są wszystkie prawidłowe i kliknij przycisk **Zakończ** aby ukończyć instalację na zawsze szyfrowane.

![Podsumowanie](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>Sprawdź akcje Kreatora
Po zakończeniu pracy Kreatora bazy danych jest skonfigurowane dla zawsze szyfrowane. Kreator wykonywane następujące akcje:

* Utworzyć CMK.
* Utworzyć CEK.
* Skonfigurowane zaznaczonych kolumnach do szyfrowania. Twoje **pacjentów** tabeli nie ma obecnie żadnych danych, ale wszystkie istniejące dane w zaznaczonych kolumnach jest teraz zaszyfrowana.

Możesz sprawdzić, przechodząc do tworzenia kluczy w programie SSMS **Clinic** > **zabezpieczeń** > **zawsze zaszyfrowane klucze**. Możesz teraz przeglądać z wygenerowana Kreatora nowych kluczy.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Tworzenie aplikacji klienckiej, która współdziała z zaszyfrowanych danych
Teraz, gdy skonfigurowano zawsze zaszyfrowane, można utworzyć aplikację, która wykonuje *wstawia* i *wybiera* w przypadku kolumn zaszyfrowanych. Aby pomyślnie uruchomić przykładowej aplikacji, należy uruchomić ją na tym samym komputerze którym uruchomiono kreatora zawsze szyfrowane. Aby uruchomić aplikację na innym komputerze, należy wdrożyć certyfikaty zawsze zaszyfrowane do komputera z uruchomionym aplikacji klienckiej.  

> [!IMPORTANT]
> Aplikacja musi używać [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) obiekty podczas przekazywania do serwera z kolumnami zawsze zaszyfrowane dane w postaci zwykłego tekstu. Przekazywanie wartości literałów bez przy użyciu obiektów SqlParameter spowoduje Wystąpił wyjątek.
> 
> 

1. Otwórz program Visual Studio i utworzyć nową aplikację konsoli języka C#. Upewnij się, że projekt ma ustawioną wartość **.NET Framework 4.6** lub nowszym.
2. Nazwij projekt **AlwaysEncryptedConsoleApp** i kliknij przycisk **OK**.

![Nową aplikację konsoli](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modyfikowanie parametrów połączenia, aby włączyć zawsze zaszyfrowane
W tej sekcji wyjaśniono, jak włączyć zawsze zaszyfrowane w ciągu połączenia bazy danych. Należy zmodyfikować aplikację konsoli utworzonego w następnej sekcji "Zawsze zaszyfrowane Przykładowa aplikacja konsolowa".

Aby włączyć zawsze zaszyfrowane, musisz dodać **ustawienie szyfrowania kolumny** — słowo kluczowe połączenia ciąg i ustaw ją na **włączone**.

Możesz ustawić bezpośrednio w parametrach połączenia lub można ją ustawić za pomocą [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Przykładową aplikację w następnej sekcji przedstawiono sposób użycia **SqlConnectionStringBuilder**.

> [!NOTE]
> Jest to jedyna różnica wymagane w określonych aplikacji klienckiej zawsze szyfrowane. Jeśli masz istniejącą aplikację przechowujący zewnętrznie parametrach połączenia (to znaczy w pliku konfiguracyjnym), można włączyć zawsze zaszyfrowane, bez konieczności zmieniania kodu.
> 
> 

### <a name="enable-always-encrypted-in-the-connection-string"></a>Włącz zawsze zaszyfrowane w parametrach połączenia
Dodaj następujące słowo kluczowe parametrów połączenia:

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Włącz zawsze zaszyfrowane przy SqlConnectionStringBuilder
Poniższy kod przedstawia sposób włączania zawsze szyfrowane przez ustawienie [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) do [włączone](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;



## <a name="always-encrypted-sample-console-application"></a>Zawsze zaszyfrowane Przykładowa aplikacja konsolowa
W przykładzie pokazano, jak:

* Zmodyfikuj parametry połączenia, aby włączyć zawsze szyfrowane.
* Wstawianie danych w zaszyfrowanych kolumn.
* Wybierz rekord filtrując określonej wartości w zaszyfrowanej kolumny.

Zastąp zawartość **Program.cs** następującym kodem. Zastąp ciąg połączenia dla zmiennej globalnej connectionString w wierszu bezpośrednio nad metody Main ciągu prawidłowe połączenie z portalu Azure. Jest to tylko zmiany, które należy wprowadzić ten kod.

Uruchom aplikację, aby wyświetlić zawsze zaszyfrowane w akcji.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;

    namespace AlwaysEncryptedConsoleApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Replace with your connection string";

        static void Main(string[] args)
        {
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

            InsertPatient(new Patient() {
                SSN = "999-99-0001", FirstName = "Orlando", LastName = "Gee", BirthDate = DateTime.Parse("01/04/1964") });
            InsertPatient(new Patient() {
                SSN = "999-99-0002", FirstName = "Keith", LastName = "Harris", BirthDate = DateTime.Parse("06/20/1977") });
            InsertPatient(new Patient() {
                SSN = "999-99-0003", FirstName = "Donna", LastName = "Carreras", BirthDate = DateTime.Parse("02/09/1973") });
            InsertPatient(new Patient() {
                SSN = "999-99-0004", FirstName = "Janet", LastName = "Gates", BirthDate = DateTime.Parse("08/31/1985") });
            InsertPatient(new Patient() {
                SSN = "999-99-0005", FirstName = "Lucy", LastName = "Harrington", BirthDate = DateTime.Parse("05/06/1993") });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
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
Można szybko sprawdzić, czy dane na serwerze są szyfrowane, badając **pacjentów** danych za pomocą narzędzia SSMS. (Użyj tego połączenia gdzie ustawienie szyfrowania kolumny nie jest jeszcze włączone).

Uruchom następujące zapytanie w bazie danych Clinic.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Widać, że zaszyfrowanych kolumn nie zawierają żadnych danych w postaci zwykłego tekstu.

   ![Nową aplikację konsoli](./media/sql-database-always-encrypted/ssms-encrypted.png)

Aby uzyskać dostęp do danych w postaci zwykłego tekstu przy użyciu narzędzia SSMS, można dodać **ustawienie szyfrowania kolumny = włączone** parametru do połączenia.

1. W programie SSMS, kliknij prawym przyciskiem myszy serwer w **Eksplorator obiektów**, a następnie kliknij przycisk **rozłączenia**.
2. Kliknij przycisk **Connect** > **aparatu bazy danych** otworzyć **Połącz z serwerem** okna, a następnie kliknij przycisk **opcje**.
3. Kliknij przycisk **dodatkowe parametry połączenia** i typ **ustawienie szyfrowania kolumny = włączone**.
   
    ![Nową aplikację konsoli](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. Uruchom następujące zapytanie w **Clinic** bazy danych.
   
        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   
     Można przeglądać dane w postaci zwykłego tekstu w zaszyfrowanych kolumn.

    ![Nową aplikację konsoli](./media/sql-database-always-encrypted/ssms-plaintext.png)



> [!NOTE]
> Jeśli łączysz się z SSMS (lub dowolnego klienta) z innego komputera, nie będzie miał dostęp do kluczy szyfrowania i nie będzie możliwe do odszyfrowania danych.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu bazy danych, która używa zawsze zaszyfrowane, można wykonać następujące czynności:

* W tym przykładzie należy uruchomić na innym komputerze. Nie będzie on mieć dostęp do kluczy szyfrowania, więc nie ma dostępu do danych w postaci zwykłego tekstu ani nie będzie działać prawidłowo.
* [Obracanie i wyczyścić klucze](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migracja danych, które jest już zaszyfrowane za pomocą zawsze zaszyfrowane](https://msdn.microsoft.com/library/mt621539.aspx).
* [Wdrażanie certyfikatów zawsze zaszyfrowane na innych komputerach klienckich](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (zobacz sekcję "Co certyfikatów dostępnych do aplikacji i użytkowników").

## <a name="related-information"></a>Informacje pokrewne
* [Zawsze zaszyfrowane (Programowanie klienta)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Przezroczystego szyfrowania danych](https://msdn.microsoft.com/library/bb934049.aspx)
* [Szyfrowanie serwera SQL](https://msdn.microsoft.com/library/bb510663.aspx)
* [Kreator zawsze zaszyfrowane](https://msdn.microsoft.com/library/mt459280.aspx)
* [Blog zawsze zaszyfrowane](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

