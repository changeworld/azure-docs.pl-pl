---
title: Przyrostowe kopiowanie wielu tabel przy użyciu Azure Data Factory | Microsoft Docs
description: W tym samouczku utworzysz potok Azure Data Factory, który przyrostowo kopiuje dane różnicowe z wielu tabel w lokalnej bazie danych SQL Server do bazy danych SQL Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/20/2018
ms.author: yexu
ms.openlocfilehash: 44ae433040c2c9cab47567cb663d4e588311a4a1
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177412"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Przyrostowe ładowanie danych z wielu tabel w SQL Server do bazy danych SQL Azure
W tym samouczku utworzysz fabrykę Azure Data Factory z potokiem, który ładuje dane różnicowe z wielu tabel w SQL Server lokalnym do usługi Azure SQL Database.    

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Przygotuj źródłowe i docelowe magazyny danych.
> * Utwórz fabrykę danych.
> * Utwórz własne środowisko Integration Runtime.
> * Zainstaluj środowisko Integration Runtime. 
> * Utwórz połączone usługi. 
> * Tworzenie zestawów danych źródła, ujścia i znaku wodnego.
> * Tworzenie, uruchamianie i monitorowanie potoku.
> * Przejrzyj wyniki.
> * Dodawanie lub aktualizowanie danych w tabelach źródłowych.
> * Uruchom ponownie i monitoruj potok.
> * Przejrzyj końcowe wyniki.

## <a name="overview"></a>Omówienie
Poniżej przedstawiono ważne kroki, które należy wykonać, aby utworzyć to rozwiązanie: 

1. **Wybierz kolumnę limitu**.
    
    Wybierz jedną kolumnę dla każdej tabeli w źródłowym magazynie danych, która może służyć do identyfikowania nowych lub zaktualizowanych rekordów dla każdego przebiegu. Zwykle dane w tej zaznaczonej kolumnie (na przykład last_modify_time lub ID) ciągle zwiększają się podczas tworzenia lub aktualizowania wierszy. Maksymalna wartość w tej kolumnie jest używana jako znak wodny.

1. **Przygotuj magazyn danych do przechowywania wartości limitu**.   
    
    W tym samouczku zapiszesz wartość limitu w bazie danych SQL.

1. **Utwórz potok z następującymi działaniami**: 
    
    a. Utwórz działanie ForEach, które wykonuje iterację przez listę nazw tabel źródłowych, które są przesyłane jako parametr do potoku. Dla każdej tabeli źródłowej wywołuje następujące działania w celu przeprowadzenia ładowania różnicowego dla tej tabeli.

    b. Utwórz dwa działania wyszukiwania. Użyj pierwszego działania Lookup do pobrania ostatniej wartości limitu. Użyj drugiego działania Lookup do pobrania nowej wartości limitu. Te wartości limitu są przesyłane do działania kopiowania.

    s. Utwórz działanie kopiowania, które kopiuje wiersze z magazynu danych źródłowych o wartości kolumny limitu powyżej starej wartości limitu i mniejszej niż nowa wartość limitu. Następnie kopiuje dane różnicowe ze źródłowego magazynu danych do usługi Azure Blob Storage jako nowy plik.

    Wykres. Utwórz działanie StoredProcedure, które aktualizuje wartość limitu dla potoku, który jest uruchamiany następnym razem. 

    Poniżej znajduje się Diagram rozwiązania wysokiego poziomu: 

    ![Przyrostowe ładowanie danych](media/tutorial-incremental-copy-multiple-tables-portal/high-level-solution-diagram.png)


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="prerequisites"></a>Wymagania wstępne
* **SQL Server**. Lokalna baza danych SQL Server jest używana jako źródłowy magazyn danych w tym samouczku. 
* **Azure SQL Database**. Baza danych SQL jest używana jako magazyn danych ujścia. Jeśli nie masz bazy danych SQL, zapoznaj się z tematem [Tworzenie bazy danych Azure SQL Database](../sql-database/sql-database-get-started-portal.md) . 

### <a name="create-source-tables-in-your-sql-server-database"></a>Tworzenie tabel źródłowych w bazie danych SQL Server

1. Otwórz SQL Server Management Studio i nawiąż połączenie z lokalną bazą danych SQL Server.

1. W **Eksplorator serwera**kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **nowe zapytanie**.

