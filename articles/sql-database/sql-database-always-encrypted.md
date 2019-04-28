---
title: 'Zawsze szyfrowane: Usługa Azure SQL Database — magazyn certyfikatów Windows | Dokumentacja firmy Microsoft'
description: W tym artykule pokazano, jak zabezpieczyć poufne dane w usłudze SQL database przy użyciu szyfrowania bazy danych przez Kreator zawsze szyfrowane w SQL Server Management Studio (SSMS). On również pokazano, jak do przechowywania kluczy szyfrowania w magazynie certyfikatów Windows.
keywords: szyfrowanie danych, szyfrowanie sql, szyfrowanie bazy danych, dane poufne, są zawsze szyfrowane
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviwer: ''
manager: craigg
ms.date: 03/08/2019
ms.openlocfilehash: 5226ec05af95cf305008968cf945070532274ee5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61420076"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-the-windows-certificate-store"></a>Zawsze szyfrowane: Ochrona poufnych danych i przechowywania kluczy szyfrowania w magazynie certyfikatów Windows

W tym artykule pokazano, jak zabezpieczyć poufne dane w usłudze SQL database przy użyciu szyfrowania bazy danych przy użyciu [Kreator zawsze szyfrowane](https://msdn.microsoft.com/library/mt459280.aspx) w [programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). On również pokazano, jak do przechowywania kluczy szyfrowania w magazynie certyfikatów Windows.

Always Encrypted to nowej technologii szyfrowania danych w usłudze Azure SQL Database i programu SQL Server, która pomaga chronić poufne dane przechowywane na serwerze podczas przenoszenia między klientem i serwerem oraz gdy dane są używane, zapewniając poufnych danych nigdy nie jest wyświetlany jako zwykły tekst wewnątrz system bazy danych. Po dane są szyfrowane, tylko aplikacje klienckie lub serwery aplikacji, które mają dostęp do kluczy mogą uzyskać dostęp do danych w postaci zwykłego tekstu. Aby uzyskać szczegółowe informacje, zobacz [Always Encrypted (aparat bazy danych)](https://msdn.microsoft.com/library/mt163865.aspx).

Po skonfigurowaniu bazy danych są zawsze szyfrowane, utworzysz aplikację kliencką w języku C# za pomocą programu Visual Studio do pracy z zaszyfrowanych danych.

Wykonaj kroki opisane w tym artykule, aby dowiedzieć się, jak skonfigurować Always Encrypted dla usługi Azure SQL database. W tym artykule dowiesz się, jak wykonywać następujące zadania:

* Użyj Kreatora zawsze szyfrowane w programie SSMS, aby utworzyć [zawsze zaszyfrowane klucze](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Tworzenie [klucz główny kolumny (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Tworzenie [klucza szyfrowania kolumny (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Tworzenie tabeli bazy danych i szyfrowanie kolumny.
* Utwórz aplikację, która wstawia, wybiera i wyświetla dane z zaszyfrowanych kolumn.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku będą potrzebne:

* Konto i subskrypcja platformy Azure. Jeśli nie masz, należy zasubskrybować [bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) wersji 13.0.700.242 lub nowszej.
* [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) lub nowszej (na komputerze klienckim).
* Program [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="create-a-blank-sql-database"></a>Tworzenie pustej bazy danych SQL

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Kliknij przycisk **Utwórz zasób** > **dane + magazyn** > **bazy danych SQL**.
3. Tworzenie **puste** bazy danych o nazwie **kliniki** na nowy lub istniejący serwer. Aby uzyskać szczegółowe instrukcje dotyczące tworzenia bazy danych w witrynie Azure portal, zobacz [pierwszej bazy danych Azure SQL](sql-database-single-database-get-started.md).

    ![Tworzenie pustej bazy danych](./media/sql-database-always-encrypted/create-database.png)

W dalszej części tego samouczka będą potrzebne parametry połączenia. Po utworzeniu bazy danych, przejdź do nowej bazy danych kliniki i skopiuj parametry połączenia. Parametry połączenia można uzyskać w dowolnym momencie, ale można łatwo skopiować go, gdy jesteś w witrynie Azure portal.

1. Kliknij przycisk **baz danych SQL** > **Clinic** > **Pokaż parametry połączenia bazy danych**.
2. Skopiuj parametry połączenia dla **ADO.NET**.

    ![Kopiowanie parametrów połączenia](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Nawiązywanie połączenia z bazą danych za pomocą programu SSMS

Otwórz aplikację SSMS i połączyć się z serwerem z bazą danych okulistycznej.

1. Otwórz program SSMS. (Kliknij **Connect** > **aparatu bazy danych** otworzyć **Połącz z serwerem** okna, jeśli nie jest otwarty).
2. Wprowadź nazwę serwera i poświadczenia. Nazwa serwera można znaleźć w bloku bazy danych SQL i parametry połączenia skopiowane wcześniej. Typ w tym nazwa kompletnego serwera *database.windows.net*.

    ![Kopiowanie parametrów połączenia](./media/sql-database-always-encrypted/ssms-connect.png)

Jeśli **nowej reguły zapory** zostanie wyświetlone okno dialogowe, zaloguj się do platformy Azure, dzięki czemu program SSMS, Utwórz nową regułę zapory dla Ciebie.

## <a name="create-a-table"></a>Tworzenie tabeli

W tej sekcji utworzysz tabelę do przechowywania danych pacjentów. Jest to normalne tabeli początkowo — skonfigurujesz szyfrowania w następnej sekcji.

1. Rozwiń **baz danych**.
2. Kliknij prawym przyciskiem myszy **Clinic** bazy danych, a następnie kliknij przycisk **nowe zapytanie**.
3. Wklej następujących instrukcji języka Transact-SQL (T-SQL) w nowym oknie zapytania i **Execute** go.

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

## <a name="encrypt-columns-configure-always-encrypted"></a>Szyfrowanie kolumn (Konfigurowanie funkcji Always Encrypted)

SSMS udostępnia kreatora łatwe konfigurowanie funkcji Always Encrypted poprzez skonfigurowanie CMK CEK i zaszyfrowanych kolumn dla Ciebie.

1. Rozwiń **baz danych** > **Clinic** > **tabel**.
2. Kliknij prawym przyciskiem myszy **pacjentów** tabeli, a następnie wybierz pozycję **szyfrowania kolumn** aby otworzyć Kreatora zawsze szyfrowane:

    ![Szyfrowanie kolumn](./media/sql-database-always-encrypted/encrypt-columns.png)

Kreator zawsze szyfrowane zawiera następujące sekcje: **Wybór kolumn**, **konfiguracji klucza głównego** (CMK) **weryfikacji**, i **Podsumowanie**.

### <a name="column-selection"></a>Wybór kolumn

Kliknij przycisk **dalej** na **wprowadzenie** strony, aby otworzyć **wybór kolumn** strony. Na tej stronie będą wybierz kolumny, które mają być szyfrowane, [typ szyfrowania i jakie klucza szyfrowania kolumny (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) do użycia.

Szyfruj **SSN** i **datę urodzenia** informacji dla każdego pacjenta. **SSN** kolumny użyje deterministyczne szyfrowanie, które obsługuje wyszukiwań równości, sprzężeń i Grupuj według. **Datę urodzenia** kolumny będzie używać szyfrowania losowego, który nie obsługuje operacji.

Ustaw **typ szyfrowania** dla **SSN** kolumny **Deterministic** i **datę urodzenia** kolumny **Randomized** . Kliknij przycisk **Dalej**.

![Szyfrowanie kolumn](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Konfiguracja klucza głównego

**Konfiguracji klucza głównego** strona jest gdzie skonfigurować swoje CMK i wybierz dostawcę magazynu kluczy przechowywania CMK. Obecnie CMK można przechowywać w magazynie certyfikatów Windows, usługi Azure Key Vault lub sprzętowego modułu zabezpieczeń (HSM). W tym samouczku przedstawiono sposób przechowywania kluczy w magazynie certyfikatów Windows.

Upewnij się, że **magazynu certyfikatów Windows** jest zaznaczone, a następnie kliknij przycisk **dalej**.

![Konfiguracja klucza głównego](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Walidacja

Można teraz szyfrowanie kolumny lub Zapisz skrypt programu PowerShell, aby uruchomić później. Na potrzeby tego samouczka wybierz **przejść do Zakończ teraz** i kliknij przycisk **dalej**.

### <a name="summary"></a>Podsumowanie

Sprawdź, czy ustawienia są poprawne, a następnie kliknij przycisk **Zakończ** aby ukończyć instalację dla funkcji Always Encrypted.

![Podsumowanie](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>Sprawdź akcje Kreatora

Po zakończeniu pracy Kreatora bazy danych skonfigurowano dla funkcji Always Encrypted. Kreator wykonywane następujące akcje:

* Utworzony CMK.
* Utworzony CEK.
* Skonfigurowane zaznaczonych kolumnach do szyfrowania. Twoje **pacjentów** tabeli obecnie nie ma żadnych danych, ale wszystkie istniejące dane w zaznaczonych kolumnach są teraz szyfrowane.

Tworzenie kluczy w programie SSMS można sprawdzić, przechodząc do **Clinic** > **zabezpieczeń** > **zawsze zaszyfrowane klucze**. Będą teraz widoczne nowe klucze, które kreator automatycznie wygenerowanego.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Utwórz aplikację kliencką, która współdziała z zaszyfrowanych danych

Skoro zdefiniowano Always Encrypted, można utworzyć aplikację, która wykonuje *wstawia* i *wybiera* dotycząca zaszyfrowanych kolumn. Aby pomyślnie uruchomić przykładową aplikację, należy uruchomić go na tym samym komputerze gdzie został uruchomiony Kreator zawsze szyfrowane. Aby uruchomić aplikację na innym komputerze, należy wdrożyć certyfikaty są zawsze szyfrowane, do komputera z uruchomioną aplikację kliencką.  

> [!IMPORTANT]
> Aplikacja musi używać [parametr SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) obiekty podczas przekazywania danych w postaci zwykłego tekstu na serwerze przy użyciu funkcji zawsze zaszyfrowane kolumny. Przekazanie wartości literałów bez korzystania z obiektów parametr SqlParameter spowoduje wyjątek.

1. Otwórz program Visual Studio i Utwórz nową aplikację konsoli C#. Upewnij się, że projekt jest ustawiona na **.NET Framework 4.6** lub nowszej.
2. Nadaj projektowi nazwę **AlwaysEncryptedConsoleApp** i kliknij przycisk **OK**.

![Nową aplikację konsoli](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Zmodyfikuj parametry połączenia umożliwiające Always Encrypted

W tej sekcji opisano sposób włączania funkcji zawsze zaszyfrowane w ciągu połączenia bazy danych. Należy zmodyfikować aplikację konsoli, nowo utworzoną w następnej sekcji "Always Encrypted Przykładowa aplikacja konsolowa."

Aby włączyć Always Encrypted, musisz dodać **ustawienie szyfrowania kolumny** — słowo kluczowe do połączenia ciągu i ustaw ją na **włączone**.

Możesz ustawić bezpośrednio w parametrach połączenia lub jest on ustawiany za pomocą [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Przykładowa aplikacja w następnej sekcji pokazano, jak **SqlConnectionStringBuilder**.

> [!NOTE]
> Jest to jedyna zmiana wymagane w aplikacji klienckiej określonych funkcji zawsze zaszyfrowane. Jeśli masz istniejącą aplikację, która przechowuje zewnętrznie jego parametry połączenia (czyli w pliku konfiguracji), można włączyć funkcji Always Encrypted bez zmieniania kodu.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Włączanie funkcji zawsze zaszyfrowane w parametrach połączenia

Dodaj następujące słowo kluczowe do parametrów połączenia:

    Column Encryption Setting=Enabled

### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Włącz zawsze zaszyfrowane za pomocą SqlConnectionStringBuilder

Poniższy kod przedstawia sposób włączania funkcji Always Encrypted, ustawiając [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) do [włączone](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="always-encrypted-sample-console-application"></a>Zawsze zaszyfrowane Przykładowa aplikacja konsolowa

Niniejszy przykład pokazuje, jak:

* Zmodyfikuj parametry połączenia umożliwiające Always Encrypted.
* Wstawianie danych zaszyfrowanych kolumn.
* Zaznacz rekord, filtrując dla określonej wartości w zaszyfrowanej kolumny.

Zastąp zawartość pliku **Program.cs** poniższym kodem. Zastąp parametry połączenia dla zmiennej globalnej connectionString w wierszu bezpośrednio powyżej metody Main Twojej prawidłowe parametry połączenia w witrynie Azure portal. Jest to jedyna zmiana, które należy wprowadzić ten kod.

Uruchom aplikację, aby zobaczyć są zawsze szyfrowane w działaniu.

```cs
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Globalization;

namespace AlwaysEncryptedConsoleApp
{
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Data Source = SPE-T640-01.sys-sqlsvr.local; Initial Catalog = Clinic; Integrated Security = true";

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

            CultureInfo culture = CultureInfo.CreateSpecificCulture("en-US");
            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993", culture)
            });


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
```

## <a name="verify-that-the-data-is-encrypted"></a>Sprawdź, czy dane są szyfrowane.

Można szybko sprawdzić, czy rzeczywistych danych na serwerze są szyfrowane, badając **pacjentów** danych za pomocą programu SSMS. (Użyj tego połączenia gdzie ustawieniem szyfrowania kolumny nie jest jeszcze włączone).

Uruchom następujące zapytanie w bazie danych okulistycznej.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Aby zobaczyć, że zaszyfrowanych kolumn nie zawierają żadnych danych w postaci zwykłego tekstu.

   ![Nową aplikację konsoli](./media/sql-database-always-encrypted/ssms-encrypted.png)

Aby uzyskać dostęp do danych w postaci zwykłego tekstu za pomocą programu SSMS, możesz dodać **ustawienie szyfrowania kolumny = włączone** parametrów połączenia.

1. W programie SSMS kliknij prawym przyciskiem myszy serwer w **Eksplorator obiektów**, a następnie kliknij przycisk **rozłączenia**.
2. Kliknij przycisk **Connect** > **aparatu bazy danych** otworzyć **Połącz z serwerem** okna, a następnie kliknij przycisk **opcje**.
3. Kliknij przycisk **dodatkowe parametry połączenia** i typ **ustawienie szyfrowania kolumny = włączone**.

    ![Nową aplikację konsoli](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. Uruchom następujące zapytanie **kliniki** bazy danych.

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Teraz widać danych zwykłego tekstu, zaszyfrowanych kolumn.

    ![Nową aplikację konsoli](./media/sql-database-always-encrypted/ssms-plaintext.png)

> [!NOTE]
> Jeśli łączysz się przy użyciu programu SSMS (lub dowolnego klienta), za pomocą innego komputera, nie będzie miał dostęp do kluczy szyfrowania i nie będzie do odszyfrowania danych.

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu bazy danych, która korzysta z funkcji zawsze zaszyfrowane, można wykonać następujące czynności:

* Za pomocą innego komputera, należy uruchomić ten przykład. Nie będzie mieć dostępu do kluczy szyfrowania, więc nie ma dostępu do danych w postaci zwykłego tekstu i nie zostanie pomyślnie uruchomiona.
* [Obracanie i wyczyścić klucze](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migrowanie danych, który jest już zaszyfrowany z funkcją Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx).
* [Wdrażanie certyfikatów są zawsze szyfrowane na innych komputerach klienckich](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (zobacz sekcję "Co certyfikaty dostępny do aplikacji i użytkownicy").

## <a name="related-information"></a>Informacje pokrewne

* [Zawsze szyfrowane (Programowanie klienta)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Przezroczyste szyfrowanie danych](https://msdn.microsoft.com/library/bb934049.aspx)
* [Szyfrowanie serwera SQL](https://msdn.microsoft.com/library/bb510663.aspx)
* [Kreator zawsze szyfrowane](https://msdn.microsoft.com/library/mt459280.aspx)
* [Zawsze zaszyfrowane Blog](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)