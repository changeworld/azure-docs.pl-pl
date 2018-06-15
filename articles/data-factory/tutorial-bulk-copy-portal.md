---
title: Zbiorcze kopiowanie danych przy użyciu usługi Azure Data Factory | Microsoft Docs
description: Dowiedz się, jak używać usługi Azure Data Factory i działania kopiowania do zbiorczego kopiowania danych ze źródłowego magazynu danych do docelowego magazynu danych.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 440b07b494b34db7ff3fcdf5d5ac830b165c339d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30173287"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Zbiorcze kopiowanie wielu tabel przy użyciu usługi Azure Data Factory
W tym samouczku przedstawiono **kopiowanie wielu tabel z bazy danych Azure SQL Database do usługi Azure SQL Data Warehouse**. Tego samego wzorca można użyć także w innych scenariuszach kopiowania. Na przykład kopiowanie tabel z programu SQL Server/Oracle do usługi Azure SQL Database/Data Warehouse/Azure Blob, kopiowanie różnych ścieżek z obiektów blob do tabeli bazy danych Azure SQL Database.

> [!NOTE]
> - Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).
> - Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz dostępnej ogólnie wersji 1 usługi Data Factory, zobacz [dokumentację dotyczącą usługi Data Factory w wersji 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Na poziomie ogólnym ten samouczek obejmuje następujące kroki:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie połączonych usług Azure SQL Database, Azure SQL Data Warehouse i Azure Storage.
> * Tworzenie zestawów danych Azure SQL Database i Azure SQL Data Warehouse.
> * Tworzenie potoku w celu wyszukania tabel do skopiowania i innego potoku w celu wykonania samej operacji kopiowania. 
> * Uruchamianie działania potoku.
> * Monitorowanie uruchomień potoku i działań.

W tym samouczku jest używana witryna Azure Portal. Aby dowiedzieć się więcej o zastosowaniu innych narzędzi/zestawów SDK do tworzenia fabryki danych, zapoznaj się z przewodnikami [Szybki start](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Kompletny przepływ pracy
Ten scenariusz obejmuje kilka tabel w usłudze Azure SQL Database, które chcesz skopiować do magazynu SQL Data Warehouse. Oto sekwencja logiczna kroków przepływu pracy, który następuje w potokach:

![Przepływ pracy](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* Pierwszy potok wyszukuje listę tabel, które należy skopiować do magazynów danych ujścia.  Alternatywnie można utrzymywać tabelę metadanych, która zawiera listę wszystkich tabel do skopiowania do magazynu danych ujścia. Następnie potok wywołuje inny potok, który działa na wszystkich tabelach w bazie danych i wykonuje operację kopiowania danych.
* Drugi potok przeprowadza rzeczywiste kopiowanie. Pobiera listę tabel jako parametr. Dla każdej tabeli na liście skopiuj konkretną tabelę w bazie danych Azure SQL Database do odpowiedniej tabeli w magazynie SQL Data Warehouse przy użyciu [kopiowania etapowego za pośrednictwem usługi Blob Storage i bazy PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) w celu uzyskania najlepszej wydajności. W tym przykładzie pierwszy potok przekazuje listę tabel jako wartość parametru. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="prerequisites"></a>Wymagania wstępne
* **Konto usługi Azure Storage**. Konto usługi Azure Storage jest używane jako przejściowy magazyn obiektów blob w operacji kopiowania zbiorczego. 
* **Usługa Azure SQL Database**. Ta baza danych zawiera dane źródłowe. 
* **Magazyn Azure SQL Data Warehouse**. Ten magazyn danych służy do przechowywania danych skopiowanych z bazy SQL Database. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Przygotowywanie bazy danych SQL Database i magazynu SQL Data Warehouse

**Przygotowywanie źródłowej bazy Azure SQL Database**:

Utwórz bazę Azure SQL Database z przykładowymi danymi Adventure Works LT zgodnie z artykułem [Tworzenie bazy danych Azure SQL](../sql-database/sql-database-get-started-portal.md). Ten samouczek kopiuje wszystkie tabele z tej przykładowej bazy danych do magazynu danych SQL.

**Przygotowywanie magazynu ujścia Azure SQL Data Warehouse**:

1. Jeśli nie masz magazynu Azure SQL Data Warehouse, utwórz go, wykonując czynności przedstawione w artykule [Create an Azure SQL Warehouse (Tworzenie magazynu Azure SQL Data Warehouse)](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).

2. Tworzenie odpowiednich schematów tabel w magazynie SQL Data Warehouse. Można użyć [narzędzia migracji](https://www.microsoft.com/download/details.aspx?id=49100) do **migracji schematów** z bazy danych Azure SQL Database do magazynu Azure SQL Data Warehouse. Usługa Azure Data Factory służy do migrowania/kopiowania danych na późniejszym etapie.

## <a name="azure-services-to-access-sql-server"></a>Usługi platformy Azure umożliwiające dostęp do serwera SQL

Zarówno dla bazy SQL Database, jak i dla magazynu SQL Data Warehouse, zezwól usługom Azure na dostęp do serwera SQL. Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** dla serwera SQL Azure zostało **WŁĄCZONE**. Ustawienie to pozwala usłudze Data Factory na odczyt danych z bazy Azure SQL Database i zapis danych w magazynie Azure SQL Data Warehouse. W celu sprawdzenia i włączenia tego ustawienia wykonaj następujące kroki:

1. Kliknij centrum **Więcej usług** po lewej stronie, a następnie kliknij przycisk **Serwery SQL**.
2. Wybierz swój serwer, a następnie kliknij przycisk **Zapora** w obszarze **USTAWIENIA**.
3. Na stronie **Ustawienia zapory** kliknij pozycję **WŁĄCZ** dla ustawienia **Zezwalaj na dostęp do usług platformy Azure**.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. Kliknij przycisk **Nowy** w lewym menu, kliknij pozycję **Dane + analiza**, a następnie kliknij pozycję **Data Factory**. 
   
   ![Nowy-> Fabryka danych](./media/tutorial-bulk-copy-portal/new-azure-data-factory-menu.png)
2. Na stronie **Nowa fabryka danych** wprowadź jako **nazwę** wartość **ADFTutorialBulkCopyDF**. 
      
     ![Strona Nowa fabryka danych](./media/tutorial-bulk-copy-portal/new-azure-data-factory.png)
 
   Nazwa fabryki danych platformy Azure musi być **globalnie unikatowa**. Jeśli dla pola nazwy wystąpi poniższy błąd, zmień nazwę fabryki danych (np. twojanazwaADFTutorialBulkCopyDF). Artykuł [Data Factory — Naming Rules (Usługa Data Factory — reguły nazewnictwa)](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
3. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych. 
4. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:
     
      - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
      - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
      Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
4. Wybierz wartość **V2 (wersja zapoznawcza)** dla **wersji**.
5. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Obecnie usługa Data Factory w wersji 2 umożliwia tworzenie fabryk danych tylko w regionach Wschodnie stany USA, Wschodnie stany USA 2 i Europa Zachodnia. Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.
6. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.     
7. Kliknij przycisk **Utwórz**.
8. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem: **Wdrażanie fabryki danych**. 

    ![kafelek Wdrażanie fabryki danych](media//tutorial-bulk-copy-portal/deploying-data-factory.png)
9. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.
   
    ![Strona główna fabryki danych](./media/tutorial-bulk-copy-portal/data-factory-home-page.png)
10. Kliknij kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić aplikację interfejsu użytkownika usługi Data Factory.
11. Na stronie **Wprowadzenie** przejdź do karty **Edycja** w lewym panelu, jak pokazano na poniższej ilustracji:  

    ![Strona Wprowadzenie](./media/tutorial-bulk-copy-portal/get-started-page.png)

## <a name="create-linked-services"></a>Tworzenie połączonych usług
Połączone usługi są tworzone w celu połączenia magazynów danych i obliczeń z fabryką danych. Połączona usługa ma informacje o połączeniu, których usługa Data Factory używa w czasie wykonywania do nawiązywania z nią połączenia. 

W tym samouczku połączysz magazyny danych Azure SQL Database, Azure SQL Data Warehouse i Azure Blob Storage ze swoją fabryką danych. Usługa Azure SQL Database to magazyn danych będący źródłem. Magazynem danych będących ujściem/docelowym jest usługa Azure SQL Data Warehouse. Usługa Azure Blob Storage służy do przygotowywania danych przed ich załadowaniem do usługi SQL Data Warehouse przy użyciu programu PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Tworzenie źródłowej połączonej usługi Azure SQL Database
W tym kroku utworzysz połączoną usługę służącą do łączenia bazy danych Azure SQL Database z fabryką danych. 

1. Kliknij pozycję **Połączenia** w dolnej części okna, a następnie kliknij pozycję **+ Nowy** na pasku narzędzi. 

    ![Przycisk Nowa połączona usługa](./media/tutorial-bulk-copy-portal/new-linked-service-button.png)
2. W oknie **Nowa połączona usługa** wybierz pozycję **Azure SQL Database**, a następnie kliknij pozycję **Kontynuuj**. 

    ![Wybieranie usługi Azure SQL Database](./media/tutorial-bulk-copy-portal/select-azure-sql-database.png)
3. W oknie **Nowa połączona usługa** wykonaj następujące czynności: 

    1. Wprowadź wartość **AzureSqlDatabaseLinkedService** w polu **Nazwa**. 
    2. W polu **Nazwa serwera** wybierz swój serwer usługi Azure SQL.
    3. W polu **Nazwa bazy danych** wybierz swoją bazę danych usługi Azure SQL. 
    4. Wprowadź **nazwę użytkownika**, aby nawiązać połączenie z bazą danych Azure SQL. 
    5. Wprowadź **hasło** dla użytkownika. 
    6. Aby przetestować połączenie z bazą danych Azure SQL przy użyciu określonych informacji, kliknij pozycję **Testuj połączenie**.
    7. Kliknij pozycję **Zapisz**.

        ![Ustawienia usługi Azure SQL Database](./media/tutorial-bulk-copy-portal/azure-sql-database-settings.png)

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Tworzenie połączonej usługi ujścia Azure SQL Data Warehouse

1. Na karcie **Połączenia** kliknij ponownie pozycję **+ Nowy** na pasku narzędzi. 
2. W oknie **Nowa połączona usługa** wybierz pozycję **Azure SQL Data Warehouse**, a następnie kliknij pozycję **Kontynuuj**. 
3. W oknie **Nowa połączona usługa** wykonaj następujące czynności: 

    1. Wprowadź wartość **AzureSqlDWLinkedService** w polu **Nazwa**. 
    2. W polu **Nazwa serwera** wybierz swój serwer usługi Azure SQL.
    3. W polu **Nazwa bazy danych** wybierz swoją bazę danych usługi Azure SQL. 
    4. Wprowadź **nazwę użytkownika**, aby nawiązać połączenie z bazą danych Azure SQL. 
    5. Wprowadź **hasło** dla użytkownika. 
    6. Aby przetestować połączenie z bazą danych Azure SQL przy użyciu określonych informacji, kliknij pozycję **Testuj połączenie**.
    7. Kliknij pozycję **Zapisz**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Tworzenie przejściowej połączonej usługi Azure Storage
W tym samouczku magazyn obiektów blob platformy Azure służy jako obszar przejściowy, pozwalający na włączenie programu PolyBase w celu podniesienia wydajności kopiowania.

1. Na karcie **Połączenia** kliknij ponownie pozycję **+ Nowy** na pasku narzędzi. 
2. W oknie **Nowa połączona usługa** wybierz pozycję **Azure Blob Storage**, a następnie kliknij pozycję **Kontynuuj**. 
3. W oknie **Nowa połączona usługa** wykonaj następujące czynności: 

    1. Wprowadź wartość **AzureStorageLinkedService** w polu **Nazwa**. 
    2. Wybierz swoje **konto usługi Azure Storage** w polu **Nazwa konta magazynu**.
    4. Kliknij pozycję **Zapisz**.


## <a name="create-datasets"></a>Tworzenie zestawów danych
W tym samouczku utworzysz zestawy danych będące źródłem i ujściem, określające lokalizację przechowywania danych. 

Wejściowy zestaw danych AzureSqlDatabaseDataset odwołuje się do elementu AzureSqlDatabaseLinkedService. Połączona usługa określa parametry połączenia w celu nawiązania połączenia z bazą danych. Zestaw danych określa nazwę bazy danych i tabelę, która zawiera dane źródłowe. 

Wyjściowy zestaw danych AzureSqlDWDataset odwołuje się do elementu AzureSqlDWLinkedService. Połączona usługa określa parametry połączenia w celu nawiązania połączenia z magazynem danych. Zestaw danych określa bazę danych i tabelę, do którego dane są kopiowane. 

W tym samouczku źródłowe i docelowe tabele SQL nie są ustalone w definicjach zestawów danych. W zamian działanie ForEach przekazuje nazwę tabeli w czasie wykonywania do działania kopiowania. 

### <a name="create-a-dataset-for-source-sql-database"></a>Tworzenie zestawu danych źródłowej bazy danych SQL Database

1. Kliknij pozycję **+ (plus)** w lewym okienku, a następnie kliknij pozycję **Zestaw danych**. 

    ![Menu Nowy zestaw danych](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
2. W oknie **Nowy zestaw danych** wybierz pozycję **Azure SQL Database** i kliknij przycisk **Zakończ**. Powinna zostać wyświetlona nowa karta o nazwie **AzureSqlTable1**. 
    
    ![Wybieranie usługi Azure SQL Database](./media/tutorial-bulk-copy-portal/select-azure-sql-database-dataset.png)
3. W oknie właściwości w dolnej części wprowadź wartość **AzureSqlDatabaseDataset** w polu **Nazwa**.

    ![Nazwa zestawu danych będącego źródłem](./media/tutorial-bulk-copy-portal/source-dataset-general.png)
4. Przejdź do karty **Połączenie** i wykonaj następujące czynności: 

    1. Wybierz wartość **AzureSqlDatabaseLinkedService** w polu **Połączona usługa**.
    2. Wybierz dowolną tabelę w polu **Tabela**. Jest to tabela fikcyjna. Zapytanie dotyczące źródłowego zestawu danych jest określane podczas tworzenia potoku. Zapytanie służy do wyodrębniania danych z bazy danych Azure SQL Database. Możesz również kliknąć pole wyboru **Edytuj**, a następnie wprowadzić wartość **dummyName** jako nazwę tabeli. 

    ![Strona połączenia źródłowego zestawu danych](./media/tutorial-bulk-copy-portal/source-dataset-connection-page.png)
 

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Tworzenie zestawu danych ujścia magazynu SQL Data Warehouse

1. Kliknij pozycję **+ (plus)** w lewym okienku, a następnie kliknij pozycję **Zestaw danych**. 
2. W oknie **Nowy zestaw danych** wybierz pozycję **Azure SQL Data Warehouse** i kliknij pozycję **Zakończ**. Powinna zostać wyświetlona nowa karta o nazwie **AzureSqlDWTable1**. 
3. W oknie właściwości w dolnej części wprowadź wartość **AzureSqlDWDataset** w polu **Nazwa**.
4. Przejdź do karty **Połączenia** i wybierz wartość **AzureSqlDatabaseLinkedService** w polu **Połączona usługa**.
5. Przejdź do karty **Parametry**, a następnie kliknij pozycję **+ Nowy**

    ![Strona połączenia źródłowego zestawu danych](./media/tutorial-bulk-copy-portal/sink-dataset-new-parameter-button.png)
6. Wprowadź ciąg **DWTableName** jako nazwę parametru. Jeśli kopiujesz/wklejasz tę nazwę ze strony, upewnij się, że nie ma **znaku spacji na końcu** ciągu **DWTableName**. 
7. W sekcji **Właściwości sparametryzowane** wprowadź `@{dataset().DWTableName}` jako wartość właściwości **tableName**. Właściwość **tableName** zestawu danych jest ustawiana na wartość przekazywaną jako argument parametru **DWTableName**. Działanie ForEach iteruje w obrębie listy tabel i przekazuje je po jednej do działania Copy (Kopiowanie). 
   
    ![Nazwa parametru](./media/tutorial-bulk-copy-portal/dwtablename-tablename.png)

## <a name="create-pipelines"></a>Tworzenie potoków
W tym samouczku tworzysz dwa potoki: **IterateAndCopySQLTables** i **GetTableListAndTriggerCopyData**. 

Potok **GetTableListAndTriggerCopyData** wykonuje dwie czynności:

* Wyszukuje tabelę systemową bazy Azure SQL Database w celu pobrania listy tabel do skopiowania.
* Wyzwala potok **IterateAndCopySQLTables**, który przeprowadza rzeczywiste kopiowanie danych.

Potok **GetTableListAndTriggerCopyData** przyjmuje listę tabel jako parametr. Dla każdej tabeli na liście kopiuje dane z tabeli w bazie Azure SQL Database do usługi Azure SQL Data Warehouse przy użyciu kopiowania przejściowego i programu PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Tworzenie potoku IterateAndCopySQLTables

1. W lewym okienku kliknij pozycję **+ (plus)**, a następnie kliknij pozycję **Potok**.

    ![Menu Nowy potok](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
2. W oknie Właściwości zmień nazwę potoku na **IterateAndCopySQLTables**. 

    ![Nazwa potoku](./media/tutorial-bulk-copy-portal/first-pipeline-name.png)
3. Przejdź do karty **Parametry** i wykonaj następujące czynności: 

    1. Kliknij pozycję **+ Nowy**. 
    2. Wprowadź ciąg **tableList** jako **nazwę** parametru.
    3. Wybierz wartość **Obiekt** w polu **Typ**.

        ![Parametr potoku](./media/tutorial-bulk-copy-portal/first-pipeline-parameter.png)
4. W przyborniku **Działania** rozwiń pozycję **Iteracja i warunki**, a następnie przeciągnij i upuść działanie **ForEach** do powierzchni projektu potoku. Możesz również wyszukać działania w przyborniku **Działania**. W dolnej części okna **Właściwości** wprowadź wartość **IterateSQLTables** w polu **Nazwa**. 

    ![Nazwa działania ForEach](./media/tutorial-bulk-copy-portal/for-each-activity-name.png)
5. Przejdź do karty **Ustawienia**, a następnie wprowadź wartość `@pipeline().parameters.tableList` w polu **Elementy**.

    ![Ustawienia działania ForEach](./media/tutorial-bulk-copy-portal/for-each-activity-settings.png)
6. Aby dodać działanie podrzędne do działania **ForEach**, **kliknij dwukrotnie** działanie ForEach (lub) kliknij pozycję **Edytuj (ikona ołówka)**. Linki akcji dla działania zostaną wyświetlone tylko po wybraniu tych pozycji. 

    ![Nazwa działania ForEach](./media/tutorial-bulk-copy-portal/edit-for-each-activity.png)
7. W przyborniku **Działania** rozwiń węzeł **DataFlow**, a następnie przeciągnij i upuść działanie **Copy** (Kopiowanie) na powierzchni projektanta potoku i zmień nazwę w oknie Właściwości na **CopyData**. Zwróć uwagę na menu linków do stron nadrzędnych w górnej części. IterateAndCopySQLTable to nazwa potoku, a IterateSQLTables to nazwa działania ForEach. Projektant należy do zakresu działania. Aby wrócić do edytora potoku z edytora ForEach, kliknij link w menu linków do stron nadrzędnych. 

    ![Kopiowanie w działaniu ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)
8. Przejdź do karty **Źródło** i wykonaj następujące czynności:

    1. Wybierz wartość **AzureSqlDatabaseDataset** w polu **Źródło danych**. 
    2. Wybierz opcję **Zapytanie** w polu **Zapytanie użytkownika**. 
    3. W obszarze **Zapytanie** wprowadź następujące zapytanie SQL.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

        ![Kopiowanie ustawień źródła](./media/tutorial-bulk-copy-portal/copy-source-settings.png)
9. Przejdź do karty **Ujście** i wykonaj następujące czynności: 

    1. Wybierz pozycję **AzureSqlDWDataset** w polu **Zestaw danych będący ujściem**.
    2. Rozwiń węzeł **Ustawienia programu Polybase** i wybierz pozycję **Zezwól na program Polybase**. 
    3. Usuń zaznaczenie opcji **Użyj wartości domyślnej typu**. 
    4. Wprowadź następujący skrypt SQL w obszarze **Skrypt czyszczenia**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Ustawienia ujścia kopiowania](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
10. Przejdź do karty **Parametry**, w razie potrzeby przewiń w dół w celu wyświetlenia sekcji **Zestaw danych będący ujściem** z parametrem **DWTableName**. Ustaw wartość tego parametru na `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]`.

    ![Parametry ujścia kopiowania](./media/tutorial-bulk-copy-portal/copy-sink-parameters.png)
11. Przejdź do karty **Ustawienia** i wykonaj następujące czynności: 

    1. Wybierz wartość **Prawda** dla opcji **Włącz tryb przejściowy**.
    2. Wybierz wartość **AzureStorageLinkedService** w polu **Połączona usługa konta magazynu**.

        ![Włączanie trybu przejściowego](./media/tutorial-bulk-copy-portal/copy-sink-staging-settings.png)
12. Aby zweryfikować ustawienia potoku, kliknij pozycję **Weryfikuj**. Potwierdź, że weryfikacja nie zwróciła błędu. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję **>>**.

    ![Raport weryfikacji potoku](./media/tutorial-bulk-copy-portal/first-pipeline-validation-report.png)

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Tworzenie potoku GetTableListAndTriggerCopyData

Ten potok wykonuje dwie czynności:

* Wyszukuje tabelę systemową bazy Azure SQL Database w celu pobrania listy tabel do skopiowania.
* Wyzwala potok „IterateAndCopySQLTables”, który przeprowadza rzeczywiste kopiowanie danych.

1. W lewym okienku kliknij pozycję **+ (plus)**, a następnie kliknij pozycję **Potok**.

    ![Menu Nowy potok](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
2. W oknie Właściwości zmień nazwę potoku na **GetTableListAndTriggerCopyData**. 

    ![Nazwa potoku](./media/tutorial-bulk-copy-portal/second-pipeline-name.png)
3. W przyborniku **Działania** rozwiń pozycję **Ogólne**, przeciągnij i upuść działanie **Lookup** (Wyszukiwanie) na powierzchni projektanta i wykonaj poniższe czynności:

    1. Wprowadź wartość **LookupTableList** w polu **Nazwa**. 
    2. Wprowadź wartość **Pobieranie listy tabel z bazy danych Azure SQL Database** w polu **Opis**.

        ![Działanie Lookup (Wyszukiwanie) — strona Ogólne](./media/tutorial-bulk-copy-portal/lookup-general-page.png)
4. Przejdź na stronę **Ustawienia** i wykonaj następujące czynności:

    1. Wybierz wartość **AzureSqlDatabaseDataset** w polu **Źródło danych**. 
    2. Wybierz pozycję **Zapytanie** w polu **Użyj zapytania**. 
    3. W obszarze **Zapytanie** wprowadź następujące zapytanie SQL.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    4. Usuń zaznaczenie pola wyboru **Tylko pierwszy wiersz**.

        ![Działanie Lookup (Wyszukiwanie) — strona ustawień](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
5. Przeciągnij i upuść działanie **Execute Pipeline** (Wykonywanie potoku) z przybornika Działania do powierzchni projektanta potoku i ustaw nazwę na wartość **TriggerCopy**.

    ![Działanie Execute Pipeline (Wykonywanie potoku) — strona Ogólne](./media/tutorial-bulk-copy-portal/execute-pipeline-general-page.png)    
6. Przejdź na stronę **Ustawienia** i wykonaj następujące czynności: 

    1. Wybierz wartość **IterateAndCopySQLTables** w polu **Wywołany potok**. 
    2. Rozwiń sekcję **Zaawansowane**. 
    3. Kliknij pozycję **+ Nowy** w sekcji **Parametry**. 
    4. Wprowadź ciąg **tableList** jako **nazwę** parametru.
    5. Wprowadź `@activity('LookupTableList').output.value` jako **wartość** parametru. Ustawiasz listę wyników działania Lookup (Wyszukiwanie) jako wejście do drugiego potoku. Lista wyników zawiera listę tabel, których dane trzeba skopiować do miejsca docelowego. 

        ![Działanie Execute Pipeline (Wykonywanie potoku) — strona Ustawienia](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)
7. **Połącz** działanie **Lookup** (Wyszukiwanie) z działaniem **Execute Pipeline** (Wykonywanie potoku), przeciągając **zielone pole** dołączone do działania Lookup do lewej strony działania Execute Pipeline.

    ![Łączenie działań Lookup (Wyszukiwanie) i Execute Pipeline (Wykonywanie potoku)](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)
8. Aby zweryfikować potok, kliknij pozycję **Weryfikuj** na pasku narzędzi. Potwierdź, że weryfikacja nie zwróciła błędów. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję **>>**.

    ![Drugi potok — raport weryfikacji](./media/tutorial-bulk-copy-portal/second-pipeline-validation-report.png)
9. Aby opublikować jednostki (zestawy danych, potoki itp.) w usłudze Data Factory, kliknij pozycję **Opublikuj wszystko**. Poczekaj na pomyślne zakończenie publikowania. 

    ![Przycisk Opublikuj](./media/tutorial-bulk-copy-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>Wyzwalanie uruchomienia potoku

1. Sprawdź, czy karta **GetTableListAndTriggerCopyData** jest aktywna. 
2. Kliknij pozycję **Wyzwól**, a następnie kliknij pozycję **Wyzwól teraz**. 

    ![Wyzwól teraz](./media/tutorial-bulk-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. Przejdź do karty **Monitorowanie**. Klikaj pozycję **Odśwież** do momentu wyświetlenia uruchomień obydwu potoków w rozwiązaniu. Kontynuuj odświeżanie listy do momentu wyświetlenia stanu **Powodzenie**. 

    ![Uruchomienia potoków](./media/tutorial-bulk-copy-portal/pipeline-runs.png)
2. Aby wyświetlić uruchomienia działań skojarzone z potokiem GetTableListAndTriggerCopyData, kliknij pierwszy link w obszarze Akcje dla tego potoku. Powinny zostać wyświetlone dwa uruchomienia działania dla tego uruchomienia potoku. 

    ![Uruchomienia działania](./media/tutorial-bulk-copy-portal/activity-runs-1.png)    
3. Aby wyświetlić dane wyjściowe działania **Lookup** (Wyszukiwanie), kliknij link w kolumnie **Dane wyjściowe** dla tego działania. Okno **Dane wyjściowe** można maksymalizować i przywracać. Po przejrzeniu kliknij znak **X**, aby zamknąć okno **Dane wyjściowe**.

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
4. Aby wrócić do widoku **Uruchomienia potoku**, kliknij pozycję **Potoki** w górnej części strony. Kliknij link **Wyświetl uruchomienia działania** (pierwszy link w kolumnie **Akcje**) dla potoku **IterateAndCopySQLTables**. Wyświetlone dane wyjściowe powinny wyglądać jak na poniższej ilustracji: zauważ, że istnieje jedno uruchomienie działania **Copy** (Kopiowanie) dla każdej tabeli w danych wyjściowych działania **Lookup** (Wyszukiwanie). 

    ![Uruchomienia działania](./media/tutorial-bulk-copy-portal/activity-runs-2.png)
5. Sprawdź, czy dane zostały skopiowane do docelowego magazynu SQL Data Warehouse używanego w tym samouczku. 

## <a name="next-steps"></a>Następne kroki
W ramach tego samouczka wykonano następujące procedury: 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie połączonych usług Azure SQL Database, Azure SQL Data Warehouse i Azure Storage.
> * Tworzenie zestawów danych Azure SQL Database i Azure SQL Data Warehouse.
> * Tworzenie potoku w celu wyszukania tabel do skopiowania i innego potoku w celu wykonania samej operacji kopiowania. 
> * Uruchamianie działania potoku.
> * Monitorowanie uruchomień potoku i działań.

Przejdź do poniższego samouczka, aby dowiedzieć się, jak przyrostowo kopiować dane z lokalizacji źródłowej do docelowej:
> [!div class="nextstepaction"]
>[Przyrostowe kopiowanie danych](tutorial-incremental-copy-portal.md)