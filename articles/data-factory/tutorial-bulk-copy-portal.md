---
title: Kopiuj dane zbiorczo przy użyciu Azure Portal
description: Dowiedz się, jak używać usługi Azure Data Factory i działania kopiowania do zbiorczego kopiowania danych ze źródłowego magazynu danych do docelowego magazynu danych.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 06/22/2018
ms.openlocfilehash: c44f1b39ae700fbd11b7c0866e7150d1edec8c4f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439522"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Zbiorcze kopiowanie wielu tabel przy użyciu usługi Azure Data Factory

W tym samouczku przedstawiono **kopiowanie wielu tabel z bazy danych Azure SQL Database do usługi Azure SQL Data Warehouse**. Tego samego wzorca można użyć także w innych scenariuszach kopiowania. Na przykład kopiowanie tabel z programu SQL Server/Oracle do usługi Azure SQL Database/Data Warehouse/Azure Blob, kopiowanie różnych ścieżek z obiektów blob do tabeli bazy danych Azure SQL Database.

> [!NOTE]
> - Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

Na poziomie ogólnym ten samouczek obejmuje następujące kroki:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie połączonych usług Azure SQL Database, Azure SQL Data Warehouse i Azure Storage.
> * Tworzenie zestawów danych Azure SQL Database i Azure SQL Data Warehouse.
> * Tworzenie potoku w celu wyszukania tabel do skopiowania i innego potoku w celu wykonania samej operacji kopiowania. 
> * Uruchom potok.
> * Monitorowanie uruchomień potoku i działań.

