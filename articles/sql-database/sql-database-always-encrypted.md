---
title: Zawsze szyfrowane — magazyn certyfikatów systemu Windows
description: W tym artykule pokazano, jak zabezpieczyć poufne dane w bazie danych SQL za pomocą szyfrowania bazy danych przy użyciu Kreatora zawsze zaszyfrowanych w programie SQL Server Management Studio (SSMS). Pokazuje również sposób przechowywania kluczy szyfrowania w magazynie certyfikatów systemu Windows.
keywords: szyfrowanie danych, szyfrowanie SQL, szyfrowanie baz danych, poufne dane, Zawsze szyfrowane
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviwer: ''
ms.date: 03/08/2019
ms.openlocfilehash: 82c3c3274a8a9d66019ce906ee7be47cedac7470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822040"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-the-windows-certificate-store"></a>Zawsze szyfrowane: ochrona poufnych danych i przechowywanie kluczy szyfrowania w magazynie certyfikatów systemu Windows

W tym artykule pokazano, jak zabezpieczyć poufne dane w bazie danych SQL za pomocą szyfrowania bazy danych przy użyciu [Kreatora zawsze zaszyfrowanych](https://msdn.microsoft.com/library/mt459280.aspx) w [programie SQL Server Management Studio (SSMS).](https://msdn.microsoft.com/library/hh213248.aspx) Pokazuje również sposób przechowywania kluczy szyfrowania w magazynie certyfikatów systemu Windows.

Zawsze szyfrowana to nowa technologia szyfrowania danych w usłudze Azure SQL Database i SQL Server, która pomaga chronić poufne dane w spoczynku na serwerze, podczas przemieszczania się między klientem a serwerem oraz podczas korzystania z danych, zapewniając, że poufne dane nigdy nie pojawiają się jako w systemie baz danych. Po zaszyfrowaniu danych tylko aplikacje klienckie lub serwery aplikacji, które mają dostęp do kluczy, mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu. Aby uzyskać szczegółowe informacje, zobacz [Zawsze zaszyfrowany (Aparat baz danych)](https://msdn.microsoft.com/library/mt163865.aspx).

Po skonfigurowaniu bazy danych do używania zawsze zaszyfrowane, utworzysz aplikację kliencką w języku C# z programem Visual Studio do pracy z zaszyfrowanymi danymi.

Wykonaj kroki opisane w tym artykule, aby dowiedzieć się, jak skonfigurować zawsze zaszyfrowane dla bazy danych SQL platformy Azure. W tym artykule dowiesz się, jak wykonywać następujące zadania:

* Użyj kreatora zawsze zaszyfrowane w SSMS, aby utworzyć [zawsze zaszyfrowane klucze](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Utwórz [klucz główny kolumny (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Utwórz [klucz szyfrowania kolumn (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Tworzenie tabeli bazy danych i szyfrowanie kolumn.
* Utwórz aplikację, która wstawia, wybiera i wyświetla dane z zaszyfrowanych kolumn.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku musisz:

* Konto i subskrypcja platformy Azure. Jeśli go nie masz, zarejestruj się, aby uzyskać [bezpłatną wersję próbną.](https://azure.microsoft.com/pricing/free-trial/)
* [Sql Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) w wersji 13.0.700.242 lub nowszej.
* [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) lub nowsza (na komputerze klienckim).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="create-a-blank-sql-database"></a>Tworzenie pustej bazy danych SQL

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Kliknij **pozycję Utwórz zasób** > **Dane + Magazyn** > **bazy danych SQL**.
3. Utwórz **pustą** bazę danych o nazwie **Klinika** na nowym lub istniejącym serwerze. Aby uzyskać szczegółowe instrukcje dotyczące tworzenia bazy danych w witrynie Azure portal, zobacz [Twoja pierwsza baza danych SQL platformy Azure.](sql-database-single-database-get-started.md)

    ![Tworzenie pustej bazy danych](./media/sql-database-always-encrypted/create-database.png)

Będziesz potrzebował ciągu połączenia w dalszej części samouczka. Po utworzeniu bazy danych przejdź do nowej bazy danych kliniki i skopiuj parametry połączenia. Możesz uzyskać parametry połączenia w dowolnym momencie, ale można go skopiować, gdy jesteś w witrynie Azure portal.

1. Kliknij pozycję **Bazy danych** > SQL**Program Clinic** > **Show parametry połączenia bazy danych**.
2. Skopiuj parametry połączenia dla **ADO.NET**.

    ![Kopiowanie parametrów połączenia](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Nawiązywanie połączenia z bazą danych za pomocą programu SSMS

Otwórz usługę SSMS i połącz się z serwerem za pomocą bazy danych Clinic.

1. Otwórz program SSMS. (Kliknij pozycję **Podłącz** > **aparat bazy danych,** aby otworzyć okno **Połącz z serwerem,** jeśli nie jest otwarte).
2. Wprowadź nazwę serwera i poświadczenia. Nazwę serwera można znaleźć w bloku bazy danych SQL i w ciągu połączenia skopiowanym wcześniej. Wpisz pełną nazwę serwera, w tym *database.windows.net*.

    ![Kopiowanie parametrów połączenia](./media/sql-database-always-encrypted/ssms-connect.png)

Jeśli zostanie otwarte okno **Nowa reguła zapory,** zaloguj się na platformie Azure i pozwól SSMS utworzyć nową regułę zapory.

## <a name="create-a-table"></a>Tworzenie tabeli

W tej sekcji utworzysz tabelę do przechowywania danych pacjenta. Będzie to początkowo normalna tabela — szyfrowanie zostanie skonfigurowane w następnej sekcji.

1. Rozwiń **bazy danych**.
2. Kliknij prawym przyciskiem myszy bazę danych **kliniki** i kliknij polecenie **Nowe zapytanie**.
3. Wklej następujące Transact-SQL (T-SQL) do nowego okna kwerendy i **wykonaj** go.

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

## <a name="encrypt-columns-configure-always-encrypted"></a>Szyfruj kolumny (konfiguruj zawsze zaszyfrowane)

SSMS udostępnia kreatora, aby łatwo skonfigurować zawsze szyfrowane przez skonfigurowanie CMK, CEK i zaszyfrowanych kolumn dla Ciebie.

1. Rozwiń**tabele** > **kliniki** > baz **danych**.
2. Kliknij prawym przyciskiem myszy tabelę **Pacjenci** i wybierz **polecenie Szyfruj kolumny,** aby otworzyć Kreatora zawsze zaszyfrowanych:

    ![Szyfruj kolumny](./media/sql-database-always-encrypted/encrypt-columns.png)

Kreator Zawsze zaszyfrowany zawiera następujące sekcje: **Wybór kolumny,** **Konfiguracja klucza głównego** (CMK), **Sprawdzanie poprawności**i **Podsumowanie**.

### <a name="column-selection"></a>Wybór kolumny

Kliknij **przycisk Dalej** na stronie **Wprowadzenie,** aby otworzyć stronę **Wyboru kolumny.** Na tej stronie należy wybrać kolumny, które mają być szyfrowane, [typ szyfrowania i klucz szyfrowania kolumny (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) do użycia.

Szyfruj informacje **O 10 I** **Data urodzenia** dla każdego pacjenta. Kolumna **SSN** będzie używać szyfrowania deterministycznego, które obsługuje wyszukiwania równości, sprzężenia i grupowanie według. **Kolumna BirthDate** będzie używać szyfrowania losowego, które nie obsługuje operacji.

Ustaw **typ szyfrowania** dla kolumny **SSN** na **Deterministyczny,** a kolumna **Data urodzenia** na **Randomizowaną**. Kliknij przycisk **alej**.

![Szyfruj kolumny](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Konfiguracja klucza głównego

Strona **Konfiguracja klucza głównego** to miejsce, w którym skonfigurowana jest usługa CMK i wybranie dostawcy magazynu kluczy, w którym będzie przechowywany cmk. Obecnie można przechowywać współrzędnościową maszynę cmk w magazynie certyfikatów systemu Windows, usługi Azure Key Vault lub sprzętowym module zabezpieczeń (HSM). W tym samouczku pokazano, jak przechowywać klucze w magazynie certyfikatów systemu Windows.

Sprawdź, czy **jest zaznaczony magazyn certyfikatów systemu Windows** i kliknij przycisk **Dalej**.

![Konfiguracja klucza głównego](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Sprawdzanie poprawności

Można teraz zaszyfrować kolumny lub zapisać skrypt programu PowerShell, aby uruchomić go później. W tym samouczku wybierz pozycję **Przejdź do końca teraz** i kliknij przycisk **Dalej**.

### <a name="summary"></a>Podsumowanie

Sprawdź, czy wszystkie ustawienia są poprawne i kliknij przycisk **Zakończ,** aby zakończyć konfigurację zawsze zaszyfrowanej.

![Podsumowanie](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>Weryfikowanie akcji kreatora

Po zakończeniu pracy kreatora baza danych jest skonfigurowana dla zawsze zaszyfrowanych. Kreator wykonał następujące czynności:

* Utworzono cmk.
* Utworzono CEK.
* Skonfigurowano wybrane kolumny do szyfrowania. Tabela **Pacjenci** nie zawiera obecnie żadnych danych, ale wszystkie istniejące dane w wybranych kolumnach są teraz szyfrowane.

Możesz zweryfikować utworzenie kluczy w SSMS, przechodząc do **Clinic** > **Security** > **Always Encrypted Keys**. Teraz możesz zobaczyć nowe klucze wygenerowane przez kreatora.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Tworzenie aplikacji klienckiej, która współpracuje z zaszyfrowanymi danymi

Teraz, gdy zawsze zaszyfrowane jest skonfigurowany, można utworzyć aplikację, która wykonuje *wstawia* i *wybiera* na zaszyfrowanych kolumnach. Aby pomyślnie uruchomić przykładową aplikację, należy ją uruchomić na tym samym komputerze, na którym uruchomiono Kreatora zawsze zaszyfrowane. Aby uruchomić aplikację na innym komputerze, należy wdrożyć zawsze zaszyfrowane certyfikaty na komputerze z uruchomiona w aplikacji klienckiej.  

> [!IMPORTANT]
> Aplikacja musi używać obiektów [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) podczas przekazywania danych w postaci zwykłego tekstu na serwer z zawsze zaszyfrowanymi kolumnami. Przekazywanie wartości dosłownych bez użycia obiektów SqlParameter spowoduje wyjątek.

1. Otwórz program Visual Studio i utwórz nową aplikację konsoli języka C#. Upewnij się, że projekt jest ustawiony na **.NET Framework 4.6** lub nowszych.
2. Nazwij projekt **AlwaysEncryptedConsoleApp** i kliknij przycisk **OK**.

![Nowa aplikacja konsoli](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modyfikowanie ciągu połączenia w celu włączenia opcji Zawsze szyfrowane

W tej sekcji wyjaśniono, jak włączyć zawsze szyfrowane w ciągu połączenia bazy danych. Zmodyfikujesz aplikację konsoli utworzoną właśnie w następnej sekcji "Zawsze szyfrowana przykładowa aplikacja konsoli".

Aby włączyć opcję Zawsze szyfrowane, należy dodać słowo kluczowe **Ustawienie szyfrowania kolumn** do ciągu połączenia i ustawić je na **Włączone**.

Można ustawić to bezpośrednio w ciągu połączenia lub można go ustawić za pomocą [sqlconnectionstringbuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Przykładowa aplikacja w następnej sekcji pokazuje, jak używać **programu SqlConnectionStringBuilder**.

> [!NOTE]
> Jest to jedyna zmiana wymagana w aplikacji klienckiej specyficznej dla zawsze zaszyfrowanych. Jeśli masz istniejącą aplikację, która przechowuje jego parametry połączenia zewnętrznie (to znaczy w pliku konfiguracyjnym), możesz włączyć opcję Zawsze szyfrowane bez zmiany kodu.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Włącz zawsze zaszyfrowane w ciągu połączenia

Dodaj następujące słowo kluczowe do ciągu połączenia:

    Column Encryption Setting=Enabled

### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Włącz zawsze szyfrowane za pomocą SqlConnectionStringBuilder

Poniższy kod pokazuje, jak włączyć zawsze szyfrowane, ustawiając [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) [do Włączone](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="always-encrypted-sample-console-application"></a>Zawsze szyfrowana przykładowa aplikacja konsoli

W tym przykładzie pokazano, jak:

* Zmodyfikuj parametry połączenia, aby włączyć opcję Zawsze szyfrowana.
* Wstawianie danych do zaszyfrowanych kolumn.
* Wybierz rekord, filtrując dla określonej wartości w zaszyfrowanej kolumnie.

Zastąp zawartość pliku **Program.cs** poniższym kodem. Zastąp parametry połączenia dla globalnej zmiennej connectionString w wierszu bezpośrednio nad główną metodą prawidłowym ciągiem połączenia z witryny Azure portal. Jest to jedyna zmiana, którą należy wprowadzić w tym kodzie.

Uruchom aplikację, aby wyświetlić opcję Zawsze zaszyfrowane w akcji.

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

## <a name="verify-that-the-data-is-encrypted"></a>Sprawdź, czy dane są szyfrowane

Można szybko sprawdzić, czy rzeczywiste dane na serwerze są szyfrowane przez zapytanie **danych pacjentów** za pomocą usługi SSMS. (Użyj bieżącego połączenia, w którym ustawienie szyfrowania kolumn nie jest jeszcze włączone).

Uruchom następującą kwerendę w bazie danych kliniki.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Widać, że zaszyfrowane kolumny nie zawierają żadnych danych w postaci zwykłego tekstu.

   ![Nowa aplikacja konsoli](./media/sql-database-always-encrypted/ssms-encrypted.png)

Aby uzyskać dostęp do danych w postaci zwykłego tekstu za pomocą usługi SSMS, można dodać parametr **Ustawienia szyfrowania kolumn=włączono** do połączenia.

1. W programie SSMS kliknij prawym przyciskiem myszy serwer w **Eksploratorze obiektów,** a następnie kliknij polecenie **Rozłącz**.
2. Kliknij **pozycję Połącz** > **aparat bazy danych,** aby otworzyć okno **Połącz z serwerem,** a następnie kliknij pozycję **Opcje**.
3. Kliknij **pozycję Dodatkowe parametry połączenia** i wpisz ustawienie **szyfrowania kolumny=włączono**.

    ![Nowa aplikacja konsoli](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. Uruchom następującą kwerendę w bazie danych **kliniki.**

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Teraz można zobaczyć dane w postaci zwykłego tekstu w zaszyfrowanych kolumnach.

    ![Nowa aplikacja konsoli](./media/sql-database-always-encrypted/ssms-plaintext.png)

> [!NOTE]
> Jeśli połączysz się z usługą SSMS (lub dowolnym klientem) z innego komputera, nie będzie on miał dostępu do kluczy szyfrowania i nie będzie mógł odszyfrować danych.

## <a name="next-steps"></a>Następne kroki

Po utworzeniu bazy danych, która używa zawsze zaszyfrowane, można wykonać następujące czynności:

* Uruchom ten przykład z innego komputera. Nie będzie miał dostępu do kluczy szyfrowania, więc nie będzie miał dostępu do danych w postaci zwykłego tekstu i nie zostanie pomyślnie uruchomiony.
* [Obróć i oczyść klawisze](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migrowanie danych, które są już zaszyfrowane za pomocą always encrypted](https://msdn.microsoft.com/library/mt621539.aspx).
* [Wdrażanie zawsze zaszyfrowanych certyfikatów na innych komputerach klienckich](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (zobacz sekcję "Udostępnianie certyfikatów aplikacjom i użytkownikom").

## <a name="related-information"></a>Informacje pokrewne

* [Zawsze szyfrowane (rozwój klienta)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Niewidoczne szyfrowanie danych](https://msdn.microsoft.com/library/bb934049.aspx)
* [Szyfrowanie programu SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
* [Zawsze zaszyfrowany Kreator](https://msdn.microsoft.com/library/mt459280.aspx)
* [Zawsze zaszyfrowany blog](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)