1. Uruchom następujące polecenie SQL względem bazy danych w celu utworzenia tabel o nazwach `customer_table` i `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Tworzenie tabel docelowych w bazie danych SQL Azure
1. Otwórz SQL Server Management Studio i nawiąż połączenie z bazą danych Azure SQL.

1. W **Eksplorator serwera**kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **nowe zapytanie**.

1. Uruchom następujące polecenie SQL względem bazy danych SQL, aby utworzyć tabele o nazwach `customer_table` i `project_table`:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Tworzenie innej tabeli w bazie danych Azure SQL Database do przechowywania wartości górnego limitu
1. Uruchom następujące polecenie SQL względem bazy danych SQL, aby utworzyć tabelę o nazwie `watermarktable` w celu zapisania wartości limitu: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Wstaw początkowe wartości limitu dla obu tabel źródłowych w tabeli znaków wodnych.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Utwórz procedurę przechowywaną w bazie danych SQL Azure 

Uruchom następujące polecenie, aby utworzyć procedurę przechowywaną w bazie danych SQL. Ta procedura składowana aktualizuje wartość limitu po każdym uruchomieniu potoku. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Tworzenie typów danych i dodatkowych procedur składowanych w usłudze Azure SQL Database
Uruchom następujące zapytanie, aby utworzyć dwie procedury składowane i dwa typy danych w bazie danych SQL. Są one używane do scalania danych z tabel źródłowych do tabel docelowych.

Aby ułatwić rozpoczęcie podróży, należy bezpośrednio użyć tych procedur składowanych przekazujących dane różnicowe za pośrednictwem zmiennej tabeli, a następnie scalić je z magazynem docelowym. Należy zachować ostrożność, ponieważ nie jest oczekiwana "duża liczba wierszy różnicowych (więcej niż 100), które mają być przechowywane w zmiennej tabeli.  

Jeśli konieczne jest scalenie dużej liczby wierszy różnicowych z magazynem docelowym, sugerujemy użycie działania kopiowania w celu skopiowania wszystkich danych różnicowych do tymczasowej tabeli "przemieszczania" w magazynie docelowym, a następnie skompilowania własnej procedury składowanej bez użycia tabeli Vari możliwość scalania ich z tabeli "przemieszczania" do tabeli "Final". 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom przeglądarkę sieci Web **Microsoft Edge** lub **Google Chrome** . Obecnie interfejs użytkownika Data Factory jest obsługiwany tylko w przeglądarkach sieci Web Microsoft Edge i Google Chrome.
1. Kliknij pozycję **Nowy** w menu po lewej stronie, kliknij pozycję **dane + analiza**, a następnie kliknij pozycję **Data Factory**. 
   
   ![New-> DataFactory](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory-menu.png)
1. Na stronie **Nowa fabryka danych** wprowadź **ADFMultiIncCopyTutorialDF** w polu **Nazwa**. 
      
     ![Nowa strona fabryki danych](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory.png)
 
   Nazwa fabryki danych Azure musi być **globalnie unikatowa**. Jeśli wystąpi następujący błąd, Zmień nazwę fabryki danych (na przykład yournameADFMultiIncCopyTutorialDF) i spróbuj utworzyć ją ponownie. Zobacz artykuł [reguły nazewnictwa Data Factory](naming-rules.md) , aby poznać reguły nazewnictwa Data Factory artefaktów.
  
       `Data factory name ADFMultiIncCopyTutorialDF is not available`
1. Wybierz swoją **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych. 
1. Dla **grupy zasobów**wykonaj jedną z następujących czynności:
     
      - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
      - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
        Aby dowiedzieć się więcej na temat grup zasobów, zobacz temat [Używanie grup zasobów do zarządzania zasobami platformy Azure](../azure-resource-manager/resource-group-overview.md).  
1. Wybierz pozycję **v2 (wersja zapoznawcza)** **wersji**.
1. Wybierz **lokalizację** fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (Azure Storage, Azure SQL Database itp.) i usługi obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą znajdować się w innych regionach.
1. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**.     
1. Kliknij przycisk **Utwórz**.      
1. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem: **wdrażanie fabryki danych**. 

    ![kafelek wdrażanie fabryki danych](media/tutorial-incremental-copy-multiple-tables-portal/deploying-data-factory.png)
1. Po zakończeniu tworzenia zostanie wyświetlona strona **Data Factory** , jak pokazano na ilustracji.
   
   ![Strona główna fabryki danych](./media/tutorial-incremental-copy-multiple-tables-portal/data-factory-home-page.png)
1. Kliknij przycisk **Utwórz kafelek & Monitoruj** , aby uruchomić Azure Data Factory interfejs użytkownika na oddzielnej karcie.
1. Na stronie wprowadzenie w interfejsie użytkownika Azure Data Factory kliknij pozycję **Utwórz potok** (lub) przejdź do karty **Edycja** . 

   ![Strona wprowadzenie](./media/tutorial-incremental-copy-multiple-tables-portal/get-started-page.png)

## <a name="create-self-hosted-integration-runtime"></a>Tworzenie własnego środowiska Integration Runtime
Podczas przemieszczania danych z magazynu danych w sieci prywatnej (lokalnie) do magazynu danych platformy Azure Zainstaluj własne środowisko Integration Runtime (IR) w środowisku lokalnym. Własne środowisko IR przenosi dane między Twoją siecią prywatną i platformą Azure. 

1. Kliknij pozycję **połączenia** w dolnej części okienka po lewej stronie, a następnie przejdź do obszaru **Integration Runtimes** w oknie **Connections (połączenia** ). 

   ![Karta połączenia](./media/tutorial-incremental-copy-multiple-tables-portal/connections-tab.png)
1. Na karcie **Integration Runtime** kliknij pozycję **+ Nowy**. 

   ![Nowe środowisko Integration Runtime — przycisk](./media/tutorial-incremental-copy-multiple-tables-portal/new-integration-runtime-button.png)
1. W oknie **konfiguracja Integration Runtime** wybierz pozycję **Wykonaj przenoszenie danych i wysyłaj działania do obliczeń zewnętrznych**, a następnie kliknij przycisk **dalej**. 

   ![Wybierz typ środowiska Integration Runtime](./media/tutorial-incremental-copy-multiple-tables-portal/select-integration-runtime-type.png)
1. Wybierz pozycję * * Private Network * *, a następnie kliknij przycisk **dalej**. 

   ![Wybieranie sieci prywatnej](./media/tutorial-incremental-copy-multiple-tables-portal/select-private-network.png)
1. Wprowadź **MySelfHostedIR** jako **nazwę**, a następnie kliknij przycisk **dalej**. 

   ![Nazwa samodzielnego środowiska IR](./media/tutorial-incremental-copy-multiple-tables-portal/self-hosted-ir-name.png)
1. Kliknij **przycisk kliknij tutaj, aby uruchomić instalację ekspresową dla tego komputera** w **opcji 1: Express Setup** . 

   ![Kliknij link instalacji ekspresowej](./media/tutorial-incremental-copy-multiple-tables-portal/click-express-setup.png)
1. W oknie **Integration Runtime (Self-Hosted) Express Setup** kliknij przycisk **Zamknij**. 

   ![Instalacja środowiska Integration Runtime — powodzenie](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
1. W przeglądarce internetowej w oknie **instalatora Integration Runtime** kliknij przycisk **Zakończ**. 

   ![Konfiguracja środowiska Integration Runtime — zakończenie](./media/tutorial-incremental-copy-multiple-tables-portal/click-finish-integration-runtime-setup.png)
1. Upewnij się, że na liście środowisk Integration Runtime jest widoczny **MySelfHostedIR** .

    ![Środowisko Integration Runtime — lista](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtimes-list.png)

## <a name="create-linked-services"></a>Tworzenie połączonych usług
Połączone usługi są tworzone w fabryce danych w celu połączenia magazynów danych i usług obliczeniowych z fabryką danych. W tej sekcji utworzysz połączone usługi do lokalnej bazy danych SQL Server i bazy danych SQL. 

### <a name="create-the-sql-server-linked-service"></a>Tworzenie połączonej usługi SQL Server
W tym kroku połączysz lokalną bazę danych SQL Server z fabryką danych.

1. W oknie **połączenia** przejdź na kartę **Integration Runtime** na karcie **połączone usługi** , a następnie kliknij pozycję **+ Nowy**.

    ![Przycisk Nowa połączona usługa](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
1. W oknie **Nowa połączona usługa** wybierz pozycję **SQL Server**, a następnie kliknij przycisk **Kontynuuj**. 

    ![Wybierz SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server.png)
1. W oknie **Nowa połączona usługa** wykonaj następujące czynności:

    1. Wprowadź **SqlServerLinkedService** dla **nazwy**. 
    1. Wybierz pozycję **MySelfHostedIR** , aby **nawiązać połączenie za pośrednictwem środowiska Integration Runtime**. Jest to **ważny** krok. Domyślne środowisko Integration Runtime nie może nawiązać połączenia z lokalnym magazynem danych. Użyj własnego środowiska Integration Runtime utworzonego wcześniej. 
    1. W polu **Nazwa serwera**wprowadź nazwę komputera, na którym znajduje się SQL Server baza danych.
    1. W polu **Nazwa bazy danych**wprowadź nazwę bazy danych w SQL Server, która zawiera dane źródłowe. W ramach wymagań wstępnych utworzono tabelę i wstawiono dane do tej bazy danych. 
    1. W **polu Typ uwierzytelniania**wybierz **Typ uwierzytelniania** , którego chcesz użyć do nawiązania połączenia z bazą danych. 
    1. W polu **Nazwa użytkownika**wprowadź nazwę użytkownika, który ma dostęp do bazy danych SQL Server. Jeśli musisz użyć znaku ukośnika (`\`) w nazwie konta użytkownika lub serwera, użyj znaku ucieczki (`\`). Przykładem jest `mydomain\\myuser`.
    1. W polu **hasło**wprowadź **hasło** użytkownika. 
    1. Aby sprawdzić, czy Data Factory może nawiązać połączenie z bazą danych SQL Server, kliknij pozycję **Test connection**. Napraw wszelkie błędy do momentu pomyślnego nawiązania połączenia. 
    1. Aby zapisać połączoną usługę, kliknij przycisk **Zapisz**.

        ![SQL Server połączona usługa — ustawienia](./media/tutorial-incremental-copy-multiple-tables-portal/sql-server-linked-service-settings.png)

### <a name="create-the-azure-sql-database-linked-service"></a>Tworzenie połączonej usługi Azure SQL Database
W ostatnim kroku utworzysz połączoną usługę, aby połączyć bazę danych SQL Server źródłowej z fabryką danych. W tym kroku połączysz lokalizację docelową/ujścia usługi Azure SQL Database z fabryką danych. 

1. W oknie **połączenia** przejdź na kartę **Integration Runtime** na karcie **połączone usługi** , a następnie kliknij pozycję **+ Nowy**.

    ![Przycisk Nowa połączona usługa](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
1. W oknie **Nowa połączona usługa** wybierz pozycję **Azure SQL Database**, a następnie kliknij przycisk **Kontynuuj**. 
1. W oknie **Nowa połączona usługa** wykonaj następujące czynności:

    1. Wprowadź **AzureSqlDatabaseLinkedService** dla **nazwy**. 
    1. W polu **Nazwa serwera**wybierz nazwę serwera SQL Azure z listy rozwijanej. 
    1. W polu **Nazwa bazy danych**wybierz bazę danych Azure SQL Database, w której utworzono customer_table i project_table w ramach wymagań wstępnych. 
    1. W polu **Nazwa użytkownika**wprowadź nazwę użytkownika mającego dostęp do bazy danych Azure SQL. 
    1. W polu **hasło**wprowadź **hasło** użytkownika. 
    1. Aby sprawdzić, czy Data Factory może nawiązać połączenie z bazą danych SQL Server, kliknij pozycję **Test connection**. Napraw wszelkie błędy do momentu pomyślnego nawiązania połączenia. 
    1. Aby zapisać połączoną usługę, kliknij przycisk **Zapisz**.

        ![Połączona usługa Azure SQL — ustawienia](./media/tutorial-incremental-copy-multiple-tables-portal/azure-sql-linked-service-settings.png)
1. Upewnij się, że na liście są widoczne dwie połączone usługi. 
   
    ![Dwie połączone usługi](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Tworzenie zestawów danych
W tym kroku utworzysz zestawy danych reprezentujące źródło danych, miejsce docelowe danych oraz miejsce przechowywania znaku wodnego.

### <a name="create-a-source-dataset"></a>Tworzenie zestawu danych źródłowych

1. W lewym okienku kliknij pozycję **+ (plus)** , a następnie kliknij pozycję **zestaw danych**.

   ![Menu Nowy zestaw danych](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. W oknie **Nowy zestaw danych** wybierz pozycję **SQL Server**, a następnie kliknij przycisk **Zakończ**. 

   ![Wybierz SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server-for-dataset.png)
1. Zostanie wyświetlona nowa karta otwarta w przeglądarce sieci Web na potrzeby konfigurowania zestawu danych. Zobaczysz również zestaw danych w widoku TreeView. Na karcie **Ogólne** w okno właściwości w dolnej części wprowadź **SourceDataset** dla **nazwy**. 

   ![Zestaw danych źródłowych — nazwa](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-general.png)
1. Przejdź do karty **połączenie** w okno właściwości i wybierz pozycję **SqlServerLinkedService** dla **połączonej usługi**. Nie należy zaznaczać tabeli w tym miejscu. Działanie kopiowania w potoku używa zapytania SQL w celu załadowania danych zamiast załadowania całej tabeli.

   ![Źródłowy zestaw danych — połączenie](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Tworzenie zestawu danych ujścia
1. W lewym okienku kliknij pozycję **+ (plus)** , a następnie kliknij pozycję **zestaw danych**.

   ![Menu Nowy zestaw danych](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. W oknie **Nowy zestaw danych** wybierz pozycję **Azure SQL Database**i kliknij przycisk **Zakończ**. 

   ![Wybierz Azure SQL Database](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
1. Zostanie wyświetlona nowa karta otwarta w przeglądarce sieci Web na potrzeby konfigurowania zestawu danych. Zobaczysz również zestaw danych w widoku TreeView. Na karcie **Ogólne** w okno właściwości w dolnej części wprowadź **SinkDataset** dla **nazwy**.

   ![Zestaw danych ujścia — ogólne](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-general.png)
1. Przejdź do karty **Parametry** w okno właściwości i wykonaj następujące czynności: 

    1. Kliknij pozycję **Nowy** w sekcji **Parametry tworzenia/aktualizacji** . 
    1. Wprowadź **SinkTableName** dla **nazwy**i **ciąg** dla tego **typu**. Ten zestaw danych pobiera **SinkTableName** jako parametr. Parametr SinkTableName jest ustawiany przez potok dynamicznie w czasie wykonywania. Działanie ForEach w potoku iteruje przez listę nazw tabel i przekazuje nazwę tabeli do tego zestawu danych w każdej iteracji.
   
       ![Zestaw danych ujścia — właściwości](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)
1. Przejdź do karty **połączenie** w okno właściwości i wybierz pozycję **AzureSqlLinkedService** dla **połączonej usługi**. W obszarze Właściwość **tabeli** kliknij pozycję **Dodaj zawartość dynamiczną**. 

   ![Zestaw danych ujścia — połączenie](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection.png)
    
    
1. Wybierz **SinkTableName** w sekcji **Parameters**
   
   ![Zestaw danych ujścia — połączenie](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-dynamicContent.png)

   
 1. Po kliknięciu przycisku **Zakończ**zobaczysz **\@dataset (). SinkTableName** jako nazwę tabeli.
   
   ![Zestaw danych ujścia — połączenie](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-completion.png)

### <a name="create-a-dataset-for-a-watermark"></a>Tworzenie zestawu danych dla limitu
W tym kroku utworzysz zestaw danych do przechowywania wartości górnego limitu. 

1. W lewym okienku kliknij pozycję **+ (plus)** , a następnie kliknij pozycję **zestaw danych**.

   ![Menu Nowy zestaw danych](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. W oknie **Nowy zestaw danych** wybierz pozycję **Azure SQL Database**i kliknij przycisk **Zakończ**. 

   ![Wybierz Azure SQL Database](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
1. Na karcie **Ogólne** w okno właściwości w dolnej części wprowadź **WatermarkDataset** dla **nazwy**.
1. Przejdź do karty **połączenie** i wykonaj następujące czynności: 

    1. Wybierz pozycję **AzureSqlDatabaseLinkedService** dla **połączonej usługi**.
    1. Wybierz pozycję **[dbo]. [ znaki wodne]** dla **tabeli**.

       ![Zestaw danych znaku wodnego — połączenie](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>Tworzenie potoku
Potok pobiera listę nazw tabel jako parametr. Działanie ForEach iteruje przez listę nazw tabel i wykonuje następujące operacje: 

1. Użyj działania Lookup do pobrania starej wartości limitu (wartość początkowa lub taka, która była używana w ostatniej iteracji).

1. Użyj działania Lookup do pobrania nowej wartości limitu (wartość maksymalna kolumny limitu w tabeli źródłowej).

1. Użyj działania kopiowania do skopiowania danych między tymi dwiema wartościami limitu ze źródłowej bazy danych do docelowej bazy danych.

1. Użyj działania StoredProcedure do zaktualizowania starej wartości limitu, która zostanie użyta w pierwszym kroku następnej iteracji. 

### <a name="create-the-pipeline"></a>Tworzenie potoku

1. W lewym okienku kliknij pozycję **+ (plus)** , a następnie kliknij pozycję **potok**.

    ![Nowy potok — menu](./media/tutorial-incremental-copy-multiple-tables-portal/new-pipeline-menu.png)
1. Na karcie **Ogólne** okna **Właściwości** wpisz **IncrementalCopyPipeline** dla **nazwy**. 

    ![Nazwa potoku](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-name.png)
1. W oknie **Właściwości** wykonaj następujące czynności: 

    1. Kliknij pozycję **+ Nowy**. 
    1. Wprowadź **ciąg tablelist jako** dla **nazwy**parametru. 
    1. Wybierz **obiekt** dla **typu**parametru.

    ![Parametry potoku](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-parameters.png) 
1. W przyborniku **działania** rozwiń pozycję **iteracja & Conditional**, a następnie przeciągnij i upuść działanie **foreach** do powierzchni projektanta potoku. Na karcie **Ogólne** okna **Właściwości** wprowadź **wartość iteratesqltables**. 

    ![Działanie ForEach — nazwa](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-name.png)
1. Przejdź do karty **Ustawienia** w oknie **Właściwości** , a następnie wprowadź `@pipeline().parameters.tableList` dla **elementów**. Działanie ForEach iteruje przez listę tabel i wykonuje operację kopiowania przyrostowego. 

    ![Działanie ForEach — ustawienia](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)
1. Wybierz działanie **foreach** w potoku, jeśli nie zostało jeszcze wybrane. Kliknij przycisk **Edytuj (ikona ołówka)** .

    ![Działanie ForEach — Edycja](./media/tutorial-incremental-copy-multiple-tables-portal/edit-foreach.png)
1. W przyborniku **działania** rozwiń pozycję **Ogólne**, przeciągnij i upuść działanie **Lookup (wyszukiwanie** ) do powierzchni projektanta potoku, a następnie wprowadź **wartość lookupoldwatermarkactivity** jako **nazwę**.

    ![Pierwsze działanie Lookup — nazwa](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-name.png)
1. Przejdź do karty **Ustawienia** w oknie **Właściwości** i wykonaj następujące czynności: 

    1. Wybierz pozycję **WatermarkDataset** dla **zestawu danych źródłowych**.
    1. Wybierz pozycję **zapytanie** dla opcji **Użyj zapytania**. 
    1. Wprowadź następujące zapytanie SQL dla **zapytania**. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![Pierwsze działanie Lookup — ustawienia](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
1. Przeciągnij i upuść działanie **Lookup (wyszukiwanie** ) z przybornika **działania** , a następnie wprowadź **LookupNewWaterMarkActivity** w polu **Nazwa**.
        
    ![Drugie działanie Lookup — nazwa](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-name.png)
1. Przejdź do karty **Ustawienia** .

    1. Wybierz pozycję **SourceDataset** dla **zestawu danych źródłowych**. 
    1. Wybierz pozycję **zapytanie** dla opcji **Użyj zapytania**.
    1. Wprowadź następujące zapytanie SQL dla **zapytania**.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Drugie działanie Lookup — ustawienia](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
1. Przeciągnij i upuść działanie **kopiowania** z przybornika **działania** , a następnie wprowadź **wartość incrementalcopyactivity** w polu **Nazwa**. 

    ![Działanie Copy (kopiowanie) — nazwa](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-name.png)
1. Połącz działania **wyszukiwania** z działaniem **kopiowania** po jednym według jednego. Aby nawiązać połączenie, Rozpocznij przeciąganie w **zielonym** polu dołączonym do działania **Lookup (wyszukiwanie** ) i upuść je w działaniu **kopiowania** . Zwolnij przycisk myszy, gdy kolor obramowania działania kopiowania zmieni się na **niebieski**.

    ![Łączenie działań wyszukiwania z działaniem kopiowania](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
1. Wybierz działanie **kopiowania** w potoku. Przejdź do karty **Źródło** w oknie **Właściwości** . 

    1. Wybierz pozycję **SourceDataset** dla **zestawu danych źródłowych**. 
    1. Wybierz pozycję **zapytanie** dla opcji **Użyj zapytania**. 
    1. Wprowadź następujące zapytanie SQL dla **zapytania**.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Działanie Copy (kopiowanie) — ustawienia źródła](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
1. Przejdź do karty **ujścia** i wybierz pozycję **SinkDataset** dla **zestawu danych ujścia**. 
        
    ![Działanie Copy (kopiowanie) — ustawienia ujścia](./media/tutorial-incremental-copy-multiple-tables-portal/copy-sink-settings.png)
1. Wykonaj następujące czynności:

    1. We właściwości **DataSet** dla parametru **SinkTableName** wprowadź `@{item().TABLE_NAME}`.
    1. Dla właściwości **nazwa procedury składowanej** wprowadź `@{item().StoredProcedureNameForMergeOperation}`.
    1. W obszarze Właściwość **typu tabeli** wprowadź wartość `@{item().TableType}`.


        ![Działanie Copy — parametry](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
1. Przeciągnij i upuść działanie **procedury składowanej** z przybornika **działania** na powierzchnię projektanta potoku. Połącz działanie **copy** z działaniem **procedury składowanej** . 

    ![Działanie Copy — parametry](./media/tutorial-incremental-copy-multiple-tables-portal/connect-copy-to-sproc.png)
1. Wybierz działanie **procedury składowanej** w potoku, a następnie wprowadź **StoredProceduretoWriteWatermarkActivity** dla **nazwy** na karcie **Ogólne** w oknie **Właściwości** . 

    ![Działanie procedury składowanej — nazwa](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-name.png)
1. Przejdź do karty **konto SQL** , a następnie wybierz pozycję **AzureSqlDatabaseLinkedService** dla **połączonej usługi**.

    ![Działanie procedury składowanej — konto SQL](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
1. Przejdź do karty **procedura składowana** i wykonaj następujące czynności:

    1. W obszarze **nazwa procedury składowanej**wybierz pozycję `usp_write_watermark`. 
    1. Wybierz pozycję **Importuj parametr**. 
    1. Określ następujące wartości parametrów: 

        | Nazwa | Typ | Wartość | 
        | ---- | ---- | ----- |
        | LastModifiedtime | DataGodzina | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | tableName | String | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Działanie procedury składowanej — ustawienia procedury składowanej](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
1. W lewym okienku kliknij pozycję **Publikuj**. Ta akcja powoduje opublikowanie utworzonych jednostek w usłudze Data Factory. 

    ![Przycisk Publikuj](./media/tutorial-incremental-copy-multiple-tables-portal/publish-button.png)
1. Poczekaj, aż zobaczysz komunikat o błędzie **opublikowano pomyślnie** . Aby wyświetlić powiadomienia, kliknij link **Pokaż powiadomienia** . Zamknij okno powiadomienia, klikając przycisk **X**.

    ![Pokaż powiadomienia](./media/tutorial-incremental-copy-multiple-tables-portal/notifications.png)

 
## <a name="run-the-pipeline"></a>Uruchamianie potoku

1. Na pasku narzędzi dla potoku kliknij pozycję **wyzwalacz**, a następnie kliknij pozycję **Wyzwól teraz**.     

    ![Wyzwól teraz](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. W oknie **uruchomienie potoku** Wprowadź następującą wartość parametru **ciąg tablelist jako** , a następnie kliknij przycisk **Zakończ**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

    ![Argumenty przebiegu potoku](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>Monitorowanie potoku

1. Przejdź do karty **monitorowanie** po lewej stronie. Zobaczysz uruchomienie potoku wyzwalane przez **wyzwalacz ręczny**. Kliknij przycisk **Odśwież** , aby odświeżyć listę. Linki w kolumnie **działania** umożliwiają wyświetlanie uruchomień działań skojarzonych z uruchomieniem potoku oraz ponowne uruchamianie potoku. 

    ![Uruchomienia potoków](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. Kliknij link **Wyświetl uruchomienia działania** w kolumnie **Akcje** . Zobaczysz wszystkie uruchomienia działań skojarzone z wybranym uruchomieniem potoku. 

    ![Uruchomienia działania](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png)

## <a name="review-the-results"></a>Przejrzyj wyniki
W SQL Server Management Studio Uruchom następujące zapytania względem docelowej bazy danych SQL, aby sprawdzić, czy dane zostały skopiowane z tabel źródłowych do tabel docelowych: 

**Zapytanie** 
```sql
select * from customer_table
```

**Output**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Zapytanie**

```sql
select * from project_table
```

**Output**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Zapytanie**

```sql
select * from watermarktable
```

**Output**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Zauważ, że wartości limitu dla obu tabel zostały zaktualizowane. 

## <a name="add-more-data-to-the-source-tables"></a>Dodawanie większej ilości danych do tabel źródłowych

Uruchom następujące zapytanie względem źródłowej bazy danych SQL Server, aby zaktualizować istniejący wiersz w customer_table. Wstaw nowy wiersz do project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Uruchom ponownie potok
1. W oknie przeglądarki sieci Web przejdź do karty **Edycja** po lewej stronie. 
1. Na pasku narzędzi dla potoku kliknij pozycję **wyzwalacz**, a następnie kliknij pozycję **Wyzwól teraz**.   

    ![Wyzwól teraz](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. W oknie **uruchomienie potoku** Wprowadź następującą wartość parametru **ciąg tablelist jako** , a następnie kliknij przycisk **Zakończ**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline-again"></a>Ponownie Monitoruj potok

1. Przejdź do karty **monitorowanie** po lewej stronie. Zobaczysz uruchomienie potoku wyzwalane przez **wyzwalacz ręczny**. Kliknij przycisk **Odśwież** , aby odświeżyć listę. Linki w kolumnie **działania** umożliwiają wyświetlanie uruchomień działań skojarzonych z uruchomieniem potoku oraz ponowne uruchamianie potoku. 

    ![Uruchomienia potoków](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. Kliknij link **Wyświetl uruchomienia działania** w kolumnie **Akcje** . Zobaczysz wszystkie uruchomienia działań skojarzone z wybranym uruchomieniem potoku. 

    ![Uruchomienia działania](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png) 

## <a name="review-the-final-results"></a>Przejrzyj końcowe wyniki
W SQL Server Management Studio Uruchom następujące zapytania względem docelowej bazy danych, aby sprawdzić, czy zaktualizowane/nowe dane zostały skopiowane z tabel źródłowych do tabel docelowych. 

**Zapytanie** 
```sql
select * from customer_table
```

**Output**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Zwróć uwagę na nowe wartości **name** i **LastModifytime** dla **PersonID** dla liczby 3. 

**Zapytanie**

```sql
select * from project_table
```

**Output**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Zwróć uwagę, że wpis **NewProject** został dodany do project_table. 

**Zapytanie**

```sql
select * from watermarktable
```

**Output**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Zauważ, że wartości limitu dla obu tabel zostały zaktualizowane.
     
## <a name="next-steps"></a>Następne kroki
W tym samouczku wykonano następujące czynności: 

> [!div class="checklist"]
> * Przygotuj źródłowe i docelowe magazyny danych.
> * Utwórz fabrykę danych.
> * Utwórz własne środowisko Integration Runtime (IR).
> * Zainstaluj środowisko Integration Runtime.
> * Utwórz połączone usługi. 
> * Tworzenie zestawów danych źródła, ujścia i znaku wodnego.
> * Tworzenie, uruchamianie i monitorowanie potoku.
> * Przejrzyj wyniki.
> * Dodawanie lub aktualizowanie danych w tabelach źródłowych.
> * Uruchom ponownie i monitoruj potok.
> * Przejrzyj końcowe wyniki.

Przejdź do następującego samouczka, aby dowiedzieć się więcej o przekształcaniu danych przy użyciu klastra Spark na platformie Azure:

> [!div class="nextstepaction"]
>[Przyrostowe ładowanie danych z Azure SQL Database do usługi Azure Blob Storage za pomocą technologii Change Tracking](tutorial-incremental-copy-change-tracking-feature-portal.md)


