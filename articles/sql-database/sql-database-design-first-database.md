---
title: 'Samouczek: projektowanie pierwszej bazy danych Azure SQL Database przy użyciu programu SSMS | Microsoft Docs'
description: Dowiedz się, jak zaprojektować pierwszą bazę danych Azure SQL Database za pomocą programu SQL Server Management Studio (SSMS).
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop databases
ms.topic: tutorial
ms.date: 04/23/2018
ms.author: carlrab
ms.openlocfilehash: ba14208e971d712184052e7470757ce48ac26879
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-design-your-first-azure-sql-database-using-ssms"></a>Samouczek: projektowanie pierwszej bazy danych Azure SQL Database przy użyciu programu SSMS

Usługa Azure SQL Database to relacyjna baza danych oferowana jako usługa (DBaaS, database-as-a service) na platformie Microsoft Cloud (Azure). Z tego samouczka dowiesz się, jak przy użyciu witryny Azure Portal i programu [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) wykonać następujące czynności: 

> [!div class="checklist"]
> * Tworzenie bazy danych w witrynie Azure Portal*
> * Skonfigurowanie reguły zapory na poziomie serwera w witrynie Azure Portal
> * Nawiązywanie połączenia z bazą danych za pomocą programu SSMS
> * Tworzenie tabel za pomocą programu SSMS
> * Ładowanie zbiorcze danych za pomocą narzędzia BCP
> * Tworzenie zapytań dotyczących tych danych za pomocą programu SSMS

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

   >[!NOTE]
   > Na potrzeby tego samouczka użyto [modelu zakupu w oparciu o jednostki DTU](sql-database-service-tiers-dtu.md), ale możesz też wybrać [model zakupu w oparciu o rdzenie wirtualne (w wersji zapoznawczej)](sql-database-service-tiers-vcore.md). 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że zainstalowano następujące elementy:
- Najnowsza wersja programu [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS).
- Najnowsze wersje narzędzi [BCP i SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433).

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-a-blank-sql-database"></a>Tworzenie pustej bazy danych SQL

Baza danych Azure SQL jest tworzona ze zdefiniowanym zestawem [zasobów obliczeniowych i przechowywania](sql-database-service-tiers-dtu.md). Baza danych jest tworzona w [grupie zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) oraz na [serwerze logicznym bazy danych Azure SQL Database](sql-database-features.md). 

Wykonaj poniższe czynności, aby utworzyć pustą bazę danych SQL. 

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.

2. Na stronie **Nowy** wybierz pozycję **Bazy danych** w sekcji Azure Marketplace, a następnie kliknij pozycję **Baza danych SQL** w sekcji **Polecane**.

   ![tworzenie pustej bazy danych](./media/sql-database-design-first-database/create-empty-database.png)

3. Wypełnij formularz Baza danych SQL w sposób pokazany na wcześniejszej ilustracji, używając następujących informacji:   

   | Ustawienie       | Sugerowana wartość | Opis | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nazwa bazy danych** | mySampleDatabase | Prawidłowe nazwy baz danych opisano w artykule [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identyfikatory baz danych). | 
   | **Subskrypcja** | Twoja subskrypcja  | Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.windowsazure.com/Subscriptions). |
   | **Grupa zasobów** | myResourceGroup | Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Reguły i ograniczenia nazewnictwa). |
   | **Wybierz źródło** | Pusta baza danych | Określa, że ma zostać utworzona pusta baza danych. |

