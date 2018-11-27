---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 11/09/2018
ms.author: genemi
ms.openlocfilehash: c4329b9efef3cdb2911466e64ac6c9f07a1e9b31
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2018
ms.locfileid: "52271652"
---
<a name="cs_0_csharpprogramexample_h2"/>

## <a name="c-program-example"></a>Przykład programu w języku C#

W kolejnych sekcjach tego artykułu zaprezentowano program w języku C#, który wysyła instrukcje języka Transact-SQL do bazy danych SQL przy użyciu platformy ADO.NET. Program w języku C# wykonuje następujące akcje:

1. [Łączy się z naszą bazą danych SQL za pomocą platformy ADO.NET](#cs_1_connect).
2. [Tworzy tabele](#cs_2_createtables).
3. [Wypełnia tabele danymi, wydając instrukcje INSERT języka T-SQL](#cs_3_insert).
4. [Aktualizuje dane przy użyciu sprzężenia](#cs_4_updatejoin).
5. [Usuwa dane przy użyciu sprzężenia](#cs_5_deletejoin).
6. [Wybiera wiersze danych przy użyciu sprzężenia](#cs_6_selectrows).
7. Zamyka połączenie (co usuwa wszystkie tabele tymczasowe z bazy danych tempdb).

Program w języku C# zawiera:

- Kod w języku C# do łączenia się z bazą danych.
- Metody zwracające kod źródłowy w języku T-SQL.
- Dwie metody wysyłające kod w języku T-SQL do bazy danych.

#### <a name="to-compile-and-run"></a>Kompilowanie i uruchamianie

Ten program w języku C# to logicznie jeden plik cs. Tutaj jednak program jest fizycznie podzielony na kilka bloków kodu, aby poprawić widoczność i czytelność kodu. Aby skompilować i uruchomić ten program, wykonaj następujące czynności:

1. Utwórz projekt w języku C# w programie Visual Studio.
    - Typem projektu powinna być aplikacja *konsoli*. Należy go wybrać z hierarchii zbliżonej do następującej: **Szablony** > **Visual C#** > **Klasyczny pulpit systemu Windows** > **Aplikacja konsoli (.NET Framework)**.
3. W pliku **Program.cs** wymaż wiersze kodu początkowego.
3. Skopiuj i wklej do pliku Program.cs każdy z następujących bloków, w tej samej kolejności, w jakiej są wyświetlane tutaj.
4. W pliku Program.cs zmodyfikuj następujące wartości w metodzie **Main**:

   - **cb.DataSource**
   - **cd.UserID**
   - **cb.Password**
   - **InitialCatalog**

5. Upewnij się, że przywoływany jest zestaw **System.Data.dll**. Aby to sprawdzić, rozwiń węzeł **Odwołania** w okienku **Eksplorator rozwiązań**.
6. Aby skompilować program w programie Visual Studio, kliknij menu **Kompilacja**.
7. Aby uruchomić program z programu Visual Studio, kliknij przycisk **Uruchom**. Dane wyjściowe raportu są wyświetlane w oknie cmd.exe.

> [!NOTE]
> Istnieje możliwość edytowania kodu w języku T-SQL w celu dodania znaku **#** na początku nazw tabel, co powoduje ich utworzenie jako tabel tymczasowych w **bazie danych tempdb**. Może to być przydatne dla celów demonstracyjnych, gdy nie jest dostępna testowa baza danych. Tabele tymczasowe są usuwane automatycznie przy zamykaniu połączenia. W przypadku tabel tymczasowych nie są wymuszane żadne instrukcje REFERENCES dla kluczy obcych.
>

<a name="cs_1_connect"/>
### <a name="c-block-1-connect-by-using-adonet"></a>Blok nr 1 kodu w języku C#: łączenie za pomocą platformy ADO.NET

- [Dalej](#cs_2_createtables)


```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll 
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
   class Program
   {
      static void Main(string[] args)
      {
         try
         {
            var cb = new SqlConnectionStringBuilder();
            cb.DataSource = "your_server.database.windows.net";
            cb.UserID = "your_user";
            cb.Password = "your_password";
            cb.InitialCatalog = "your_database";

            using (var connection = new SqlConnection(cb.ConnectionString))
            {
               connection.Open();

               Submit_Tsql_NonQuery(connection, "2 - Create-Tables",
                  Build_2_Tsql_CreateTables());

               Submit_Tsql_NonQuery(connection, "3 - Inserts",
                  Build_3_Tsql_Inserts());

               Submit_Tsql_NonQuery(connection, "4 - Update-Join",
                  Build_4_Tsql_UpdateJoin(),
                  "@csharpParmDepartmentName", "Accounting");

               Submit_Tsql_NonQuery(connection, "5 - Delete-Join",
                  Build_5_Tsql_DeleteJoin(),
                  "@csharpParmDepartmentName", "Legal");

               Submit_6_Tsql_SelectEmployees(connection);
            }
         }
         catch (SqlException e)
         {
            Console.WriteLine(e.ToString());
         }
         Console.WriteLine("View the report output here, then press any key to end the program...");
         Console.ReadKey();
      }
```


<a name="cs_2_createtables"/>
### <a name="c-block-2-t-sql-to-create-tables"></a>Blok nr 2 kodu w języku C#: kod w języku T-SQL do tworzenia tabel

- [Wstecz](#cs_1_connect) &nbsp; / &nbsp; [Dalej](#cs_3_insert)

```csharp
      static string Build_2_Tsql_CreateTables()
      {
         return @"
DROP TABLE IF EXISTS tabEmployee;
DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.


CREATE TABLE tabDepartment
(
   DepartmentCode  nchar(4)          not null
      PRIMARY KEY,
   DepartmentName  nvarchar(128)     not null
);

CREATE TABLE tabEmployee
(
   EmployeeGuid    uniqueIdentifier  not null  default NewId()
      PRIMARY KEY,
   EmployeeName    nvarchar(128)     not null,
   EmployeeLevel   int               not null,
   DepartmentCode  nchar(4)              null
      REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
);
";
      }
```

#### <a name="entity-relationship-diagram-erd"></a>Diagram relacji jednostek

Wcześniejsze instrukcji CREATE TABLE zawierają słowo kluczowe **REFERENCES** do tworzenia relacji za pomocą *klucza obcego* między dwiema tabelami.  Jeśli używasz bazy danych tempdb, wykomentuj słowo kluczowe `--REFERENCES`, wpisując na początku dwa łączniki.

Poniżej zaprezentowano diagram relacji jednostek, który przedstawia relację między dwiema tabelami. Wartości w kolumnie *podrzędnej* #tabEmployee.DepartmentCode są ograniczone do wartości znajdujących się w kolumnie *nadrzędnej* #tabDepartment.Department.

![Diagram relacji jednostek pokazujący klucz obcy](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)


<a name="cs_3_insert"/>
### <a name="c-block-3-t-sql-to-insert-data"></a>Blok nr 3 kodu w języku C#: kod w języku T-SQL do wstawiania danych

- [Wstecz](#cs_2_createtables) &nbsp; / &nbsp; [Dalej](#cs_4_updatejoin)


```csharp
      static string Build_3_Tsql_Inserts()
      {
         return @"
-- The company has these departments.
INSERT INTO tabDepartment
   (DepartmentCode, DepartmentName)
      VALUES
   ('acct', 'Accounting'),
   ('hres', 'Human Resources'),
   ('legl', 'Legal');

-- The company has these employees, each in one department.
INSERT INTO tabEmployee
   (EmployeeName, EmployeeLevel, DepartmentCode)
      VALUES
   ('Alison'  , 19, 'acct'),
   ('Barbara' , 17, 'hres'),
   ('Carol'   , 21, 'acct'),
   ('Deborah' , 24, 'legl'),
   ('Elle'    , 15, null);
";
      }
```


<a name="cs_4_updatejoin"/>
### <a name="c-block-4-t-sql-to-update-join"></a>Blok nr 4 kodu w języku C#: kod w języku T-SQL do aktualizacji ze sprzężeniem

- [Wstecz](#cs_3_insert) &nbsp; / &nbsp; [Dalej](#cs_5_deletejoin)


```csharp
      static string Build_4_Tsql_UpdateJoin()
      {
         return @"
DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';


-- Promote everyone in one department (see @parm...).
UPDATE empl
   SET
      empl.EmployeeLevel += 1
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName1;
";
      }
```


<a name="cs_5_deletejoin"/>
### <a name="c-block-5-t-sql-to-delete-join"></a>Blok nr 5 kodu w języku C#: kod w języku T-SQL do usuwania ze sprzężeniem

- [Wstecz](#cs_4_updatejoin) &nbsp; / &nbsp; [Dalej](#cs_6_selectrows)


```csharp
      static string Build_5_Tsql_DeleteJoin()
      {
         return @"
DECLARE @DName2  nvarchar(128);
SET @DName2 = @csharpParmDepartmentName;  --'Legal';


-- Right size the Legal department.
DELETE empl
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName2

-- Disband the Legal department.
DELETE tabDepartment
   WHERE DepartmentName = @DName2;
";
      }
```



<a name="cs_6_selectrows"/>
### <a name="c-block-6-t-sql-to-select-rows"></a>Blok nr 6 kodu w języku C#: kod w języku T-SQL do wybierania wierszy

- [Wstecz](#cs_5_deletejoin) &nbsp; / &nbsp; [Dalej](#cs_6b_datareader)


```csharp
      static string Build_6_Tsql_SelectEmployees()
      {
         return @"
-- Look at all the final Employees.
SELECT
      empl.EmployeeGuid,
      empl.EmployeeName,
      empl.EmployeeLevel,
      empl.DepartmentCode,
      dept.DepartmentName
   FROM
      tabEmployee   as empl
      LEFT OUTER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   ORDER BY
      EmployeeName;
";
      }
```


<a name="cs_6b_datareader"/>
### <a name="c-block-6b-executereader"></a>Blok nr 6b kodu w języku C#: ExecuteReader

- [Wstecz](#cs_6_selectrows) &nbsp; / &nbsp; [Dalej](#cs_7_executenonquery)

Ta metoda jest przeznaczona do uruchamiania instrukcji SELECT w języku T-SQL, która jest tworzona przez metodę **Build_6_Tsql_SelectEmployees**.


```csharp
      static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("Now, SelectEmployees (6)...");

         string tsql = Build_6_Tsql_SelectEmployees();

         using (var command = new SqlCommand(tsql, connection))
         {
            using (SqlDataReader reader = command.ExecuteReader())
            {
               while (reader.Read())
               {
                  Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                     reader.GetGuid(0),
                     reader.GetString(1),
                     reader.GetInt32(2),
                     (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                     (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
               }
            }
         }
      }
```


<a name="cs_7_executenonquery"/>
### <a name="c-block-7-executenonquery"></a>Blok nr 7 kodu w języku C#: ExecuteNonQuery

- [Wstecz](#cs_6b_datareader) &nbsp; / &nbsp; [Dalej](#cs_8_output)

Ta metoda jest wywoływana dla operacji, które modyfikują dane w tabelach bez zwracania żadnych wierszy danych.


```csharp
      static void Submit_Tsql_NonQuery(
         SqlConnection connection,
         string tsqlPurpose,
         string tsqlSourceCode,
         string parameterName = null,
         string parameterValue = null
         )
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

         using (var command = new SqlCommand(tsqlSourceCode, connection))
         {
            if (parameterName != null)
            {
               command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                  parameterName,
                  parameterValue);
            }
            int rowsAffected = command.ExecuteNonQuery();
            Console.WriteLine(rowsAffected + " = rows affected.");
         }
      }
   } // EndOfClass
}
```


<a name="cs_8_output"/>
### <a name="c-block-8-actual-test-output-to-the-console"></a>Blok nr 8 kodu w języku C#: rzeczywiste dane wyjściowe testu w konsoli

- [Wstecz](#cs_7_executenonquery)

W tej sekcji znajdują się dane wyjściowe, które program wysłał do konsoli. Przebiegi testów różnią się między sobą jedynie wartościami identyfikatorów GUID.


```text
[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>> csharp_db_test.exe

=================================
Now, CreateTables (10)...

=================================
Now, Inserts (20)...

=================================
Now, UpdateJoin (30)...
2 rows affected, by UpdateJoin.

=================================
Now, DeleteJoin (40)...

=================================
Now, SelectEmployees (50)...
0199be49-a2ed-4e35-94b7-e936acf1cd75 , Alison , 20 , acct , Accounting
f0d3d147-64cf-4420-b9f9-76e6e0a32567 , Barbara , 17 , hres , Human Resources
cf4caede-e237-42d2-b61d-72114c7e3afa , Carol , 22 , acct , Accounting
cdde7727-bcfd-4f72-a665-87199c415f8b , Elle , 15 , NULL , NULL

[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>>
```
