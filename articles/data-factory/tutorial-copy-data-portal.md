---
title: Tworzenie potoku fabryki danych za pomocą witryny Azure Portal | Microsoft Docs
description: Ten samouczek zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych z potokiem za pomocą witryny Azure Portal. Potok wykorzystuje działanie kopiowania do skopiowania danych z usługi Azure Blob Storage do bazy danych SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: 38c9c97af0be77bf9ad4bea2d24676c7448b3aea
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447559"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Kopiowanie danych z usługi Azure Blob Storage do bazy danych SQL za pomocą usługi Azure Data Factory
W tym samouczku utworzysz fabrykę danych przy użyciu interfejsu użytkownika usługi Azure Data Factory. Potok w tej fabryce danych kopiuje dane z usługi Azure Blob Storage do bazy danych SQL. Wzorzec konfiguracji w tym samouczku ma zastosowanie do kopiowania danych z magazynu opartego na plikach do relacyjnego magazynu danych. Aby zapoznać się z listą magazynów danych obsługiwanych jako źródła i ujścia, zobacz tabelę zawierającą [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Jeśli jesteś nowym użytkownikiem usługi Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

Ten samouczek obejmuje wykonanie następujących kroków:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku z działaniem kopiowania.
> * Testowe uruchamianie potoku.
> * Ręczne wyzwalanie potoku.
> * Wyzwalanie potoku zgodnie z harmonogramem.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* **Konto usługi Azure Storage**. Magazyn obiektów blob jest używany jako magazyn danych będący *źródłem*. Jeśli nie masz konta magazynu, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu platformy Azure](../storage/common/storage-quickstart-create-account.md).
* **Usługa Azure SQL Database**. Baza danych jest używana jako magazyn danych będący *ujściem*. Jeśli nie masz bazy danych SQL, utwórz ją, wykonując czynności przedstawione w artykule [Tworzenie bazy danych SQL](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Tworzenie obiektu blob i tabeli SQL

Teraz przygotuj swój magazyn obiektów blob i bazę danych SQL na potrzeby tego samouczka, wykonując następujące kroki.

#### <a name="create-a-source-blob"></a>Tworzenie źródłowego obiektu Blob

1. Uruchom program Notatnik. Skopiuj poniższy tekst i zapisz go na dysku jako plik **emp.txt**:

    ```
    John,Doe
    Jane,Doe
    ```

1. W magazynie obiektów blob utwórz kontener o nazwie **adftutorial**. W tym kontenerze utwórz folder o nazwie **input**. Następnie przekaż plik **emp.txt** do folderu **input**. Do wykonania tych zadań użyj witryny Azure Portal lub narzędzi takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Tworzenie tabeli SQL ujścia

1. Utwórz tabelę **dbo.emp** w bazie danych SQL przy użyciu poniższego skryptu SQL:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

1. Zezwól usługom platformy Azure na dostęp do programu SQL Server. Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone i ma wartość **WŁĄCZ** dla programu SQL Server, aby usługa Data Factory mogła zapisywać dane w programie SQL Server. W celu sprawdzenia i włączenia tego ustawienia wykonaj następujące kroki:

    a. Po lewej stronie wybierz pozycję **Więcej usług** > **Serwery SQL**.

    b. Wybierz swój serwer, a następnie wybierz pozycję **Zapora** w obszarze **USTAWIENIA**.

    c. Na stronie **Ustawienia zapory** wybierz pozycję **WŁĄCZ** dla ustawienia **Zezwalaj na dostęp do usług platformy Azure**.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
W tym kroku utworzysz fabrykę danych i uruchomisz interfejs użytkownika usługi Data Factory, aby utworzyć potok w fabryce danych. 

1. Otwórz przeglądarkę **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
2. W menu po lewej stronie wybierz **Utwórz zasób** > **dane + analiza** > **usługi Data Factory**: 
  
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

3. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**. 
      
     ![Nowa fabryka danych](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Nazwa fabryki danych platformy Azure musi być *globalnie unikatowa*. Jeśli dla pola nazwy zobaczysz poniższy komunikat o błędzie, zmień nazwę fabryki danych (np. twojanazwaADFTutorialDataFactory). Reguły nazewnictwa dla artefaktów usługi Data Factory można znaleźć w artykule [Data Factory — reguły nazewnictwa](naming-rules.md).
  
   ![Komunikat o błędzie](./media/tutorial-copy-data-portal/name-not-available-error.png)
4. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych. 
5. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
     
    a. Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

    b. Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/resource-group-overview.md). 
6. W obszarze **Wersja** wybierz pozycję **V2**.
7. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez fabrykę danych mogą znajdować się w innych regionach.
8. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**. 
9. Wybierz pozycję **Utwórz**. 
10. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem **Wdrażanie fabryki danych**: 

    ![Kafelek Wdrażanie fabryki danych](media/tutorial-copy-data-portal/deploying-data-factory.png)
1. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.
   
    ![Strona główna fabryki danych](./media/tutorial-copy-data-portal/data-factory-home-page.png)
1. Wybierz pozycję **Tworzenie i monitorowanie**, aby uruchomić interfejs użytkownika usługi Data Factory na osobnej karcie.

## <a name="create-a-pipeline"></a>Tworzenie potoku
W tym kroku utworzysz potok z działaniem kopiowania w fabryce danych. Działanie kopiowania kopiuje dane z magazynu obiektów blob do usługi SQL Database. W [samouczku szybkiego startu](quickstart-create-data-factory-portal.md) utworzono potok, wykonując następujące czynności:

1. Utworzenie połączonej usługi. 
1. Utworzenie wejściowych i wyjściowych zestawów danych.
1. Tworzenie potoku.

W tym samouczku zaczniesz od utworzenia potoku. Następnie utworzysz usługi połączone i zestawy danych, gdy będą potrzebne do skonfigurowania potoku. 

1. Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok**. 

   ![Tworzenie potoku](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
1. Na karcie **Ogólne** dla potoku w polu **Nazwa** wprowadź wartość **CopyPipeline**.

1. W przyborniku **Działania** rozwiń kategorię **Przenoszenie i przekształcanie**, a następnie przeciągnij działanie **Kopiowanie danych** z przybornika i upuść je na powierzchni projektanta potoku. Wprowadź wartość **CopyFromBlobToSql** w polu **Nazwa**.

    ![Działanie kopiowania](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Konfigurowanie źródła

1. Przejdź do karty **Źródło**. Wybierz pozycję **+ Nowy**, aby utworzyć źródłowy zestaw danych. 

1. W oknie **Nowy zestaw danych** wybierz pozycję **Azure Blob Storage**, a następnie wybierz przycisk **Zakończ**. Dane źródłowe znajdują się w magazynie obiektów blob, musisz więc wybrać usługę **Azure Blob Storage** dla źródłowego zestawu danych. 

    ![Wybór magazynu](./media/tutorial-copy-data-portal/select-azure-blob-dataset.png)

1. Zostanie otwarta nowa karta dla tego zestawu danych obiektu blob. Na karcie **Ogólne** w dolnej części okna **Właściwości** w polu **Nazwa** wprowadź wartość **SourceBlobDataset**.

    ![Nazwa zestawu danych](./media/tutorial-copy-data-portal/dataset-name.png)

1. W oknie **Właściwości** przejdź do karty **Połączenie**. Kliknij pozycję **+ Nowy** obok pola tekstowego **Połączona usługa**. 

    ![Przycisk Nowa połączona usługa](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)

1. W oknie **Nowa połączona usługa** wprowadź jako nazwę **AzureStorageLinkedService**, wybierz konto magazynu z listy **Nazwa konta magazynu**, a następnie wybierz przycisk **Zapisz**, aby wdrożyć połączoną usługę.

    ![Nowa połączona usługa](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)

1. Po utworzeniu połączonej usługi wrócisz do ustawień zestawu danych. Wybierz przycisk **Przeglądaj** obok pozycji **Ścieżka pliku**.

    ![Przycisk Przeglądaj do wyszukiwania ścieżki pliku](./media/tutorial-copy-data-portal/file-browse-button.png)

1. Przejdź do folderu **adftutorial/input**, wybierz plik **emp.txt**, a następnie wybierz przycisk **Zakończ**. 

    ![Wybieranie pliku wejściowego](./media/tutorial-copy-data-portal/select-input-file.png)

1. Upewnij się, że w obszarze **Format pliku** jest ustawiona wartość **Format tekstowy**, a w obszarze **Ogranicznik kolumny** jest ustawiona wartość **Przecinek (`,`)**. Jeśli plik źródłowy używa innych ograniczników wiersza i kolumny, możesz wybrać pozycję **Wykryj format tekstowy** w obszarze **Format pliku**. Narzędzie do kopiowania danych automatycznie wykrywa format pliku i ograniczniki. Ciągle możesz zastąpić te wartości. Aby wyświetlić podgląd danych na tej stronie, wybierz pozycję **Podgląd danych**.

    ![Wykrywanie formatu tekstowego](./media/tutorial-copy-data-portal/detect-text-format.png)

1. Przejdź do karty **Schemat** w oknie **Właściwości** i wybierz pozycję **Importuj schemat**. Zwróć uwagę, że aplikacja wykryła dwie kolumny w pliku źródłowym. W tym miejscu importuje się schemat, dzięki czemu można mapować kolumny z magazynu danych będącego źródłem na magazyn danych będący ujściem. Jeśli nie ma potrzeby mapowania kolumn, możesz pominąć ten krok. W tym samouczku zaimportuj schemat.

    ![Wykrywanie schematu źródłowego](./media/tutorial-copy-data-portal/detect-source-schema.png)  

1. Teraz wróć do potoku, do karty **Źródło**. Upewnij się, że wybrano wartość **SourceBlobDataset**. Aby wyświetlić podgląd danych na tej stronie, wybierz pozycję **Podgląd danych**. 
    
    ![Zestaw danych źródłowych](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Konfigurowanie ujścia

1. Przejdź do karty **Ujście**, a następnie wybierz pozycję **+ Nowy**, aby utworzyć zestaw danych będący ujściem. 

    ![Zestaw danych będący ujściem](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
1. W **nowy zestaw danych** oknie input "SQL" w polu wyszukiwania, aby filtrować łączników, a następnie wybierz **usługi Azure SQL Database**, a następnie wybierz pozycję **Zakończ**. W tym samouczku skopiujesz dane do bazy danych SQL. 

    ![Wybieranie bazy danych SQL](./media/tutorial-copy-data-portal/select-azure-sql-dataset.png)
1. Na karcie **Ogólne** w oknie **Właściwości** w polu **Nazwa** wpisz wartość **OutputSqlDataset**. 
    
    ![Nazwa zestawu danych wyjściowych](./media/tutorial-copy-data-portal/output-dataset-name.png)
1. Przejdź do karty **Połączenie** i wybierz pozycję **+ Nowy** obok obszaru **Połączona usługa**. Zestaw danych musi być skojarzony z połączoną usługą. Połączona usługa ma parametry połączenia, których usługa Data Factory używa do nawiązywania połączenia z usługą SQL Database w środowisku uruchomieniowym. Zestaw danych określa kontener, folder i plik (opcjonalnie), do którego dane są kopiowane. 
    
    ![Połączona usługa](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
1. W oknie **Nowa połączona usługa** wykonaj następujące kroki: 

    a. W obszarze **Nazwa** wprowadź wartość **AzureSqlDatabaseLinkedService**.

    b. W polu **Nazwa serwera** wybierz swoje wystąpienie programu SQL Server.

    c. W polu **Nazwa bazy danych** wybierz swoją usługę SQL Database.

    d. W polu **Nazwa użytkownika** wprowadź nazwę użytkownika.

    e. W polu **Hasło** wprowadź hasło użytkownika.

    f. Wybierz pozycję **Testuj połączenie**, aby przetestować połączenie.

    g. Wybierz opcję **Zapisz**, aby zapisać połączoną usługę. 
    
    ![Zapisywanie nowej połączonej usługi](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. W obszarze **Tabela** wybierz pozycję **[dbo].[emp]**. 

    ![Tabela](./media/tutorial-copy-data-portal/select-emp-table.png)
1. Przejdź do karty **Schemat** i wybierz pozycję **Importuj schemat**. 

    ![Wybieranie pozycji Importuj schemat](./media/tutorial-copy-data-portal/import-destination-schema.png)
1. Zaznacz kolumnę **ID**, a następnie wybierz pozycję **Usuń**. Kolumna **ID** jest kolumną tożsamości w bazie danych SQL, więc działanie kopiowania nie musi wstawiać danych do tej kolumny.

    ![Usuwanie kolumny ID](./media/tutorial-copy-data-portal/delete-id-column.png)
1. Przejdź do karty z potokiem i upewnij się, że w obszarze **Zestaw danych będący ujściem** wybrano pozycję **OutputSqlDataset**.

    ![Karta potoku](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        

### <a name="configure-mapping"></a>Konfigurowanie mapowania

Przejdź do karty **Mapowanie** w dolnej części okna **Właściwości** i wybierz pozycję **Importuj schematy**. Zwróć uwagę, że pierwsza i druga kolumna w pliku źródłowym są mapowane na pola **FirstName** i **LastName** w bazie danych SQL.

![Mapowanie schematów](./media/tutorial-copy-data-portal/map-schemas.png)

## <a name="validate-the-pipeline"></a>Weryfikowanie potoku
Wybierz na pasku narzędzi pozycję **Weryfikuj**, aby zweryfikować potok.
 
Kod JSON skojarzony z potokiem można wyświetlić, klikając pozycję **Kod**  w prawym górnym rogu.

## <a name="debug-and-publish-the-pipeline"></a>Debugowanie i publikowanie potoku
Przed opublikowaniem artefaktów (połączone usługi, zestawy danych i potok) w usłudze Data Factory lub własnym repozytorium Git usługi Azure Repos możesz debugować potok. 

1. Aby debugować potok, wybierz na pasku narzędzi pozycję **Debuguj**. Na karcie **Dane wyjściowe** w dolnej części okna wyświetlany jest stan uruchomienia potoku. 

1. Po pomyślnie uruchomić potok na górnym pasku narzędzi wybierz **Opublikuj wszystkie**. Ta akcja powoduje opublikowanie utworzonych jednostek (zestawy danych i potok) w usłudze Data Factory.

    ![Publikowanie](./media/tutorial-copy-data-portal/publish-button.png)

1. Poczekaj na wyświetlenie komunikatu **Pomyślnie opublikowano**. Aby wyświetlić komunikaty powiadomień, kliknij pozycję **Pokaż powiadomienia** w prawym górnym rogu (przycisk dzwonka). 

## <a name="trigger-the-pipeline-manually"></a>Ręczne wyzwalanie potoku
W tym kroku ręcznie wyzwolisz potok, który został opublikowany w poprzednim kroku. 

1. Wybierz pozycję **Wyzwól** na pasku narzędzi, a następnie wybierz pozycję **Wyzwól teraz**. Na stronie **Uruchomienie potoku** wybierz przycisk **Zakończ**.  

1. Przejdź do karty **Monitorowanie** po lewej stronie. Widoczne jest uruchomienie potoku, które zostało wyzwolone za pomocą wyzwalacza ręcznego. Za pomocą linków w kolumnie **Akcje** możesz wyświetlić szczegóły działań i ponownie uruchomić potok.

    ![Monitorowanie uruchomień potoku](./media/tutorial-copy-data-portal/monitor-pipeline.png)
1. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz link **Wyświetl uruchomienia działań** w kolumnie **Akcje**. W tym przykładzie istnieje tylko jedno działanie, dlatego na liście widnieje tylko jedna pozycja. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz link **Szczegóły** (ikona okularów) w kolumnie **Akcje**. Wybierz pozycję **Potoki** u góry ekranu, aby powrócić do widoku **Uruchomienia potoków**. Aby odświeżyć widok, wybierz pozycję **Odśwież**.

    ![Monitorowanie uruchomień działania](./media/tutorial-copy-data-portal/view-activity-runs.png)
1. Sprawdź, czy dodano jeszcze dwa wiersze do tabeli **emp** w bazie danych SQL. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Wyzwalanie potoku zgodnie z harmonogramem
W tym kroku utworzysz wyzwalacz harmonogramu potoku. Wyzwalacz uruchamia potok zgodnie z określonym harmonogramem, na przykład co godzinę lub codziennie. W tym przykładzie ustawisz wyzwalacz, aby uruchamiał się co minutę, aż do określonej daty/godziny zakończenia. 

1. Przejdź do karty **Tworzenie** po lewej stronie karty Monitorowanie. 

1. Przejdź do potoku, kliknij pozycję **Wyzwalacz** na pasku narzędzi, a następnie wybierz pozycję **Nowy/Edytuj**. 

1. W oknie **Dodawanie wyzwalaczy** wybierz pozycję **Wybierz wyzwalacz**, a następnie wybierz przycisk **+ Nowy**. 

    ![Przycisk Nowy](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
1. W oknie **Nowy wyzwalacz** wykonaj następujące czynności: 

    a. W obszarze **Nazwa** wprowadź wartość **RunEveryMinute**.

    b. W obszarze **Koniec** wybierz pozycję **W dniu**.

    c. W obszarze **Dzień zakończenia** wybierz listę rozwijaną.

    d. Wybierz opcję **Bieżący dzień**. Domyślnie dzień zakończenia jest ustawiony na następny dzień.

    e. Zaktualizuj część **Minuty** tak, aby była o kilka minut późniejsza od bieżącej godziny. Wyzwalacz zostanie aktywowany tylko w przypadku, gdy opublikujesz zmiany. Jeśli ustawisz tylko kilkuminutowy odstęp od bieżącej godziny i nie opublikujesz zmian do tego czasu, wyzwalacz się nie uruchomi.

    f. Wybierz przycisk **Zastosuj**. 

    ![Właściwości wyzwalacza](./media/tutorial-copy-data-portal/set-trigger-properties.png)

    g. Zaznacz opcję **Aktywowany**. Przy użyciu tego pola wyboru możesz dezaktywować wyzwalacz i aktywować go później.

    h. Wybierz opcję **Dalej**.

    ![Przycisk Aktywowany](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Za poszczególne uruchomienia potoku są naliczane opłaty, zatem określ stosowną datę zakończenia. 
1. Na stronie **Parametry uruchamiania wyzwalacza** zapoznaj się z ostrzeżeniem, a następnie wybierz przycisk **Zakończ**. Potok w tym przykładzie nie przyjmuje żadnych parametrów. 

    ![Parametry uruchomienia wyzwalacza](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)

1. Kliknij przycisk **Publikuj wszystko**, aby opublikować zmianę. 

1. Przejdź do karty **Monitorowanie** po lewej stronie, aby zobaczyć wyzwolone uruchomienia potoku. 

    ![Wyzwolone uruchomienia potoku](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
1. Aby przełączyć się z widoku **Uruchomienia potoku** do widoku **Uruchomienia wyzwalacza**, wybierz pozycję **Uruchomienia potoku**, a następnie pozycję **Uruchomienia wyzwalacza**.
    
    ![Uruchomienia wyzwalacza](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
1. Uruchomienia wyzwalacza znajdują się na liście. 

    ![Lista uruchomień wyzwalacza](./media/tutorial-copy-data-portal/trigger-runs-list.png)
1. Sprawdź, czy do tabeli **emp** są wstawiane dwa wiersze na minutę (dla każdego uruchomienia potoku), aż do określonego czasu zakończenia. 

## <a name="next-steps"></a>Kolejne kroki
Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w magazynie obiektów blob. W tym samouczku omówiono: 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku z działaniem kopiowania.
> * Testowe uruchamianie potoku.
> * Ręczne wyzwalanie potoku.
> * Wyzwalanie potoku zgodnie z harmonogramem.
> * Monitorowanie uruchomień potoku i działań.


Aby dowiedzieć się, jak kopiować dane ze środowiska lokalnego do chmury, przejdź do następującego samouczka: 

> [!div class="nextstepaction"]
>[Kopiowanie danych ze środowiska lokalnego do chmury](tutorial-hybrid-copy-portal.md)