4. Kliknij pozycję **Serwer**, aby utworzyć i skonfigurować nowy serwer dla nowej bazy danych. Wypełnij **formularz nowego serwera**, używając następujących informacji: 

   | Ustawienie       | Sugerowana wartość | Opis | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nazwa serwera** | Dowolna nazwa unikatowa w skali globalnej | Prawidłowe nazwy serwera opisano w artykule [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Reguły i ograniczenia nazewnictwa). | 
   | **Identyfikator logowania administratora serwera** | Dowolna prawidłowa nazwa | Prawidłowe nazwy identyfikatorów logowania opisano w artykule [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identyfikatory baz danych).|
   | **Hasło** | Dowolne prawidłowe hasło | Hasło musi mieć co najmniej osiem znaków i musi zawierać znaki z trzech z następujących kategorii: wielkie litery, małe litery, cyfry i znaki inne niż alfanumeryczne. |
   | **Lokalizacja** | Dowolna prawidłowa lokalizacja | Aby uzyskać informacje na temat regionów, zobacz temat [Regiony systemu Azure](https://azure.microsoft.com/regions/). |

   ![tworzenie serwera bazy danych](./media/sql-database-design-first-database/create-database-server.png)

5. Kliknij pozycję **Wybierz**.

6. Kliknij pozycję **Warstwa cenowa**, aby określić warstwę usługi, liczbę jednostek DTU lub rdzeni wirtualnych i ilość miejsca do magazynowania. Przejrzyj opcje liczby jednostek DTU/rdzeni wirtualnych i miejsca do magazynowania dostępne dla poszczególnych warstw usługi. Na potrzeby tego samouczka użyto [modelu zakupu w oparciu o jednostki DTU](sql-database-service-tiers-dtu.md), ale możesz też wybrać [model zakupu w oparciu o rdzenie wirtualne (w wersji zapoznawczej)](sql-database-service-tiers-vcore.md). 

7. Na potrzeby tego samouczka wybierz warstwę usługi **Standardowa**, a następnie wybierz za pomocą suwaka **100 jednostek DTU (S3)** i **400** GB miejsca do magazynowania.

   ![tworzenie bazy danych s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

8. Zaakceptuj warunki wersji zapoznawczej, aby użyć opcji **dodatkowego magazynu**. 

   > [!IMPORTANT]
   > -  Rozmiary magazynu większe niż uwzględniona ilość miejsca do magazynowania są dostępne w wersji zapoznawczej i za dodatkową opłatą. Szczegóły można znaleźć w [cenniku usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
   >-  W warstwie Premium ponad 1 TB miejsca do magazynowania jest obecnie dostępny w następujących regionach: Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada Środkowa, Kanada Wschodnia, Środkowe stany USA, Francja Środkowa, Niemcy Środkowe, Japonia Wschodnia, Japonia Zachodnia, Korea Środkowa, Północno-środkowe stany USA, Europa Północna, Południowo-środkowe stany USA, Azja Południowo-Wschodnia, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, Wschodnie stany USA 2, Zachodnie stany USA, US Gov Wirginia i Europa Zachodnia. Więcej informacji można znaleźć na stronie [bieżących ograniczeń poziomów P11–P15](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  


9. Po wybraniu warstwy serwera, liczby jednostek DTU i ilości miejsca do magazynowania kliknij przycisk **Zastosuj**.  

10. Wybierz **sortowanie** dla pustej bazy danych (na potrzeby tego samouczka użyj wartości domyślnej). Aby uzyskać więcej informacji na temat sortowań, zobacz [Sortowania](https://docs.microsoft.com/sql/t-sql/statements/collations)

11. Teraz po uzupełnieniu formularza SQL Database kliknij przycisk **Utwórz**, aby aprowizować bazę danych. Aprowizacja zajmuje kilka minut. 

12. Na pasku narzędzi kliknij pozycję **Powiadomienia**, aby monitorować proces wdrażania.
    
     ![powiadomienie](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Tworzenie reguły zapory na poziomie serwera

Usługa SQL Database tworzy zaporę na poziomie serwera, która uniemożliwia zewnętrznym aplikacjom i narzędziom łączenie się z serwerem i wszelkimi bazami danych na tym serwerze, chyba że zostanie utworzona reguła zapory otwierająca zaporę dla konkretnych adresów IP. Wykonaj następujące kroki, aby utworzyć [regułę zapory na poziomie serwera usługi SQL Database](sql-database-firewall-configure.md) dla podanego adresu IP klienta i włączyć zewnętrzną łączność przez zaporę usługi SQL Database wyłącznie dla konkretnego adresu IP. 

> [!NOTE]
> Usługa SQL Database nawiązuje komunikację na porcie 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być zablokowany przez firmową zaporę. Jeśli nastąpi taka sytuacja, nie będzie można nawiązać połączenia z serwerem usługi Azure SQL Database, chyba że dział IT otworzy port 1433.
>

1. Po ukończeniu wdrażania kliknij pozycję **Bazy danych SQL** w menu po lewej stronie i kliknij bazę danych **mySampleDatabase** na stronie **Bazy danych SQL**. Zostanie otwarta strona przeglądu bazy danych zawierająca w pełni kwalifikowaną nazwę serwera (na przykład **mynewserver-20170824.database.windows.net**) i opcje dalszej konfiguracji. 

2. Skopiuj tę w pełni kwalifikowaną nazwę serwera w celu nawiązania połączenia z serwerem i jego bazami danych w kolejnych samouczkach i przewodnikach Szybki start. 

   ![nazwa serwera](./media/sql-database-get-started-portal/server-name.png) 

3. Kliknij pozycję **Ustaw zaporę serwera** na pasku narzędzi. Zostanie otwarta strona **Ustawienia zapory** dla serwera SQL Database. 

   ![reguła zapory serwera](./media/sql-database-get-started-portal/server-firewall-rule.png) 

4. Kliknij pozycję **Dodaj adres IP klienta** na pasku narzędzi, aby dodać bieżący adres IP do nowej reguły zapory. Reguła zapory może otworzyć port 1433 dla pojedynczego adresu IP lub zakresu adresów IP.

5. Kliknij pozycję **Zapisz**. Dla bieżącego adresu IP zostanie utworzona reguła zapory na poziomie serwera otwierająca port 1433 na serwerze logicznym.

6. Kliknij przycisk **OK**, a następnie zamknij stronę **Ustawienia zapory**.

Teraz można połączyć się z serwerem usługi SQL Database i jego bazami danych przy użyciu programu SQL Server Management Studio lub innego wybranego narzędzia z tego adresu IP przy użyciu poprzednio utworzonego konta administratora serwera.

> [!IMPORTANT]
> Domyślnie dostęp za pośrednictwem zapory usługi SQL Database jest włączony dla wszystkich usług platformy Azure. Kliknij przycisk **WYŁ.** na tej stronie, aby wyłączyć tę opcję dla wszystkich usług platformy Azure.

## <a name="sql-server-connection-information"></a>Informacje o połączeniu z serwerem SQL

Pobierz w pełni kwalifikowaną nazwę serwera dla serwera Azure SQL Database w witrynie Azure Portal. W pełni kwalifikowanej nazwy serwera używa się do nawiązywania połączenia z serwerem przy użyciu programu SQL Server Management Studio.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Bazy danych SQL** z menu po lewej stronie, a następnie kliknij bazę danych na stronie **Bazy danych SQL**. 
3. W okienku **Essentials** na stronie bazy danych w witrynie Azure Portal zlokalizuj i skopiuj **nazwę serwera**.

   ![informacje o połączeniu](./media/sql-database-get-started-portal/server-name.png)

## <a name="connect-to-the-database-with-ssms"></a>Nawiązywanie połączenia z bazą danych za pomocą programu SSMS

Nawiąż połączenie z serwerem Azure SQL Database za pomocą programu [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms).

1. Otwórz program SQL Server Management Studio.

2. W oknie dialogowym **Połącz z serwerem** wprowadź następujące informacje:

   | Ustawienie       | Sugerowana wartość | Opis | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Typ serwera | Aparat bazy danych | Ta wartość jest wymagana |
   | Nazwa serwera | W pełni kwalifikowana nazwa serwera | Nazwa może mieć taką formę: **mynewserver20170824.database.windows.net**. |
   | Authentication | Uwierzytelnianie programu SQL Server | Uwierzytelnianie SQL to jedyny typ uwierzytelniania skonfigurowany w tym samouczku. |
   | Login | Konto administratora serwera | To konto określono podczas tworzenia serwera. |
   | Hasło | Hasło konta administratora serwera | To hasło określono podczas tworzenia serwera. |

   ![łączenie z serwerem](./media/sql-database-connect-query-ssms/connect.png)

3. Kliknij przycisk **Opcje** w oknie dialogowym **Połącz z serwerem**. W sekcji **Nawiązywanie połączenia z bazą danych** wprowadź ciąg **mySampleDatabase**, aby nawiązać połączenie z tą bazą danych.

   ![nawiązywanie połączenia z bazą danych na serwerze](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Kliknij przycisk **Połącz**. W programie SSMS zostanie otwarte okno Eksplorator obiektów. 

5. W Eksploratorze obiektów rozwiń pozycję **Bazy danych**, a następnie rozwiń pozycję **mySampleDatabase**, aby wyświetlić obiekty w przykładowej bazie danych.

   ![obiekty bazy danych](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="create-tables-in-the-database"></a>Tworzenie tabel w bazie danych 

Utwórz schemat bazy danych z czterema tabelami, które modelują system zarządzania studentami dla uczelni wyższych, korzystając z języka [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference):

- Person (Osoba)
- Course (Zajęcia)
- Student
- Credit (Zaliczenie)

Na poniższym diagramie przedstawiono, jak te tabele są ze sobą powiązane. Niektóre z tych tabel odwołują się do kolumn w innych tabelach. Na przykład tabela Student odwołuje się do kolumny **PersonId** w tabeli **Person**. Zapoznaj się z tym diagramem, aby zrozumieć, jak tabele w tym samouczku są ze sobą powiązane. Szczegółowe omówienie tworzenia efektywnych tabel bazy danych znajduje się w temacie [Tworzenie efektywnych tabel bazy danych](https://msdn.microsoft.com/library/cc505842.aspx). Aby uzyskać informacje dotyczące wybierania typów danych, zobacz [Typy danych](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> W celu tworzenia i projektowania tabel można również użyć [projektanta tabel w programie SQL Server Management Studio](https://msdn.microsoft.com/library/hh272695.aspx). 

![Relacje między tabelami](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. W Eksploratorze obiektów kliknij prawym przyciskiem myszy pozycję **mySampleDatabase** i kliknij opcję **Nowe zapytanie**. Zostanie otwarte puste okno zapytania, które jest połączone z Twoją bazą danych.

2. W oknie zapytania wykonaj następujące zapytanie, aby utworzyć cztery tabele w bazie danych: 

   ```sql 
   -- Create Person table

   CREATE TABLE Person
   (
   PersonId   INT IDENTITY PRIMARY KEY,
   FirstName   NVARCHAR(128) NOT NULL,
   MiddelInitial NVARCHAR(10),
   LastName   NVARCHAR(128) NOT NULL,
   DateOfBirth   DATE NOT NULL
   )
   
   -- Create Student table
 
   CREATE TABLE Student
   (
   StudentId INT IDENTITY PRIMARY KEY,
   PersonId  INT REFERENCES Person (PersonId),
   Email   NVARCHAR(256)
   )
   
   -- Create Course table
 
   CREATE TABLE Course
   (
   CourseId  INT IDENTITY PRIMARY KEY,
   Name   NVARCHAR(50) NOT NULL,
   Teacher   NVARCHAR(256) NOT NULL
   ) 

   -- Create Credit table
 
   CREATE TABLE Credit
   (
   StudentId   INT REFERENCES Student (StudentId),
   CourseId   INT REFERENCES Course (CourseId),
   Grade   DECIMAL(5,2) CHECK (Grade <= 100.00),
   Attempt   TINYINT,
   CONSTRAINT  [UQ_studentgrades] UNIQUE CLUSTERED
   (
   StudentId, CourseId, Grade, Attempt
   )
   )
   ```

   ![Tworzenie tabel](./media/sql-database-design-first-database/create-tables.png)

3. Rozwiń węzeł „tabele” w Eksploratorze obiektów programu SQL Server Management Studio, aby wyświetlić utworzone tabele.

   ![Utworzone tabele w programie SSMS](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Ładowanie danych do tabel

1. W folderze Pobrane utwórz folder o nazwie **SampleTableData** do przechowywania przykładowych danych bazy danych. 

2. Kliknij prawym przyciskiem myszy poniższe linki i zapisz je w folderze **SampleTableData**. 

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Otwórz okno wiersza polecenia i przejdź do folderu SampleTableData.

4. Wykonaj następujące polecenia, aby wstawić przykładowe dane do tabel, zastępując wartości **ServerName** (Nazwa serwera), **DatabaseName** (Nazwa bazy danych), **UserName** (Nazwa użytkownika) i **Password** (Hasło) wartościami odpowiednimi dla Twojego środowiska.
  
   ```bcp
   bcp Course in SampleCourseData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

Przykładowe dane zostaną załadowane do utworzonych wcześniej tabel.

## <a name="query-data"></a>Zapytania o dane

Wykonaj następujące zapytania, aby pobrać informacje z tabel bazy danych. Zobacz [Pisanie zapytań SQL](https://technet.microsoft.com/library/bb264565.aspx), aby dowiedzieć się więcej na temat pisania zapytań SQL. Pierwsze zapytanie sprzęga wszystkie cztery tabele, aby znaleźć wszystkich studentów nauczanych przez wykładowcę „Dominick Pope”, którzy mają ocenę wyższą niż 75% osób na jego zajęciach. Drugie zapytanie sprzęga wszystkie cztery tabele i znajduje wszystkie zajęcia, na które kiedykolwiek zapisał się student „Noe Coleman”.

1. W oknie zapytań programu SQL Server Management Studio wykonaj następujące zapytanie:

   ```sql 
   -- Find the students taught by Dominick Pope who have a grade higher than 75%

   SELECT  person.FirstName,
   person.LastName,
   course.Name,
   credit.Grade
   FROM  Person AS person
   INNER JOIN Student AS student ON person.PersonId = student.PersonId
   INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
   INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope' 
   AND Grade > 75
   ```

2. W oknie zapytań programu SQL Server Management Studio wykonaj następujące zapytanie:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled

   SELECT  course.Name,
   course.Teacher,
   credit.Grade
   FROM  Course AS course
   INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
   INNER JOIN Student AS student ON student.StudentId = credit.StudentId
   INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
   AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>Następne kroki 
W tym samouczku przedstawiono podstawowe zadania dotyczące bazy danych, takie jak tworzenie bazy danych i tabel, ładowanie danych i tworzenie zapytań oraz przywracanie bazy danych do wcześniejszego punktu w czasie. W tym samouczku omówiono:
> [!div class="checklist"]
> * Tworzenie bazy danych
> * Konfigurowanie reguły zapory
> * Nawiązywanie połączenia z bazą danych za pomocą programu [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS)
> * Tworzenie tabel
> * Zbiorcze ładowanie danych
> * Tworzenie zapytań dotyczących danych

Przejdź do następnego samouczka, aby dowiedzieć się, jak projektować bazy danych przy użyciu programu Visual Studio i języka C#.

> [!div class="nextstepaction"]
>[Projektowanie bazy danych Azure SQL Database i nawiązywanie połączenia za pomocą języka C# i narzędzia ADO.NET](sql-database-design-first-database-csharp.md)