W tym samouczku jest używana witryna Azure Portal. Aby dowiedzieć się więcej o zastosowaniu innych narzędzi/zestawów SDK do tworzenia fabryki danych, zapoznaj się z przewodnikami [Szybki start](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Kompletny przepływ pracy
Ten scenariusz obejmuje kilka tabel w usłudze Azure SQL Database, które chcesz skopiować do magazynu SQL Data Warehouse. Oto sekwencja logiczna kroków przepływu pracy, który następuje w potokach:

![Przepływ pracy](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* Pierwszy potok wyszukuje listę tabel, które należy skopiować do magazynów danych ujścia.  Alternatywnie można utrzymywać tabelę metadanych, która zawiera listę wszystkich tabel do skopiowania do magazynu danych ujścia. Następnie potok wywołuje inny potok, który działa na wszystkich tabelach w bazie danych i wykonuje operację kopiowania danych.
* Drugi potok przeprowadza rzeczywiste kopiowanie. Pobiera listę tabel jako parametr. Dla każdej tabeli na liście skopiuj konkretną tabelę w bazie danych Azure SQL Database do odpowiedniej tabeli w magazynie SQL Data Warehouse przy użyciu [kopiowania etapowego za pośrednictwem usługi Blob Storage i bazy PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) w celu uzyskania najlepszej wydajności. W tym przykładzie pierwszy potok przekazuje listę tabel jako wartość parametru. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne
* **Konto usługi Azure Storage**. Konto usługi Azure Storage jest używane jako przejściowy magazyn obiektów blob w operacji kopiowania zbiorczego. 
* **Usługa Azure SQL Database**. Ta baza danych zawiera dane źródłowe. 
* **Magazyn Azure SQL Data Warehouse**. Ten magazyn danych służy do przechowywania danych skopiowanych z bazy SQL Database. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Przygotowywanie bazy danych SQL Database i magazynu SQL Data Warehouse

**Przygotowywanie źródłowej bazy Azure SQL Database**:

Utwórz bazę Azure SQL Database z przykładowymi danymi Adventure Works LT zgodnie z artykułem [Tworzenie bazy danych Azure SQL Database](../sql-database/sql-database-get-started-portal.md). Ten samouczek kopiuje wszystkie tabele z tej przykładowej bazy danych do magazynu danych SQL.

**Przygotowywanie magazynu ujścia Azure SQL Data Warehouse**:

1. Jeśli nie masz magazynu Azure SQL Data Warehouse, utwórz go, wykonując czynności przedstawione w artykule [Create an Azure SQL Warehouse (Tworzenie magazynu Azure SQL Data Warehouse)](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).

1. Tworzenie odpowiednich schematów tabel w magazynie SQL Data Warehouse. Usługa Azure Data Factory służy do migrowania/kopiowania danych na późniejszym etapie.

## <a name="azure-services-to-access-sql-server"></a>Usługi platformy Azure umożliwiające dostęp do serwera SQL

Zarówno dla bazy SQL Database, jak i dla magazynu SQL Data Warehouse, zezwól usługom Azure na dostęp do serwera SQL. Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** dla serwera SQL Azure zostało **WŁĄCZONE**. Ustawienie to pozwala usłudze Data Factory na odczyt danych z bazy Azure SQL Database i zapis danych w magazynie Azure SQL Data Warehouse. 

Aby sprawdzić i włączyć to ustawienie, przejdź do usługi Azure SQL Server > Zabezpieczenia > zapory i sieci wirtualne > Ustaw **opcję Zezwalaj na dostęp do usług platformy Azure** na wartość **włączone**.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. W menu po lewej stronie wybierz pozycję **Utwórz zasób** > **Analytics** > **Data Factory**: ![Data Factory zaznaczenie w okienku "nowe"](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Na stronie **Nowa fabryka danych** wprowadź **ADFTutorialBulkCopyDF** w polu **Nazwa**. 
 
   Nazwa fabryki danych platformy Azure musi być **globalnie unikatowa**. Jeśli dla pola nazwy wystąpi poniższy błąd, zmień nazwę fabryki danych (np. twojanazwaADFTutorialBulkCopyDF). Artykuł [Data Factory — Naming Rules (Usługa Data Factory — reguły nazewnictwa)](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
1. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych. 
1. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:
     
   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
   - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
     Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
1. Wybierz opcję **V2** w obszarze **Wersja**.
1. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.
1. Kliknij przycisk **Utwórz**.
1. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**.
   
1. Kliknij kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić aplikację interfejsu użytkownika usługi Data Factory.
1. Na stronie **wprowadzenie** przejdź do karty **autor** w lewym panelu, jak pokazano na poniższej ilustracji:  

     ![Strona Wprowadzenie](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-linked-services"></a>Tworzenie połączonych usług
Połączone usługi są tworzone w celu połączenia magazynów danych i obliczeń z fabryką danych. Połączona usługa ma informacje o połączeniu, których usługa Data Factory używa do nawiązywania połączenia z magazynem danych w środowisku uruchomieniowym. 

W tym samouczku połączysz magazyny danych Azure SQL Database, Azure SQL Data Warehouse i Azure Blob Storage ze swoją fabryką danych. Usługa Azure SQL Database to magazyn danych będący źródłem. Magazynem danych będących ujściem/docelowym jest usługa Azure SQL Data Warehouse. Usługa Azure Blob Storage służy do przygotowywania danych przed ich załadowaniem do usługi SQL Data Warehouse przy użyciu programu PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Tworzenie źródłowej połączonej usługi Azure SQL Database
W tym kroku utworzysz połączoną usługę służącą do łączenia bazy danych Azure SQL Database z fabryką danych. 

1. Kliknij pozycję **Połączenia** w dolnej części okna, a następnie kliknij pozycję **+ Nowy** na pasku narzędzi. 

    ![Przycisk Nowa połączona usługa](./media/tutorial-bulk-copy-portal/new-linked-service-button.png)
1. W oknie **Nowa połączona usługa** wybierz pozycję **Azure SQL Database**, a następnie kliknij pozycję **Kontynuuj**. 
1. W oknie **Nowa połączona usługa (Azure SQL Database)** wykonaj następujące czynności: 

    a. Wprowadź wartość **AzureSqlDatabaseLinkedService** w polu **Nazwa**.
    
    b. W polu **Nazwa serwera** wybierz swój serwer usługi Azure SQL.
    
    d. W polu **Nazwa bazy danych** wybierz swoją bazę danych Azure SQL Database. 
    
    d. Wprowadź **nazwę użytkownika**, aby nawiązać połączenie z bazą danych Azure SQL Database. 
    
    e. Wprowadź **hasło** dla użytkownika. 

    f. Aby przetestować połączenie z bazą danych Azure SQL Database przy użyciu określonych informacji, kliknij pozycję **Testuj połączenie**.
  
    g. Kliknij pozycję **Kontynuuj**.


### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Tworzenie połączonej usługi ujścia Azure SQL Data Warehouse

1. Na karcie **Połączenia** kliknij ponownie pozycję **+ Nowy** na pasku narzędzi. 
1. W oknie **Nowa połączona usługa** wybierz pozycję **Azure SQL Data Warehouse**, a następnie kliknij pozycję **Kontynuuj**. 
1. W oknie **Nowa połączona usługa (Azure SQL Data Warehouse)** wykonaj następujące czynności: 
   
    a. Wprowadź wartość **AzureSqlDWLinkedService** w polu **Nazwa**.
     
    b. W polu **Nazwa serwera** wybierz swój serwer usługi Azure SQL.
     
    d. W polu **Nazwa bazy danych** wybierz swoją bazę danych Azure SQL Database. 
     
    d. Wprowadź **nazwę użytkownika**, aby nawiązać połączenie z bazą danych Azure SQL Database. 
     
    e. Wprowadź **hasło** dla użytkownika. 
     
    f. Aby przetestować połączenie z bazą danych Azure SQL Database przy użyciu określonych informacji, kliknij pozycję **Testuj połączenie**.
     
    g. Kliknij pozycję **Kontynuuj**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Tworzenie przejściowej połączonej usługi Azure Storage
W tym samouczku magazyn obiektów blob platformy Azure służy jako obszar przejściowy, pozwalający na włączenie programu PolyBase w celu podniesienia wydajności kopiowania.

1. Na karcie **Połączenia** kliknij ponownie pozycję **+ Nowy** na pasku narzędzi. 
1. W oknie **Nowa połączona usługa** wybierz pozycję **Azure Blob Storage**, a następnie kliknij pozycję **Kontynuuj**. 
1. W oknie **Nowa połączona usługa (Azure Blob Storage)** wykonaj następujące czynności: 

    a. Wprowadź wartość **AzureStorageLinkedService** w polu **Nazwa**.                                                     
    
    b. Wybierz swoje **konto usługi Azure Storage** w polu **Nazwa konta magazynu**.
    
    d. Kliknij pozycję **Kontynuuj**.


## <a name="create-datasets"></a>Utwórz zestawy danych
W tym samouczku utworzysz zestawy danych będące źródłem i ujściem, określające lokalizację przechowywania danych. 

Wejściowy zestaw danych **AzureSqlDatabaseDataset** odwołuje się do elementu **AzureSqlDatabaseLinkedService**. Połączona usługa określa parametry połączenia w celu nawiązania połączenia z bazą danych. Zestaw danych określa nazwę bazy danych i tabelę, która zawiera dane źródłowe. 

Wyjściowy zestaw danych **AzureSqlDWDataset** odwołuje się do elementu **AzureSqlDWLinkedService**. Połączona usługa określa parametry połączenia w celu nawiązania połączenia z magazynem danych. Zestaw danych określa bazę danych i tabelę, do którego dane są kopiowane. 

W tym samouczku źródłowe i docelowe tabele SQL nie są ustalone w definicjach zestawów danych. W zamian działanie ForEach przekazuje nazwę tabeli w czasie wykonywania do działania kopiowania. 

### <a name="create-a-dataset-for-source-sql-database"></a>Tworzenie zestawu danych źródłowej bazy danych SQL Database

1. Kliknij pozycję **+ (plus)** w okienku po lewej stronie, a następnie kliknij pozycję **zestaw danych**. 

    ![Menu Nowy zestaw danych](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. W oknie **Nowy zestaw danych** wybierz pozycję **Azure SQL Database**, a następnie kliknij przycisk **Kontynuuj**. 
    
1. W oknie **dialogowym Ustawianie właściwości** w obszarze **Nazwa**wprowadź **AzureSqlDatabaseDataset**. W obszarze **połączona usługa**wybierz pozycję **AzureSqlDatabaseLinkedService**. Następnie kliknij pozycję **Kontynuuj**.
1. Przejdź do karty **połączenie** , a następnie wybierz dowolną tabelę dla **tabeli**. Jest to tabela fikcyjna. Zapytanie dotyczące źródłowego zestawu danych jest określane podczas tworzenia potoku. Zapytanie służy do wyodrębniania danych z bazy danych Azure SQL Database. Możesz również kliknąć pole wyboru **Edytuj**, a następnie wprowadzić wartość **dummyName** jako nazwę tabeli. 
 

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Tworzenie zestawu danych ujścia magazynu SQL Data Warehouse

1. Kliknij pozycję **+ (plus)** w lewym okienku, a następnie kliknij pozycję **Zestaw danych**. 
1. W oknie **Nowy zestaw danych** wybierz pozycję **Azure SQL Data Warehouse**, a następnie kliknij przycisk **Kontynuuj**.
1. W oknie **dialogowym Ustawianie właściwości** w obszarze **Nazwa**wprowadź **AzureSqlDWDataset**. W obszarze **połączona usługa**wybierz pozycję **AzureSqlDatabaseLinkedService**. Następnie kliknij pozycję **Kontynuuj**.
1. Przejdź do karty **Parametry**, a następnie kliknij pozycję **+ Nowy** i wprowadź ciąg **DWTableName** jako nazwę parametru. Jeśli skopiujesz/wkleisz tę nazwę ze strony, upewnij się, że na końcu elementu **DWTableName**nie ma końcowego **znaku spacji** .
1. Przejdź do karty **Połączenie**. 

    a. W obszarze **Tabela** wybierz opcję **Edytuj**, kliknij w polu wprowadzania nazwy tabeli, a następnie kliknij link **Dodaj zawartość dynamiczną** poniżej. 

    b. Na stronie **Dodaj zawartość dynamiczną** kliknij pozycję **DWTAbleName** w obszarze **Parametry**, co spowoduje automatyczne wypełnienie `@dataset().DWTableName`pola tekstowego wyrażenie Top, a następnie kliknij przycisk **Zakończ**. Właściwość **tableName** zestawu danych jest ustawiana na wartość przekazywaną jako argument parametru **DWTableName**. Działanie ForEach iteruje w obrębie listy tabel i przekazuje je po jednej do działania Copy (Kopiowanie). 

    ![Konstruktor parametrów zestawu danych](./media/tutorial-bulk-copy-portal/dataset-parameter-builder.png)
 
## <a name="create-pipelines"></a>Utwórz potoki
W tym samouczku tworzysz dwa potoki: **IterateAndCopySQLTables** i **GetTableListAndTriggerCopyData**. 

Potok **GetTableListAndTriggerCopyData** wykonuje dwie czynności:

* Wyszukuje tabelę systemową bazy Azure SQL Database w celu pobrania listy tabel do skopiowania.
* Wyzwala potok **IterateAndCopySQLTables**, który przeprowadza rzeczywiste kopiowanie danych.

Potok **IterateAndCopySQLTables** pobiera listę tabel jako parametr. Dla każdej tabeli na liście kopiuje dane z tabeli w bazie Azure SQL Database do usługi Azure SQL Data Warehouse przy użyciu kopiowania przejściowego i programu PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Tworzenie potoku IterateAndCopySQLTables

1. W lewym okienku kliknij pozycję **+ (plus)** , a następnie kliknij pozycję **Potok**.

    ![Menu Nowy potok](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. Na karcie **Ogólne** określ jako nazwę wartość **IterateAndCopySQLTables**. 

1. Przejdź do karty **Parametry** i wykonaj następujące czynności: 

    a. Kliknij pozycję **+ Nowy**. 
    
    b. Wprowadź ciąg **tableList** jako **nazwę** parametru.
    
    d. Wybierz wartość **Array** w polu **Typ**.

1. W przyborniku **Działania** rozwiń pozycję **Iteracja i warunki**, a następnie przeciągnij i upuść działanie **ForEach** do powierzchni projektu potoku. Możesz również wyszukać działania w przyborniku **Działania**. 

    a. Na karcie **Ogólne** u dołu wprowadź wartość **IterateSQLTables** w polu **Nazwa**. 

    b. Przejdź do karty **Ustawienia** , kliknij pole wejściowe dla **pozycji elementy**, a następnie kliknij link **Dodaj zawartość dynamiczną** poniżej. 

    d. Na stronie **Dodaj zawartość dynamiczną** Zwiń sekcje **systemowe** i **funkcje** , kliknij **ciąg tablelist jako** w obszarze **Parametry**, co spowoduje automatyczne wypełnienie pola tekstowego pierwsze wyrażenie jako `@pipeline().parameter.tableList`. Następnie kliknij przycisk **Zakończ**. 

    ![Konstruktor parametru ForEach](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Przejdź do karty **Działania**, kliknij pozycję **Dodaj działanie**, aby dodać działanie podrzędne do działania **ForEach**.

1. W przyborniku **działania** rozwiń pozycję **Przenieś & transfer**, a następnie przeciągnij i upuść działanie **Kopiowanie danych** do powierzchni projektanta potoku. Zwróć uwagę na menu linków do stron nadrzędnych w górnej części. **IterateAndCopySQLTable** jest nazwą potoku, a **wartość iteratesqltables** jest nazwą działania foreach. Projektant należy do zakresu działania. Aby przełączyć się z powrotem do edytora potoku z edytora ForEach, można kliknąć link w menu łączy. 

    ![Kopiowanie w działaniu ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. Przejdź do karty **Źródło** i wykonaj następujące czynności:

    1. Wybierz wartość **AzureSqlDatabaseDataset** w polu **Źródło danych**. 
    1. Wybierz opcję **zapytania** dla opcji **Użyj zapytania**. 
    1. Kliknij pole wprowadzania **Zapytanie** -> wybierz pozycję **Dodaj zawartość dynamiczną** poniżej -> wprowadź poniższe wyrażenie w polu **Zapytanie** -> wybierz przycisk **Zakończ**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 


1. Przejdź do karty **Ujście** i wykonaj następujące czynności: 

    1. Wybierz pozycję **AzureSqlDWDataset** w polu **Zestaw danych będący ujściem**.
    1. Kliknij pole wejściowe dla wartości parametru DWTableName-> wybierz pozycję **Dodaj zawartość dynamiczną** poniżej, wprowadź wyrażenie `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` jako skrypt, > wybierz pozycję **Zakończ**.
    1. Zaznacz pole wyboru **Zezwalaj na użycie bazy**. 
    1. Usuń zaznaczenie opcji **Użyj wartości domyślnej typu**. 
    1. Kliknij pole wprowadzania **Skrypt wstępnego kopiowania** -> wybierz pozycję **Dodaj zawartość dynamiczną** poniżej -> wprowadź poniższe wyrażenie jako skrypt -> wybierz przycisk **Zakończ**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Ustawienia ujścia kopiowania](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
1. Przejdź do karty **Ustawienia** i wykonaj następujące czynności: 

    1. Zaznacz pole wyboru **Włącz przemieszczanie**.
    1. Wybierz wartość **AzureStorageLinkedService** w polu **Połączona usługa konta magazynu**.

1. Aby zweryfikować ustawienia potoku, kliknij pozycję **Weryfikuj** na górnym pasku narzędzi dla potoku. Upewnij się, że nie wystąpił błąd sprawdzania poprawności. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję **>>** .

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Tworzenie potoku GetTableListAndTriggerCopyData

Ten potok wykonuje dwie czynności:

* Wyszukuje tabelę systemową bazy Azure SQL Database w celu pobrania listy tabel do skopiowania.
* Wyzwala potok „IterateAndCopySQLTables”, który przeprowadza rzeczywiste kopiowanie danych.

1. W lewym okienku kliknij pozycję **+ (plus)** , a następnie kliknij pozycję **Potok**.
1. Na karcie **Ogólne** Zmień nazwę potoku na **GetTableListAndTriggerCopyData**. 

1. W przyborniku **działania** rozwiń węzeł **Ogólne**, a następnie przeciągnij i upuść działanie **Lookup (wyszukiwanie** ) do powierzchni projektanta potoku i wykonaj następujące czynności:

    1. Wprowadź wartość **LookupTableList** w polu **Nazwa**. 
    1. Wprowadź wartość **Pobieranie listy tabel z bazy danych Azure SQL Database** w polu **Opis**.

1. Przejdź do karty **Ustawienia** i wykonaj następujące czynności:

    1. Wybierz wartość **AzureSqlDatabaseDataset** w polu **Źródło danych**. 
    1. Wybierz pozycję **Zapytanie** w polu **Użyj zapytania**. 
    1. W obszarze **Zapytanie** wprowadź następujące zapytanie SQL.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Usuń zaznaczenie pola wyboru **Tylko pierwszy wiersz**.

        ![Działanie Lookup (Wyszukiwanie) — strona ustawień](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Przeciągnij i upuść działanie **Wykonaj potoku** z przybornika działania na powierzchnię projektanta potoku i Ustaw nazwę na **TriggerCopy**.

1. Przejdź do karty **Ustawienia** i wykonaj następujące czynności: 

    1. Wybierz wartość **IterateAndCopySQLTables** w polu **Wywołany potok**. 
    1. Rozwiń sekcję **Zaawansowane**. 
    1. Kliknij pozycję **+ Nowy** w sekcji **Parametry**. 
    1. Wprowadź ciąg **tableList** jako **nazwę** parametru.
    1. Kliknij pole wprowadzania WARTOŚĆ -> wybierz pozycję **Dodaj zawartość dynamiczną** poniżej -> wprowadź `@activity('LookupTableList').output.value` jako wartość nazwy tabeli -> wybierz przycisk **Zakończ**. Ustawiasz listę wyników działania wyszukiwania jako dane wejściowe do drugiego potoku. Lista wyników zawiera listę tabel, których dane trzeba skopiować do miejsca docelowego. 

        ![Działanie Execute Pipeline (Wykonywanie potoku) — strona Ustawienia](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)
1. Aby **połączyć** działanie **Lookup** z działaniem **Wykonaj potok** , przeciągnij **zielony pole** dołączone do działania Lookup (wyszukiwanie) na lewo od działania wykonaj potoku.

    ![Łączenie działań Lookup (Wyszukiwanie) i Execute Pipeline (Wykonywanie potoku)](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)
1. Aby zweryfikować potok, kliknij pozycję **Weryfikuj** na pasku narzędzi. Potwierdź, że weryfikacja nie zwróciła błędów. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję **>>** .

1. Aby opublikować jednostki (zestawy danych, potoki itp.) w usłudze Data Factory, kliknij pozycję **Opublikuj wszystko** w górnej części okna. Poczekaj na pomyślne zakończenie publikowania. 

## <a name="trigger-a-pipeline-run"></a>Wyzwalanie uruchomienia potoku

Przejdź do **GetTableListAndTriggerCopyData**potoku, kliknij pozycję **Dodaj wyzwalacz**, a następnie kliknij pozycję **Wyzwól teraz**. 

## <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. Przejdź do karty **monitorowanie** . kliknij pozycję **Odśwież** , aby zobaczyć przebiegi dla potoków w rozwiązaniu. Kontynuuj odświeżanie listy do momentu wyświetlenia stanu **Powodzenie**. 

1. Aby wyświetlić uruchomienia działań skojarzone z potokiem **GetTableListAndTriggerCopyData** , kliknij pierwszy link w linku akcje dla tego potoku. Powinny zostać wyświetlone dwa uruchomienia działania dla tego uruchomienia potoku. 

1. Aby wyświetlić dane wyjściowe działania **Lookup** (Wyszukiwanie), kliknij link w kolumnie **Dane wyjściowe** dla tego działania. Okno **Dane wyjściowe** można maksymalizować i przywracać. Po przejrzeniu kliknij znak **X**, aby zamknąć okno **Dane wyjściowe**.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
1. Aby wrócić do widoku **Uruchomienia potoku**, kliknij pozycję **Potoki** w górnej części strony. Kliknij link **Wyświetl uruchomienia działania** (pierwszy link w kolumnie **Akcje**) dla potoku **IterateAndCopySQLTables**. Zauważ, że dla każdej tabeli w danych wyjściowych działania **Lookup** jest uruchomione jedno działanie **kopiowania** . 

1. Sprawdź, czy dane zostały skopiowane do docelowego magazynu SQL Data Warehouse używanego w tym samouczku. 

## <a name="next-steps"></a>Następne kroki
W ramach tego samouczka wykonano następujące procedury: 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie połączonych usług Azure SQL Database, Azure SQL Data Warehouse i Azure Storage.
> * Tworzenie zestawów danych Azure SQL Database i Azure SQL Data Warehouse.
> * Tworzenie potoku w celu wyszukania tabel do skopiowania i innego potoku w celu wykonania samej operacji kopiowania. 
> * Uruchom potok.
> * Monitorowanie uruchomień potoku i działań.

Przejdź do poniższego samouczka, aby dowiedzieć się, jak przyrostowo kopiować dane z lokalizacji źródłowej do docelowej:
> [!div class="nextstepaction"]
>[Przyrostowe kopiowanie danych](tutorial-incremental-copy-portal.md)
